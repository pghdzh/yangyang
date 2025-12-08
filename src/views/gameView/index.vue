<template>
  <div class="flappy-root" role="application" aria-label="管道小鸟 - 秧秧 主题">
    <!-- 右上角固定操作栏（用于打开排行榜 / 难度选择） -->
    <div class="top-controls" aria-hidden="false">
      <select
        class="difficulty-select"
        v-model="difficulty"
        @change="applyDifficulty"
      >
        <option value="easy">简单</option>
        <option value="normal">普通</option>
        <option value="hard">困难</option>
      </select>

      <button
        class="open-ranking-btn"
        @click="openRankingDrawer"
        aria-haspopup="dialog"
      >
        榜单
      </button>
    </div>

    <!-- 主布局（canvas） -->
    <div class="game-layout">
      <section class="game-area" ref="containerRef" aria-label="游戏区">
        <canvas ref="canvasRef" class="game-canvas" tabindex="0"></canvas>

        <!-- 左上角得分 -->
        <div class="score-box" aria-hidden="true">
          <div class="label">得分</div>
          <div class="value">{{ score }}</div>
        </div>

        <!-- 中央大分数背景 -->
        <div class="center-score" aria-hidden="true">{{ score }}</div>

        <!-- 覆盖层：开始 / 结束（竖向按钮） -->
        <div class="overlay" v-if="state !== 'running'">
          <div class="overlay-inner" role="region" aria-live="polite">
            <p v-if="state === 'idle'" class="hint">
              点击或触摸开始 · 轻触以飞起
            </p>

            <div v-else-if="state === 'over'" class="end-block">
              <div class="overImg"><img :src="overImg" alt="" /></div>
              <p class="result">
                游戏结束 · 得分 <span class="end-score">{{ score }}</span>
              </p>

              <div class="submit-column">
                <input
                  v-model="name"
                  placeholder="输入昵称（最多12字）"
                  maxlength="12"
                  class="name-input"
                  v-show="score == bestScore"
                />
                <button
                  class="btn primary"
                  :disabled="submitting || !canSubmit"
                  @click="submitScore"
                  v-show="score == bestScore"
                >
                  {{ submitting ? "提交中..." : "提交分数并重新开始" }}
                </button>
                <button class="btn" @click="startOrRestart">重新开始</button>
                <p v-show="score < bestScore">
                  未达到个人最高分不显示上传按钮哦
                </p>
              </div>
            </div>
          </div>
        </div>
      </section>
    </div>

    <!-- 右侧抽屉（排行榜） -->
    <aside
      :class="['ranking-drawer', { open: rankingDrawerOpen }]"
      role="dialog"
      aria-modal="true"
      aria-label="排行榜抽屉"
    >
      <div class="drawer-header">
        <div class="drawer-title">鸣潮 · 秧秧小游戏 榜单</div>
        <div class="drawer-actions">
          <button class="btn small" @click="refreshRanking">刷新</button>
          <button class="btn small" @click="closeRankingDrawer">关闭</button>
        </div>
      </div>

      <div class="drawer-body">
        <ol class="ranking-list" v-if="rankingList.length">
          <li
            v-for="(item, idx) in rankingList"
            :key="item.id ?? idx"
            class="rank-item"
          >
            <div class="rank-left">
              <span class="rank-no">{{ idx + 1 }}</span>
              <span class="nick">{{ item.nickname || "匿名" }}</span>
            </div>
            <div class="rank-count">{{ item.count }}</div>
          </li>
        </ol>
        <div v-else class="empty">暂无榜单记录 / 拉取失败</div>
      </div>

      <div class="drawer-footer">
        <small>显示 top {{ pageSize }}</small>
      </div>
    </aside>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, nextTick, computed, watch } from "vue";
import {
  getRankingList,
  addRankingItem,
  updateRankingItem,
} from "@/api/modules/ranking";

const bgm = new Audio("/game/bgm.mp3");
bgm.loop = true;
bgm.volume = 0.4;

const sounds = {
  flap: new Audio("/game/flap.mp3"),
  hit: new Audio("/game/click.mp3"),
};

/** 可配置项与默认值 */
const CHARACTER_KEY = "yangyang_game";
const page = 1;
const pageSize = 99;

/** 游戏参数（可调） */
const GRAVITY = 0.5;
const FLAP_VEL = -8.0;
// PIPE_* 将被难度配置覆盖
const PIPE_WIDTH = 72;
let PIPE_GAP = 170; // 默认会被 difficulty 覆盖 (Normal)
let PIPE_SPEED = 2.6;
let PIPE_INTERVAL = 1500;
let PIPE_Score = 1; //得分
let overImg = ref("/game/over (1).png"); //结束图片
const BIRD_SIZE = 56; // 修改为 56
const COLLISION_RATIO = 0.38; // 碰撞半径系数（更宽松）

const MAX_ROT = Math.PI / 4;
const MIN_ROT = -Math.PI / 2.6;

/** ranking 类型 */
interface RankingItem {
  id?: number;
  nickname: string;
  count: number;
}
const rankingList = ref<RankingItem[]>([]);

/** 组件状态 */
const containerRef = ref<HTMLElement | null>(null);
const canvasRef = ref<HTMLCanvasElement | null>(null);
type State = "idle" | "running" | "over";
const state = ref<State>("idle");

const score = ref(0);
const bestScore = ref<number>(
  Number(localStorage.getItem("flappy_best") || "0")
);

const birdY = ref(0);
const birdX = ref(0);
const birdV = ref(0);
const birdRot = ref(0);
type Pipe = { x: number; gapY: number; passed: boolean };
const pipes = ref<Pipe[]>([]);

/** canvas */
let ctx: CanvasRenderingContext2D | null = null;
let dpr = 1;
let width = 360;
let height = 640;

/** timing */
let rafId = 0;
let lastTime = 0;
let spawnTimer = 0;

/** bird svg（秧秧风格） */
const defaultBirdSvg = encodeURIComponent(`
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64" role="img" aria-label="秧秧风格小肥啾">
  <defs>
    <filter id="drop" x="-50%" y="-50%" width="200%" height="200%">
      <feDropShadow dx="0" dy="2" stdDeviation="3" flood-color="#000" flood-opacity="0.18"/>
    </filter>
    <linearGradient id="ribbon" x1="0" x2="1" y1="0" y2="1">
      <stop offset="0" stop-color="#2e88c9"/>
      <stop offset="1" stop-color="#7fd3ff"/>
    </linearGradient>
  </defs>

  <g filter="url(#drop)">
    <ellipse cx="30" cy="34" rx="16" ry="13" fill="#ffffff"/>
    <path d="M18 26 q6 -8 24 -6 q-8 2 -24 8 z" fill="#0f3b62"/>
    <path d="M22 34 q6 -6 14 -4 q-8 4 -14 8 z" fill="#ffffff" stroke="#e9f5ff" stroke-width="0.7" stroke-linejoin="round"/>
    <path d="M14 38 q2 6 10 6 q2 -5 -10 -6 z" fill="#0f3b62" stroke="#2e88c9" stroke-width="0.6" />
    <path d="M36 33 l6 0 l-3 3 z" fill="#ffb36b" stroke="#e99b58" stroke-width="0.3" stroke-linejoin="round"/>
    <circle cx="38" cy="28" r="3" fill="#0a0a0a"/>
    <circle cx="39" cy="27" r="0.7" fill="#ffffff" opacity="0.95"/>
    <path d="M24 24 q10 -6 18 -4" stroke="url(#ribbon)" stroke-width="2.2" stroke-linecap="round" fill="none" opacity="0.95"/>
    <circle cx="31" cy="35.2" r="2" fill="#ff8da1" opacity="0.14"/>
    <ellipse cx="30" cy="34" rx="16" ry="13" fill="none" stroke="#e9f6ff" stroke-width="0.6" opacity="0.6"/>
  </g>
</svg>
`);
const birdImg = new Image();
let birdImgSrc = `data:image/svg+xml;utf8,${defaultBirdSvg}`;
birdImg.src = birdImgSrc;
birdImg.onload = () => {
  if (typeof draw === "function") draw();
};

/** ranking drawer 控制 */
const rankingDrawerOpen = ref(false);
let pausedByDrawer = false;

const name = ref("");
const submitting = ref(false);

const canSubmit = computed(
  () => !!(name.value && name.value.trim().length > 0)
);

/** 难度配置 */
const difficulty = ref<"easy" | "normal" | "hard">("normal");
const difficultyMap = {
  easy: { gap: 200, speed: 2.2, interval: 2000, scoreNum: 1 },
  normal: { gap: 170, speed: 2.6, interval: 1600, scoreNum: 2 },
  hard: { gap: 150, speed: 3.0, interval: 1200, scoreNum: 3 },
};

function applyDifficulty() {
  const cfg = difficultyMap[difficulty.value];
  PIPE_GAP = cfg.gap;
  PIPE_SPEED = cfg.speed;
  PIPE_INTERVAL = cfg.interval;
  PIPE_Score = cfg.scoreNum;
  // 可视提醒：短暂振动/提示（不做强制重置）
}

/** 立即应用初始难度 */
applyDifficulty();

/** 游戏逻辑 */
function resetGame() {
  score.value = 0;
  pipes.value = [];
  birdV.value = 0;
  birdRot.value = 0;
  birdX.value = width * 0.28;
  birdY.value = height / 2;
  spawnTimer = 0;
  spawnPipe();
  lastTime = performance.now();
}

function startOrRestart() {
  bgm.play();
  sounds.hit.play();
  resetGame();
  state.value = "running";
  lastTime = performance.now();
  cancelAnimationFrame(rafId);
  rafId = requestAnimationFrame(loop);
}

function spawnPipe() {
  const margin = 80;
  const gapY =
    margin + Math.random() * (height - margin * 2 - PIPE_GAP) + PIPE_GAP / 2;
  pipes.value.push({ x: width + 40, gapY, passed: false });
}

function flap() {
  if (state.value === "idle") {
    startOrRestart();
    return;
  }
  if (state.value !== "running") return;
  birdV.value = FLAP_VEL;
  birdRot.value = MAX_ROT;
  sounds.flap.currentTime = 0;
  sounds.flap.play();
}

const collisionRadius = () => BIRD_SIZE * COLLISION_RATIO;

function checkCollision(): boolean {
  const r = collisionRadius();
  const bx = birdX.value;
  const by = birdY.value;
  if (by - r <= 0 || by + r >= height) return true;
  for (const p of pipes.value) {
    const px = p.x;
    const gapTop = p.gapY - PIPE_GAP / 2;
    const gapBottom = p.gapY + PIPE_GAP / 2;
    if (bx + r > px && bx - r < px + PIPE_WIDTH) {
      if (by - r < gapTop || by + r > gapBottom) return true;
    }
  }
  return false;
}
function playVoice(name: string) {
  const audio = new Audio(`/game/${name}`);
  audio.play().catch((e) => console.warn("音频播放失败：", e));
}
function loop(now: number) {
  const dt = Math.min(40, now - lastTime);
  lastTime = now;

  birdV.value += GRAVITY * (dt / (1000 / 60));
  birdY.value += birdV.value * (dt / (1000 / 60));
  if (birdV.value > 0) {
    birdRot.value += (MIN_ROT - birdRot.value) * 0.06;
  } else {
    birdRot.value += (MAX_ROT - birdRot.value) * 0.12;
  }

  // 管道移动 & 得分
  for (let i = pipes.value.length - 1; i >= 0; i--) {
    pipes.value[i].x -= PIPE_SPEED * (dt / (1000 / 60));
    if (!pipes.value[i].passed && pipes.value[i].x + PIPE_WIDTH < birdX.value) {
      pipes.value[i].passed = true;
      score.value += PIPE_Score;
      if (score.value > bestScore.value) {
        bestScore.value = score.value;
        localStorage.setItem("flappy_best", String(bestScore.value));
      }
    }
    if (pipes.value[i].x + PIPE_WIDTH < -100) pipes.value.splice(i, 1);
  }

  spawnTimer += dt;
  if (spawnTimer >= PIPE_INTERVAL) {
    spawnTimer = 0;
    spawnPipe();
  }

  if (checkCollision()) {
    let textNum = Math.floor(Math.random() * 9);
    let overText = `audio (${textNum}).mp3`;
    playVoice(overText);
    bgm.pause();
    state.value = "over";
    let imgNum = Math.floor(Math.random() * 4) + 1;
    overImg.value = `/game/over (${imgNum}).png`;
    Math.random;
    cancelAnimationFrame(rafId);
    draw();
    return;
  }

  draw();
  rafId = requestAnimationFrame(loop);
}

/** 绘制 */
function draw() {
  if (!ctx) return;
  ctx.clearRect(0, 0, width, height);

  // 背景渐变
  const gBg = ctx.createLinearGradient(0, 0, 0, height);
  gBg.addColorStop(0, "#031127");
  gBg.addColorStop(1, "#07162a");
  ctx.fillStyle = gBg;
  ctx.fillRect(0, 0, width, height);

  // 柔光
  ctx.save();
  ctx.globalAlpha = 0.06;
  ctx.beginPath();
  ctx.ellipse(
    width * 0.18,
    height * 0.22,
    width * 0.32,
    height * 0.16,
    0,
    0,
    Math.PI * 2
  );
  ctx.fillStyle = "#2e88c9";
  ctx.fill();
  ctx.globalAlpha = 0.05;
  ctx.beginPath();
  ctx.ellipse(
    width * 0.82,
    height * 0.14,
    width * 0.18,
    height * 0.12,
    0,
    0,
    Math.PI * 2
  );
  ctx.fillStyle = "#7fd3ff";
  ctx.fill();
  ctx.restore();

  // 地面
  ctx.fillStyle = "#07162a";
  ctx.fillRect(0, height - 44, width, 44);

  // 管道
  for (const p of pipes.value) {
    const x = p.x;
    const gapTop = p.gapY - PIPE_GAP / 2;
    const gapBottom = p.gapY + PIPE_GAP / 2;

    const grad = ctx.createLinearGradient(x, 0, x + PIPE_WIDTH, 0);
    grad.addColorStop(0, "#022c45");
    grad.addColorStop(0.6, "#0f5f8f");
    grad.addColorStop(1, "#2e88c9");
    ctx.fillStyle = grad;

    ctx.fillRect(x, 0, PIPE_WIDTH, gapTop);
    ctx.fillRect(x, gapBottom, PIPE_WIDTH, height - gapBottom - 44);

    ctx.fillStyle = "rgba(255,255,255,0.06)";
    ctx.fillRect(x + 4, 0, 6, gapTop);
    ctx.fillRect(x + 4, gapBottom, 6, height - gapBottom - 44);
    ctx.fillStyle = "rgba(0,0,0,0.12)";
    ctx.fillRect(x + PIPE_WIDTH - 8, 0, 8, gapTop);
    ctx.fillRect(x + PIPE_WIDTH - 8, gapBottom, 8, height - gapBottom - 44);
  }

  // 小肥啾（绘制为正方形 BIRD_SIZE）
  const bx = birdX.value,
    by = birdY.value,
    drawSize = BIRD_SIZE;
  ctx.save();
  ctx.translate(bx, by);
  ctx.rotate(birdRot.value);
  ctx.drawImage(birdImg, -drawSize / 2, -drawSize / 2, drawSize, drawSize);
  ctx.restore();
}

/** 事件与尺寸 */
function onPointerDown(e: PointerEvent | MouseEvent | TouchEvent) {
  // 兼容事件对象
  const ev = e as Event;
  const target = ev.target as HTMLElement | null;

  // 如果点击的是表单控件（输入框/按钮/选择/可编辑区域），则不要 preventDefault 或触发 flap
  if (target) {
    // 若点击在 input/button/select/textarea 或可编辑元素内，直接放过
    if (target.closest("input, button, select, textarea, [contenteditable]")) {
      return;
    }
    // 若点击在排名抽屉 / modal / overlay 的交互区域，也放过（避免拦截其内部控件）
    if (
      target.closest(".ranking-drawer") ||
      target.closest(".ranking-modal") ||
      target.closest(".overlay-inner")
    ) {
      return;
    }
  }

  // 否则视为游戏点击（阻止浏览器默认行为并触发 flap）
  e.preventDefault();
  flap();
}

function handleResize() {
  const el = containerRef.value;
  const canvas = canvasRef.value;
  if (!el || !canvas) return;
  const rect = el.getBoundingClientRect();
  width = Math.max(300, Math.floor(rect.width));
  height = Math.max(420, Math.floor(rect.height));
  dpr = window.devicePixelRatio || 1;
  canvas.width = Math.floor(width * dpr);
  canvas.height = Math.floor(height * dpr);
  canvas.style.width = `${width}px`;
  canvas.style.height = `${height}px`;
  if (!ctx) ctx = canvas.getContext("2d");
  if (ctx) ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  birdX.value = width * 0.28;
  birdY.value = Math.min(Math.max(birdY.value || height / 2, 80), height - 120);
}

/** 生命周期 */
onMounted(() => {
  const canvas = canvasRef.value!;
  const container = containerRef.value!;
  ctx = canvas.getContext("2d");

  nextTick(() => {
    handleResize();
    resetGame();
    draw();
  });

  const pointerDown = (ev: PointerEvent) => onPointerDown(ev);
  canvas.addEventListener("pointerdown", pointerDown);
  container.addEventListener("pointerdown", pointerDown);
  window.addEventListener("resize", handleResize);
  const onKey = (ev: KeyboardEvent) => {
    if (ev.code === "Space") {
      ev.preventDefault();
      flap();
    }
  };
  window.addEventListener("keydown", onKey);

  container.style.touchAction = "none";

  onUnmounted(() => {
    cancelAnimationFrame(rafId);
    canvas.removeEventListener("pointerdown", pointerDown);
    container.removeEventListener("pointerdown", pointerDown);
    window.removeEventListener("resize", handleResize);
    window.removeEventListener("keydown", onKey);
  });
});

onUnmounted(() => {
  cancelAnimationFrame(rafId);
});

/** 排行榜相关 */
const fetchRanking = async () => {
  try {
    const res = await getRankingList({
      page,
      pageSize,
      character_key: CHARACTER_KEY,
    });
    if (res && res.success) {
      rankingList.value = res.data;
    } else {
      console.error("获取排行榜失败", res?.message);
      rankingList.value = [];
    }
  } catch (err) {
    console.error("获取排行榜异常", err);
    rankingList.value = [];
  }
};

const openRankingDrawer = async () => {
  // 如果正在运行，则暂停并记录状态
  if (state.value === "running") {
    pausedByDrawer = true;
    cancelAnimationFrame(rafId);
  } else {
    pausedByDrawer = false;
  }
  rankingDrawerOpen.value = true;
  await fetchRanking();
};

const closeRankingDrawer = () => {
  rankingDrawerOpen.value = false;
  if (pausedByDrawer && state.value !== "over") {
    // 恢复为运行状态（如果游戏不是结束态）
    state.value = "running";
    lastTime = performance.now();
    rafId = requestAnimationFrame(loop);
  }
};

const refreshRanking = async () => {
  await fetchRanking();
};

/** 提交分数 */
const submitScore = async () => {
  if (!canSubmit.value) return;
  submitting.value = true;

  try {
    const payload = {
      character_key: CHARACTER_KEY,
      nickname: name.value.trim(),
      count: score.value || 0,
    };

    // 防护：昵称为空不提交
    if (!payload.nickname) {
      console.warn("昵称为空，取消提交");
      return;
    }

    // 找到本地排行榜中同名且 count 最大的那一项（若有重复昵称）
    const existing = rankingList.value.reduce<RankingItem | null>(
      (acc, cur) => {
        if (cur.nickname === payload.nickname) {
          if (!acc || cur.count > acc.count) return cur;
        }
        return acc;
      },
      null
    );

    if (existing) {
      // 已存在同名记录
      if (payload.count > existing.count) {
        // 只在新分数更高时调用更新接口
        const res = await updateRankingItem(existing.id as number, {
          count: payload.count,
          // 你也可以在这里同时更新 nickname 或 character_key（若需要）
        });
        if (res && res.success) {
          await fetchRanking();
          rankingDrawerOpen.value = true;
        } else {
          console.error("更新排行榜失败", res?.message);
        }
      } else {
        // 新提交分数不高于已有分数 — 不做 update，可以直接打开抽屉或提示
        console.info("已有更高或相同分数，未更新排行榜");
        rankingDrawerOpen.value = true;
      }
    } else {
      // 不存在同名，直接新增
      const res = await addRankingItem(payload);
      if (res && res.success) {
        await fetchRanking();
        rankingDrawerOpen.value = true;
      } else {
        console.error("提交排行榜失败", res?.message);
      }
    }
  } catch (err) {
    console.error("提交排行榜异常", err);
  } finally {
    submitting.value = false;
    startOrRestart();
  }
};

/** 监听 difficulty 改变并应用（当用户手动选择时由 select 控制） */
watch(difficulty, () => applyDifficulty());
</script>

<style scoped lang="scss">
$bg-deep: #031127;
$deep-2: #07162a;
$accent-1: #2e88c9;
$accent-2: #7fd3ff;
$text-main: #f3fbff;

.flappy-root {
  width: 100%;
  min-height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  background: linear-gradient(180deg, $bg-deep 0%, $deep-2 100%);
  color: $text-main;
  font-family: "PingFang SC", "Noto Sans SC", system-ui, -apple-system,
    "Segoe UI";
  padding-top: 64px;

  /* 右上角操作 */
  .top-controls {
    position: fixed;
    top: 100px;
    right: 16px;
    z-index: 60;
    display: flex;
    gap: 8px;
    align-items: center;

    .difficulty-select {
      padding: 6px 8px;
      border-radius: 8px;
      border: none;
      background: #000;
      color: $text-main;
      font-weight: 700;
      cursor: pointer;
    }

    .open-ranking-btn {
      padding: 8px 12px;
      border-radius: 12px;
      border: none;
      background: linear-gradient(90deg, $accent-1, $accent-2);
      color: #042a36;
      font-weight: 800;
      cursor: pointer;
      box-shadow: 0 6px 18px rgba($accent-1, 0.08);
    }
  }

  .game-layout {
    flex: 1;
    display: flex;
    gap: 12px;
    width: 100%;
    box-sizing: border-box;
    padding: 12px;
  }

  .game-area {
    position: relative;
    flex: 1 1 auto;
    border-radius: 12px;
    overflow: hidden;
    background: linear-gradient(
      180deg,
      rgba(255, 255, 255, 0.02),
      rgba(0, 0, 0, 0.04)
    );
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.5);
    min-width: 280px;

    .game-canvas {
      width: 100%;
      height: 100%;
      display: block;
      touch-action: none;
      cursor: pointer;
      background: transparent;
    }

    .score-box {
      position: absolute;
      top: 12px;
      left: 12px;
      z-index: 6;
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      gap: 4px;
      pointer-events: none;
      .label {
        font-size: 12px;
        color: rgba($text-main, 0.85);
      }
      .value {
        font-size: 18px;
        font-weight: 800;
        color: $accent-1;
        text-shadow: 0 6px 18px rgba($accent-1, 0.06);
      }
    }

    .center-score {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      pointer-events: none;
      font-size: 96px;
      font-weight: 900;
      color: rgba($text-main, 0.06);
      text-shadow: 0 8px 24px rgba($accent-1, 0.04);
      z-index: 3;
    }

    .overlay {
      position: absolute;
      inset: 0;
      z-index: 8;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(
        180deg,
        rgba(2, 6, 12, 0.28),
        rgba(2, 6, 12, 0.52)
      );
      .overlay-inner {
        width: min(640px, 94%);
        padding: 18px;
        border-radius: 12px;
        text-align: center;
        color: $text-main;
        .title {
          font-size: 20px;
          font-weight: 900;
          color: $accent-2;
          margin-bottom: 8px;
        }
        .hint {
          font-size: 14px;
          color: rgba($text-main, 0.88);
        }

        .end-block {
          margin-bottom: 200px;
          .overImg {
            width: 120px;
            height: 120px;
            margin: 0 auto;

            img {
              width: 100%;
            }
          }
          .result {
            font-size: 15px;
            color: rgba($text-main, 0.94);
            margin-bottom: 8px;
          }
          .end-score {
            font-weight: 900;
            color: $accent-1;
            margin-left: 6px;
          }

          /* 竖向按钮组 */
          .submit-column {
            display: flex;
            flex-direction: column;
            gap: 8px;
            align-items: center;
            margin-top: 8px;
            .name-input {
              padding: 8px 10px;
              border-radius: 10px;
              border: 1px solid rgba($text-main, 0.06);
              background: rgba(255, 255, 255, 0.02);
              color: $text-main;
              min-width: 200px;
              outline: none;
              z-index: 99;
            }
            .btn {
              padding: 10px 14px;
              border-radius: 10px;
              border: none;
              font-weight: 700;
              cursor: pointer;
              background: rgba(255, 255, 255, 0.03);
              color: $text-main;
              width: 200px;
              text-align: center;
            }
            .primary {
              background: linear-gradient(90deg, $accent-1, $accent-2);
              color: #062230;
            }
            .ghost {
              background: transparent;
              border: 1px solid rgba($text-main, 0.04);
            }
            .outline {
              background: transparent;
              border: 1px solid rgba($text-main, 0.06);
              color: $text-main;
            }
          }
        }
      }
    }
  }

  .footer-note {
    margin-top: 8px;
    font-size: 12px;
    color: rgba($text-main, 0.6);
    text-align: center;
  }

  /* 右侧抽屉样式 */
  .ranking-drawer {
    position: fixed;
    top: 64px; /* 不覆盖顶部导航 */
    right: 0;
    height: calc(100vh - 64px);
    width: 0;
    max-width: 420px;
    overflow: hidden;
    z-index: 80;
    transition: width 260ms ease, box-shadow 260ms ease;
    box-shadow: none;
    display: flex;
    flex-direction: column;
    background: linear-gradient(
      180deg,
      rgba(255, 255, 255, 0.02),
      rgba(255, 255, 255, 0.01)
    );

    &.open {
      width: min(420px, 92%);
      box-shadow: -20px 0 40px rgba(0, 0, 0, 0.6);
    }

    .drawer-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 12px 14px;
      border-bottom: 1px solid rgba($text-main, 0.03);
      .drawer-title {
        font-weight: 900;
        color: $accent-2;
      }
      .drawer-actions {
        display: flex;
        gap: 8px;
        .btn {
          padding: 6px 8px;
          font-size: 12px;
        }
      }
    }

    .drawer-body {
      padding: 12px 14px;
      overflow: auto;
      flex: 1;
      .ranking-list {
        list-style: none;
        margin: 0;
        padding: 0;
        li {
          display: flex;
          justify-content: space-between;
          align-items: center;
          padding: 10px 6px;
          border-bottom: 1px dashed rgba(255, 255, 255, 0.03);
          .rank-left {
            display: flex;
            align-items: center;
            gap: 8px;
            .rank-no {
              width: 28px;
              font-weight: 900;
              color: $accent-1;
            }
            .nick {
              color: $text-main;
              font-weight: 700;
            }
          }
          .rank-count {
            color: $accent-2;
            font-weight: 900;
            width: 64px;
            text-align: right;
          }
        }
      }
      .empty {
        padding: 16px;
        text-align: center;
        color: rgba($text-main, 0.6);
      }
    }

    .drawer-footer {
      padding: 10px 14px;
      text-align: center;
      font-size: 12px;
      color: rgba($text-main, 0.6);
      border-top: 1px solid rgba($text-main, 0.03);
    }
  }

  /* 响应式：小屏时抽屉覆盖式显示 */
  @media (max-width: 900px) {
    .top-controls {
      top: 100px;
      right: 8px;
    }
    .ranking-drawer {
      top: 64px;
      &.open {
        width: 100%;
        left: 0;
        right: 0;
      }
    }
    .game-area {
      min-height: 420px;
    }
    .center-score {
      font-size: 48px;
    }
  }
}
</style>
