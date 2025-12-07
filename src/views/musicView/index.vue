<template>
  <section
    class="cantarella-player"
    @keydown.space.prevent="onSpace"
    tabindex="0"
    ref="rootEl"
    aria-label="秧秧 音乐播放器"
  >
    <div class="stage">
      <!-- 左侧：封面与控制 -->
      <div class="left" role="region" aria-label="播放器控制区">
        <div class="cover" :style="coverStyle">
          <!-- video 作为纯装饰性背景，屏读器隐藏 -->
          <video
            v-if="videoSrc"
            class="video-background"
            :src="videoSrc"
            autoplay
            muted
            loop
            playsinline
            aria-hidden="true"
            tabindex="-1"
            :class="videoClass"
          ></video>

          <!-- 加载遮罩 -->
          <div v-if="loadingAudio" class="loading-overlay" aria-hidden="true">
            <div class="spinner" />
            <div class="loading-text">加载中…</div>
          </div>
        </div>

        <div class="controls">
          <div class="title" :title="current?.title || '未选择曲目'">
            {{ current?.title || "未选择曲目" }}
          </div>

          <div class="meta">
            <span class="time">{{ formatTime(currentTime) }}</span>
            <span class="divider">/</span>
            <span class="time">{{ formatTime(duration) }}</span>
          </div>

          <!-- 进度条（Pointer 支持）-->
          <div
            class="progress-wrap"
            ref="progressWrap"
            @click="seekByClick"
            @pointerdown.prevent="onPointerDownProgress"
            role="slider"
            :aria-valuemin="0"
            :aria-valuemax="duration"
            :aria-valuenow="currentTime"
            aria-label="进度条"
          >
            <div class="progress-bar">
              <div
                class="progress"
                :style="{ width: progressPercent + '%' }"
              ></div>
            </div>
            <div
              class="progress-handle"
              :style="{ left: progressPercent + '%' }"
              aria-hidden="true"
            ></div>
          </div>

          <!-- 控件行 -->
          <div class="btns">
            <button class="icon" @click="prev" aria-label="上一首">⟵</button>

            <button
              class="play"
              @click="togglePlay"
              :aria-pressed="playing"
              :aria-label="playing ? '暂停' : '播放'"
            >
              <span v-if="!playing">▶</span>
              <span v-else>▌▌</span>
            </button>

            <button class="icon" @click="next" aria-label="下一首">⟶</button>

            <div class="modes" role="group" aria-label="播放模式">
              <button
                :class="{ active: shuffle }"
                @click="toggleShuffle"
                title="随机播放"
              >
                🔀
              </button>
              <button
                :class="{ active: repeatMode !== 'off' }"
                @click="toggleRepeat"
                title="循环模式"
              >
                🔁
              </button>
            </div>

            <div class="volume" aria-label="音量控制">
              <input
                type="range"
                min="0"
                max="1"
                step="0.01"
                v-model.number="volume"
                aria-label="音量"
              />
            </div>
          </div>

          <div v-if="errorMessage" class="error-msg" role="status">
            {{ errorMessage }}
          </div>
        </div>
      </div>

      <!-- 右侧：播放列表 -->
      <div
        class="right"
        :class="{ collapsed: !playlistOpen && isMobile }"
        role="region"
        aria-label="播放列表"
      >
        <div class="playlist-header">
          <div class="left-head">
            <h3>播放列表</h3>
            <!-- 文本展开/收起按钮（替换文件夹图标） -->
            <button
              class="toggle-list-text"
              @click="togglePlaylist"
              :title="playlistOpen ? '收起播放列表' : '展开播放列表'"
            >
              {{ playlistOpen ? "收起" : "展开" }}
            </button>
            <div class="api-hint">
              {{ loading ? "加载中…" : list.length ? "" : "目录为空" }}
            </div>
          </div>

          <div class="search-wrap">
            <input
              v-model="searchTerm"
              @input="onSearchInput"
              placeholder="搜索曲名..."
              aria-label="搜索曲目"
            />
            <button
              v-if="searchTerm"
              class="clear"
              @click="clearSearch"
              aria-label="清除搜索"
            >
              ✕
            </button>
          </div>
        </div>

        <div class="list-area">
          <div v-if="loading" class="list-loading">
            <div class="small-spinner" />
            加载目录...
          </div>

          <ul class="playlist" role="list">
            <li
              v-for="(item, idx) in filteredList"
              :key="item.name || idx"
              :class="{ active: idx === index }"
              @click="selectTrack(idx)"
              tabindex="0"
              @keyup.enter="selectTrack(idx)"
              role="listitem"
              :aria-current="idx === index ? 'true' : 'false'"
            >
              <div class="left-col">
                <div class="dot" aria-hidden="true"></div>
                <!-- 尽量显示完整名称：允许换行，窄屏时限制为两行 -->
                <div class="title" :title="item.title">{{ item.title }}</div>
              </div>
              <div class="right-col">
                <div class="len">
                  {{ item.duration ? formatTime(item.duration) : "--:--" }}
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <!-- audio 元素 -->
    <audio
      ref="audioRef"
      @timeupdate="onTimeUpdate"
      @ended="onEnded"
      @loadedmetadata="onLoadedMetadata"
      @error="onAudioError"
      preload="metadata"
    ></audio>
  </section>
</template>

<script setup lang="ts">
import {
  ref,
  computed,
  onMounted,
  onBeforeUnmount,
  watch,
  nextTick,
} from "vue";
import { getMusicList, getMusicUrl } from "@/api/modules/music"; // 请确认路径

type MusicItem = {
  name: string;
  title: string;
  url?: string;
  duration?: number | null;
};

const list = ref<MusicItem[]>([]);
const loading = ref(false);
const index = ref<number>(-1);
const playing = ref(false);
const audioRef = ref<HTMLAudioElement | null>(null);
const currentTime = ref<number>(0);
const duration = ref<number>(0);
const volume = ref<number>(
  Number(localStorage.getItem("cantarella_volume") ?? 0.8)
);
const shuffle = ref<boolean>(false);
const repeatMode = ref<"off" | "one" | "all">("off");

const rootEl = ref<HTMLElement | null>(null);
const progressWrap = ref<HTMLElement | null>(null);
const dragging = ref(false);
const playlistOpen = ref(true);
const errorMessage = ref<string | null>(null);
const loadingAudio = ref(false);

// 根据窗口宽度判断移动端
const isMobile = ref<boolean>(window.innerWidth <= 920);
window.addEventListener("resize", () => {
  isMobile.value = window.innerWidth <= 920;
});

// 随机选择视频源（mp1/mp2 里）
const videoSrc = ref("");
const videoClass = ref(""); // "landscape" or "portrait"

// 搜索相关
const searchTerm = ref("");
let searchTimer: any = null;
const searchDebounceMs = 240;

// 当前曲目与进度计算
const current = computed(() =>
  index.value >= 0 && list.value[index.value] ? list.value[index.value] : null
);
const progressPercent = computed(() =>
  duration.value
    ? Math.min(100, Math.max(0, (currentTime.value / duration.value) * 100))
    : 0
);

// 封面渐变（秧秧风格）
const coverStyle = computed(() => {
  const t = current.value?.title || "cantarella";
  let hash = 0;
  for (let i = 0; i < t.length; i++)
    hash = (hash << 5) - hash + t.charCodeAt(i);
  const r1 = (Math.abs(hash) % 120) + 40;
  const r2 = (Math.abs(hash * 3) % 120) + 40;
  return {
    background: `radial-gradient(circle at 28% 28%, rgba(95,224,255,0.06), transparent 12%), linear-gradient(135deg, rgba(${r1},${r2},255,0.08), rgba(111,92,230,0.08))`,
  };
});

// 过滤后的列表（基于搜索）
const filteredList = computed(() => {
  const term = (searchTerm.value || "").trim().toLowerCase();
  if (!term) return list.value;
  return list.value.filter((i) => (i.title || "").toLowerCase().includes(term));
});

// 获取列表
async function fetchList() {
  loading.value = true;
  try {
    const res = await getMusicList();
    const items =
      res?.ok && Array.isArray(res.list)
        ? res.list
        : Array.isArray(res)
        ? res
        : res?.list ?? [];
    list.value = items.map((it: any) => ({
      name: it.name,
      title: it.title ?? (it.name ? it.name.replace(/\.mp3$/i, "") : "未知"),
      url: getMusicUrl(it.name),
      duration: null,
    }));
    // // 自动选中第一首不自动播放
    // if (list.value.length > 0) {
    //    index.value = 0;
    //    await loadCurrent(false);
    // }
  } catch (e) {
    console.error("获取音乐列表失败", e);
    list.value = [];
    errorMessage.value = "加载目录失败";
  } finally {
    loading.value = false;
  }
}

// 设置并预检音源
async function safeSetSrc(url: string) {
  const a = audioRef.value!;
  errorMessage.value = null;
  loadingAudio.value = true;
  try {
    try {
      const head = await fetch(url, { method: "HEAD" });
      if (!head.ok) throw new Error(`资源响应 ${head.status}`);
      const ct = head.headers.get("content-type") || "";
      if (!ct.includes("audio")) {
        console.warn("content-type 不是 audio:", ct);
      }
    } catch (e) {
      // 忽略 HEAD 失败
    }
    a.src = url;
    a.load();
  } catch (err) {
    console.error("设置音源失败", err);
    errorMessage.value = "无法加载音频资源";
    throw err;
  } finally {
    // loadingAudio 会在 loadedmetadata 或 error 中被关闭
  }
}

async function loadCurrent(doPlay = false) {
  const a = audioRef.value;
  const curr = current.value;
  if (!a || !curr) return;
  a.pause();
  duration.value = 0;
  currentTime.value = 0;
  try {
    await safeSetSrc(curr.url || getMusicUrl(curr.name));
    if (doPlay) await play();
  } catch {
    playing.value = false;
    loadingAudio.value = false;
  }
}

async function play() {
  const a = audioRef.value;
  if (!a) return;
  try {
    await a.play();
    playing.value = true;
    errorMessage.value = null;
  } catch (e: any) {
    console.warn("播放失败", e);
    playing.value = false;
    errorMessage.value = "播放被浏览器阻止或资源不可用";
  }
}
function pause() {
  audioRef.value?.pause();
  playing.value = false;
}
function togglePlay() {
  if (!audioRef.value) return;
  if (playing.value) pause();
  else play();
}

function selectTrack(i: number) {
  if (i < 0 || i >= list.value.length) return;
  index.value = i;
  loadCurrent(true);
}

// 音频事件
function onTimeUpdate(e: Event) {
  const t = e.target as HTMLAudioElement;
  currentTime.value = t.currentTime || 0;
}
function onLoadedMetadata(e: Event) {
  const t = e.target as HTMLAudioElement;
  duration.value = isFinite(t.duration) ? t.duration : 0;
  if (current.value && !current.value.duration)
    current.value.duration = duration.value;
  loadingAudio.value = false;
}
function onEnded() {
  loadingAudio.value = false;
  if (repeatMode.value === "one") {
    if (audioRef.value) {
      audioRef.value.currentTime = 0;
      play();
    }
    return;
  }
  if (shuffle.value) {
    playRandom();
    return;
  }
  if (index.value < list.value.length - 1) selectTrack(index.value + 1);
  else {
    if (repeatMode.value === "all") selectTrack(0);
    else playing.value = false;
  }
}
function onAudioError(e: Event) {
  const a = audioRef.value;
  console.error("audio error", a?.error);
  errorMessage.value = "音频播放出错";
  playing.value = false;
  loadingAudio.value = false;
}

// 上一/下一/随机
function next() {
  if (!list.value.length) return;
  if (shuffle.value) {
    playRandom();
    return;
  }
  if (index.value < list.value.length - 1) selectTrack(index.value + 1);
  else if (repeatMode.value === "all") selectTrack(0);
}
function prev() {
  if (!audioRef.value) return;
  if (audioRef.value.currentTime > 4) {
    audioRef.value.currentTime = 0;
    return;
  }
  if (index.value > 0) selectTrack(index.value - 1);
  else if (repeatMode.value === "all") selectTrack(list.value.length - 1);
}
function playRandom() {
  if (!list.value.length) return;
  if (list.value.length === 1) {
    selectTrack(0);
    return;
  }
  let i = index.value;
  while (i === index.value) i = Math.floor(Math.random() * list.value.length);
  selectTrack(i);
}

// 进度条：点击 & 拖拽
function seekByClick(e: MouseEvent | TouchEvent) {
  if (!progressWrap.value || !duration.value || !audioRef.value) return;
  const rect = progressWrap.value.getBoundingClientRect();
  const clientX =
    (e as MouseEvent).clientX ?? (e as TouchEvent).touches?.[0]?.clientX;
  if (clientX == null) return;
  const x = Math.min(Math.max(0, clientX - rect.left), rect.width);
  const ratio = x / rect.width;
  audioRef.value.currentTime = ratio * duration.value;
  currentTime.value = audioRef.value.currentTime;
}
function onPointerDownProgress(e: PointerEvent) {
  if (!progressWrap.value || !audioRef.value || !duration.value) return;
  dragging.value = true;
  (e.target as Element).setPointerCapture?.(e.pointerId);
  window.addEventListener("pointermove", onPointerMoveProgress);
  window.addEventListener("pointerup", onPointerUpProgress);
  handlePointer(e);
}
function onPointerMoveProgress(e: PointerEvent) {
  handlePointer(e);
}
function onPointerUpProgress(e: PointerEvent) {
  dragging.value = false;
  window.removeEventListener("pointermove", onPointerMoveProgress);
  window.removeEventListener("pointerup", onPointerUpProgress);
}
function handlePointer(e: PointerEvent) {
  if (!progressWrap.value || !audioRef.value || !duration.value) return;
  const rect = progressWrap.value.getBoundingClientRect();
  const x = Math.min(Math.max(0, e.clientX - rect.left), rect.width);
  const ratio = x / rect.width;
  audioRef.value.currentTime = ratio * duration.value;
  currentTime.value = audioRef.value.currentTime;
}

// 音量持久化
watch(volume, (v) => {
  if (audioRef.value) audioRef.value.volume = v;
  localStorage.setItem("cantarella_volume", String(v));
});

// 模式切换
function toggleShuffle() {
  shuffle.value = !shuffle.value;
}
function toggleRepeat() {
  if (repeatMode.value === "off") repeatMode.value = "all";
  else if (repeatMode.value === "all") repeatMode.value = "one";
  else repeatMode.value = "off";
}

// 键盘空格
function onSpace() {
  if (
    document.activeElement &&
    ["INPUT", "TEXTAREA"].includes(document.activeElement.tagName)
  )
    return;
  togglePlay();
}

// 切换播放列表（文字）
function togglePlaylist() {
  playlistOpen.value = !playlistOpen.value;
  if (playlistOpen.value)
    nextTick(() => {
      const el = document.querySelector(".playlist li.active");
      el?.scrollIntoView({ block: "center", behavior: "smooth" });
    });
}

// 搜索防抖
function onSearchInput() {
  if (searchTimer) clearTimeout(searchTimer);
  searchTimer = setTimeout(() => {
    // 过滤由 computed filteredList 完成
    searchTimer = null;
  }, searchDebounceMs);
}
function clearSearch() {
  searchTerm.value = "";
}

// 格式化时间
function formatTime(sec?: number) {
  if (!sec || !isFinite(sec)) return "--:--";
  const s = Math.floor(sec % 60);
  const m = Math.floor(sec / 60);
  return `${String(m).padStart(2, "0")}:${String(s).padStart(2, "0")}`;
}

// 生命周期：绑定 audio ref、初始化列表和 videoSrc
onMounted(async () => {
  audioRef.value =
    (document.querySelector(".cantarella-player audio") as HTMLAudioElement) ??
    null;
  if (audioRef.value) audioRef.value.volume = volume.value;

  // 设置 videoSrc 与 class：桌面优先横屏(mp1), 移动优先竖屏(mp2)
  const isM = isMobile.value;
  const folder = isM ? "/mp1" : "/mp2";
  // 随机选择 1..4，如果没有资源请保证路径存在
  // const idx = Math.floor(Math.random() * 4) + 1;
  videoSrc.value = `${folder}/1 (1).mp4`;
  videoClass.value = isM ? "landscape" : "portrait";

  await fetchList();

  window.addEventListener("keydown", globalKeydown);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", globalKeydown);
});

// 全局键盘
function globalKeydown(e: KeyboardEvent) {
  if (e.code === "Space") {
    if (
      document.activeElement &&
      ["INPUT", "TEXTAREA"].includes(document.activeElement.tagName)
    )
      return;
    e.preventDefault();
    togglePlay();
  } else if (e.code === "Escape") {
    pause();
  }
}
</script>

<style scoped lang="scss">
$bg-deep: #031127; // 深海夜色底
$deep-2: #07162a; // 次深底用于渐变
$accent-1: #2e88c9; // 暗紫主光（冷雅）
$accent-2: #7fd3ff; // 冷海蓝高光（湿光感）

$blood: #ff6b85;
$text: #f3fbff;
$glass: rgba(95, 224, 255, 0.04);
$shadow: rgba(1, 2, 6, 0.6);

/* 整体 */
.cantarella-player {
  padding: 20px;
  min-height: 100vh;
  background: radial-gradient(
      800px 220px at 18% 10%,
      rgba($accent-1, 0.02),
      transparent 8%
    ),
    linear-gradient(180deg, $deep-2 0%, $bg-deep 100%);
  color: $text;
  padding-top: 80px;
  font-family: "Noto Sans SC", "Inter", system-ui, -apple-system, "Segoe UI",
    Roboto, Arial;
  outline: none;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* 五线谱微纹理 */
.cantarella-player::before {
  content: "";
  position: fixed;
  left: 0;
  top: 100px;
  width: 100%;
  height: 200px;
  background-image: repeating-linear-gradient(
    to bottom,
    rgba(255, 255, 255, 0.01) 0px,
    rgba(255, 255, 255, 0.01) 1px,
    transparent 1px,
    transparent 18px
  );
  opacity: 0.04;
  pointer-events: none;
  mix-blend-mode: overlay;
}

/* 主区域布局 */
.stage {
  display: flex;
  gap: 18px;
  max-width: 1100px;
  margin: 0 auto;
  align-items: flex-start;
}

/* 左侧 */
.left {
  width: 420px;
  background: linear-gradient(180deg, rgba(6, 6, 8, 0.34), rgba(4, 4, 6, 0.18));
  border: 1px solid $glass;
  border-radius: 12px;
  padding: 14px;
  box-shadow: 0 18px 56px $shadow;
  position: relative;
  min-height: 380px;
}

/* cover 区 */
.cover {
  width: 100%;
  height: 560px;
  border-radius: 10px;
  background: linear-gradient(
    135deg,
    rgba($accent-2, 0.12),
    rgba($accent-1, 0.06)
  );
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  position: relative;
  box-shadow: inset 0 -6px 28px rgba(0, 0, 0, 0.45);
}

/* 视频比例控制：横向/纵向 */
.video-background {
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0.9;
  transform: scale(1.02);
  filter: saturate(0.92) contrast(0.95) blur(0.2px);
}
.video-background.landscape {
  aspect-ratio: 16 / 9;
}
.video-background.portrait {
  aspect-ratio: 9 / 16;
  width: auto;
  height: 110%;
}

/* 加载遮罩 */
.loading-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, rgba(0, 0, 0, 0.36), rgba(0, 0, 0, 0.28));
  z-index: 8;
}
.spinner,
.small-spinner {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: 4px solid rgba(255, 255, 255, 0.06);
  border-top-color: rgba($accent-1, 0.9);
  animation: spin 1s linear infinite;
}
.small-spinner {
  width: 18px;
  height: 18px;
  border-width: 3px;
  border-top-color: rgba($accent-2, 0.9);
  margin-right: 8px;
}
.loading-text {
  margin-top: 8px;
  color: rgba($text, 0.9);
  font-weight: 700;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* 控件 */
.controls {
  margin-top: 12px;
}
.title {
  font-size: 1.05rem;
  font-weight: 900;
  color: $accent-1;
  letter-spacing: 0.2px;
}
.meta {
  margin-top: 6px;
  font-size: 0.95rem;
  color: rgba($text, 0.86);
  display: flex;
  gap: 8px;
  align-items: center;
}

/* 进度条 */
.progress-wrap {
  margin-top: 12px;
  cursor: pointer;
  touch-action: none;
}
.progress-bar {
  height: 10px;
  background: rgba(255, 255, 255, 0.03);
  border-radius: 10px;
  overflow: hidden;
  position: relative;
}
.progress {
  height: 100%;
  width: 0%;
  background: linear-gradient(90deg, $accent-2, $accent-1);
  box-shadow: 0 6px 18px rgba($accent-1, 0.06) inset;
  transition: width 120ms linear;
}
.progress-handle {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: $accent-1;
  transform: translateX(-50%);
  position: relative;
  top: -3px;
  box-shadow: 0 8px 26px rgba($accent-1, 0.14);
}

/* 按钮行 */
.btns {
  margin-top: 12px;
  display: flex;
  align-items: center;
  gap: 10px;
  flex-wrap: wrap;
}
.icon,
.play {
  border: none;
  background: transparent;
  color: $text;
  font-size: 18px;
  cursor: pointer;
  padding: 8px;
  border-radius: 8px;
  transition: transform 160ms, background 160ms;
}
.play {
  font-size: 20px;
  background: linear-gradient(
    90deg,
    rgba($accent-2, 0.12),
    rgba($accent-1, 0.06)
  );
  padding: 8px 12px;
  border-radius: 12px;
  box-shadow: 0 8px 28px rgba(0, 0, 0, 0.5);
}
.icon:hover,
.play:hover {
  transform: translateY(-4px);
}
.modes button {
  margin-right: 8px;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.04);
  padding: 6px 8px;
  border-radius: 8px;
  cursor: pointer;
}
.modes .active {
  background: linear-gradient(
    90deg,
    rgba($accent-1, 0.06),
    rgba($accent-2, 0.04)
  );
  border-color: rgba($accent-1, 0.12);
}
.volume input[type="range"] {
  width: 120px;
}
.toggle-list-text {
  padding: 8px 10px;
  border-radius: 8px;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.03);
  color: rgba($text, 0.95);
  cursor: pointer;
  display: none;
}

/* 错误 */
.error-msg {
  margin-top: 10px;
  color: $blood;
  font-weight: 700;
  font-size: 0.9rem;
}

/* 右侧列表 */
.right {
  flex: 1;
  max-height: 68vh;
  overflow: hidden;
  border-radius: 12px;
  background: linear-gradient(180deg, rgba(6, 6, 8, 0.22), rgba(4, 4, 6, 0.12));
  border: 1px solid rgba(255, 255, 255, 0.02);
  padding: 12px;
  box-shadow: 0 12px 36px rgba(0, 0, 0, 0.45);
  display: flex;
  flex-direction: column;
  transition: max-height 260ms ease;
}

/* 折叠态 */
.right.collapsed {
  max-height: 64px;
}

/* header */
.playlist-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  gap: 12px;
}
.left-head {
  display: flex;
  gap: 12px;
  align-items: center;
}
.playlist-header h3 {
  margin: 0;
  color: $accent-2;
  font-weight: 900;
}
.api-hint {
  color: rgba($text, 0.6);
  font-size: 0.9rem;
}

/* 搜索 */
.search-wrap {
  display: flex;
  align-items: center;
  gap: 8px;
}
.search-wrap input {
  padding: 8px 10px;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.04);
  background: linear-gradient(180deg, rgba(8, 4, 8, 0.48), rgba(6, 2, 6, 0.44));
  color: $text;
  width: 220px;
}
.search-wrap .clear {
  background: transparent;
  border: none;
  color: rgba($text, 0.7);
  cursor: pointer;
}

/* 列表区域 */
.list-area {
  position: relative;
  flex: 1;
  overflow: hidden;
}
.list-loading {
  display: flex;
  align-items: center;
  gap: 8px;
  color: rgba($text, 0.7);
  padding: 10px 0;
}

/* 列表 */
.playlist {
  list-style: none;
  padding: 0;
  margin: 0;
  overflow-y: auto;
  max-height: calc(68vh - 52px);
  display: block;
}
.playlist li {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 12px;
  align-items: center;
  padding: 10px;
  border-radius: 10px;
  cursor: pointer;
  transition: background 160ms, transform 160ms;
}
.playlist li:hover {
  transform: translateX(6px);
  background: linear-gradient(
    90deg,
    rgba($accent-1, 0.03),
    rgba($accent-2, 0.02)
  );
}
.playlist li.active {
  background: linear-gradient(
    90deg,
    rgba($accent-1, 0.06),
    rgba($accent-2, 0.04)
  );
  box-shadow: 0 8px 28px rgba($accent-1, 0.04) inset;
}

/* 左列 */
.left-col {
  display: flex;
  gap: 12px;
  align-items: center;
  min-width: 0;
}
.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: linear-gradient(90deg, $accent-2, $accent-1);
  box-shadow: 0 6px 18px rgba($accent-1, 0.06);
  flex: 0 0 auto;
}
.title {
  font-weight: 700;
  color: $text;
  overflow-wrap: anywhere; /* 允许换行显示 */
  max-width: 100%;
}

/* 对窄屏尽量显示两行 */
.title {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 右列时长 */
.len {
  color: rgba($text, 0.6);
  font-weight: 700;
  min-width: 48px;
  text-align: right;
}

@media (max-width: 920px) {
  .toggle-list-text {
    display: block;
  }
  .stage {
    flex-direction: column;
  }
  .left {
    width: 100%;
    min-height: auto;
  }
  .cover {
    height: 200px;
  }
  .right {
    width: 100%;
    max-height: none;
    order: 3;
  }

  .mini-left {
    display: flex;
    gap: 8px;
    align-items: center;
  }
  .mini-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: linear-gradient(90deg, $accent-2, $accent-1);
  }
  .mini-title {
    font-weight: 700;
    font-size: 0.95rem;
    max-width: 180px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .mini-right .play {
    width: 44px;
    height: 44px;
    display: inline-grid;
    place-items: center;
    font-size: 16px;
    padding: 0;
    border-radius: 8px;
  }
}

/* 小屏微调 */
@media (max-width: 520px) {
  .search-wrap input {
    width: 140px;
  }

  .progress-handle {
    width: 14px;
    height: 14px;
  }
}
</style>
