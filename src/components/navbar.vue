<template>
  <header class="app-header">
    <h1 class="title">秧秧电子设定集</h1>
    <!-- 在线人数展示 -->
    <div class="online-count" v-if="onlineCount !== null">
      当前在线：<span class="count">{{ onlineCount }}人</span>
    </div>
    <!-- 移动端汉堡按钮 -->
    <button
      class="hamburger"
      @click="toggleMobileNav"
      aria-label="Toggle navigation"
    >
      <span :class="{ open: mobileNavOpen }"></span>
      <span :class="{ open: mobileNavOpen }"></span>
      <span :class="{ open: mobileNavOpen }"></span>
    </button>

    <!-- 普通导航 & 移动端下拉导航 -->
    <nav :class="['nav-links', { 'mobile-open': mobileNavOpen }]">
      <RouterLink
        v-for="item in navItems"
        :key="item.name"
        :to="item.path"
        class="nav-item"
        active-class="active-link"
        @click="mobileNavOpen = false"
      >
        {{ item.name }}
      </RouterLink>

      <a
        href="https://slty.site/#/redirector"
        target="_blank"
        rel="noopener"
        class="nav-item"
        active-class="active-link"
        @click="mobileNavOpen = false"
      >
        霜落映界
      </a>
    </nav>
  </header>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { io } from "socket.io-client";

const navItems = [
  { name: "风旅绘卷", path: "/" },
  { name: "云雀时迹", path: "/timeLine" },
  { name: "流息回响录", path: "/message" },
  { name: "心弦风络", path: "/gallery" },
  { name: "万物弥声阁", path: "/resources" },
  { name: "瑝珑风言栈", path: "/talk" },
  { name: "羽触视界", path: "/voice" },
  { name: "秧秧飞啾", path: "/game" },
  { name: "和鸣诗篇", path: "/music" },
  { name: "踏白风闻簿", path: "/wiki" },
  { name: "愿风吻过你", path: "/thanks" },
];

const mobileNavOpen = ref(false);
function toggleMobileNav() {
  mobileNavOpen.value = !mobileNavOpen.value;
}

const siteId = "yangyang";

const onlineCount = ref<number | null>(null);

// 连接时带上 query.siteId
const socket: any = io(import.meta.env.VITE_API_BASE_URL, {
  query: { siteId },
});

onMounted(() => {
  socket.on("onlineCount", (count: number) => {
    onlineCount.value = count;
  });
});

onBeforeUnmount(() => {
  socket.disconnect();
});
</script>

<style scoped lang="scss">
/* 秧秧 — 风羽守望：深海湖蓝 / 晴空湖蓝 / 珍珠光 + 羽尘气流 */
.app-header {
  --deep-bg: linear-gradient(
    180deg,
    #031127 0%,
    #07162a 100%
  ); /* 深海夜蓝 -> 更温柔的底 */
  --feather-haze: rgba(120, 180, 230, 0.06); /* 羽尘薄雾 */
  --accent: #2e88c9; /* 主色：湖蓝（服饰主色） */
  --accent-2: #7fd3ff; /* 次色：晴空薄蓝（羽流/亮点） */
  --pearl: #dfeff9; /* 珍珠高光 */
  --muted-text: #f3fbff; /* 温柔偏冷的白 */
  --shadow-core: rgba(3, 6, 10, 0.45);

  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  height: 64px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 40px;
  background: radial-gradient(
      420px 100px at 86% 18%,
      rgba(110, 160, 210, 0.03),
      transparent 12%
    ),
    var(--deep-bg);
  backdrop-filter: blur(6px) saturate(1);
  box-shadow: 0 10px 28px var(--shadow-core),
    0 0 14px rgba(60, 140, 200, 0.04) inset;
  border-bottom: 1px solid rgba(140, 190, 230, 0.04);
  animation: fadeInDown 0.45s ease-out both;
  overflow: visible;

  &::after {
    /* 羽尘薄雾 + 细小羽流纹理（模拟风中羽毛与气流） */
    content: "";
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    height: 100%;
    pointer-events: none;
    background: linear-gradient(180deg, var(--feather-haze), transparent 26%),
      repeating-linear-gradient(
        90deg,
        rgba(255, 255, 255, 0.02) 0 2px,
        rgba(255, 255, 255, 0) 2px 8px
      );
    mix-blend-mode: screen;
    opacity: 0.95;
  }

  /* 右侧羽影与风带（取代原来的链条电弧） */
  &::before {
    content: "";
    position: absolute;
    right: 6%;
    top: 4%;
    width: 360px;
    height: 110px;
    pointer-events: none;
    background: radial-gradient(
        44px 14px at 14% 22%,
        rgba(127, 200, 255, 0.12),
        transparent 30%
      ),
      radial-gradient(
        88px 28px at 50% 40%,
        rgba(46, 136, 201, 0.06),
        transparent 46%
      ),
      linear-gradient(90deg, rgba(120, 160, 200, 0.04), rgba(0, 0, 0, 0) 40%),
      /* 细长羽丝条纹 -> 表现羽毛纹理与风动 */
        repeating-linear-gradient(
          -25deg,
          rgba(255, 255, 255, 0.02) 0px 6px,
          rgba(255, 255, 255, 0) 6px 14px
        );
    filter: blur(6px) contrast(1.01);
    transform: translateY(0) rotate(-4deg);
    animation: feather-drift 9s ease-in-out infinite;
    mix-blend-mode: screen;
  }

  .title {
    position: relative;
    font-family: "Cinzel", "Noto Serif SC", serif;
    font-style: normal;
    font-size: 26px;
    font-weight: 800;
    color: var(--muted-text);
    background: linear-gradient(90deg, var(--accent), var(--accent-2));
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: 0.6px;
    text-shadow: 0 6px 18px rgba(3, 6, 10, 0.45);
    transition: transform 0.26s ease, text-shadow 0.26s ease;
    animation: float-pulse 7.2s ease-in-out infinite;

    &:hover {
      transform: translateY(-2px) scale(1.03);
      text-shadow: 0 12px 28px rgba(100, 160, 220, 0.06);
    }
  }

  .online-count {
    position: relative;
    margin-left: 16px;
    padding: 6px 14px;
    font-family: "Noto Sans SC", "PingFang SC", sans-serif;
    font-size: 1rem;
    color: var(--muted-text);
    background: linear-gradient(
      135deg,
      rgba(6, 10, 14, 0.22),
      rgba(6, 12, 18, 0.14)
    );
    border: 1px solid rgba(130, 180, 230, 0.03);
    border-radius: 24px;
    backdrop-filter: blur(6px);
    box-shadow: 0 6px 14px rgba(3, 6, 10, 0.35),
      0 0 10px rgba(95, 170, 230, 0.03) inset;
    overflow: hidden;
    cursor: default;
    transition: transform 0.22s ease, box-shadow 0.22s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 12px 28px rgba(3, 6, 10, 0.45),
        0 0 36px rgba(127, 200, 255, 0.04);
    }

    .count {
      display: inline-block;
      margin-left: 18px;
      font-weight: 800;
      color: var(--accent-2);
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-shadow: 0 0 8px rgba(127, 200, 255, 0.04);
    }
  }

  .nav-links {
    display: flex;
    gap: 22px;
    align-items: center;

    .nav-item {
      position: relative;
      color: var(--muted-text);
      font-weight: 600;
      text-decoration: none;
      transition: color 0.22s ease, transform 0.16s ease;
      padding: 6px 4px;
      border-radius: 6px;
      overflow: visible;
      font-family: "STKaiti", "KaiTi", "Noto Serif SC", serif;
      font-style: italic;

      &::after {
        /* 风波渐变下划线，代替电弧 */
        content: "";
        position: absolute;
        left: 50%;
        bottom: -8px;
        width: 0;
        height: 6px;
        border-radius: 6px;
        background: linear-gradient(
          90deg,
          rgba(0, 0, 0, 0),
          rgba(46, 136, 201, 0.9),
          rgba(127, 211, 255, 0.88),
          rgba(0, 0, 0, 0)
        );
        transform: translateX(-50%);
        opacity: 0.95;
        filter: blur(0.8px) contrast(1.02);
        transition: width 0.36s cubic-bezier(0.2, 0.9, 0.2, 1), left 0.36s,
          opacity 0.24s;
        background-size: 200% 100%;
        animation: wind-wave 6.4s linear infinite;
      }

      &::before {
        /* 小羽珠高光 */
        content: "";
        position: absolute;
        right: 14%;
        top: -6px;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: radial-gradient(circle, var(--pearl), transparent 60%);
        opacity: 0;
        transform: translateY(-6px) scale(0.86);
        transition: opacity 0.26s, transform 0.36s;
        box-shadow: 0 6px 12px rgba(40, 80, 120, 0.06);
      }

      &:hover {
        color: var(--accent-2);
        transform: translateY(-1.8px);
        text-shadow: 0 0 6px rgba(80, 140, 190, 0.02);
      }

      &:hover::after {
        width: 72%;
        left: 50%;
        opacity: 1;
      }

      &:hover::before {
        opacity: 1;
        transform: translateY(0) scale(1);
      }
    }

    .active-link {
      color: var(--accent);
      font-weight: 700;

      &::before {
        content: "❦"; /* 小羽饰符号，表现秧秧的羽与陪伴感 */
        position: absolute;
        right: 0px;
        top: 50%;
        transform: translateY(-50%) rotate(-6deg);
        font-size: 12px;
        color: var(--pearl);
        text-shadow: 0 2px 8px rgba(120, 180, 220, 0.06);
        animation: feather-emblem 3.4s ease-in-out infinite;
        opacity: 0.98;
      }

      &::after {
        width: 92%;
        opacity: 1;
        box-shadow: 0 6px 18px rgba(30, 70, 110, 0.04);
      }
    }
  }

  .hamburger {
    display: none;
    flex-direction: column;
    justify-content: space-around;
    width: 28px;
    height: 24px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;

    span {
      display: block;
      width: 100%;
      height: 3px;
      background: rgba(243, 251, 255, 0.94);
      border-radius: 2px;
      transition: transform 0.28s ease, opacity 0.28s ease, background 0.28s;
      box-shadow: 0 2px 6px rgba(3, 6, 10, 0.12),
        0 0 8px rgba(100, 160, 220, 0.03);
    }

    span.open:nth-child(1) {
      transform: translateY(10px) rotate(45deg);
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
    }

    span.open:nth-child(2) {
      opacity: 0;
    }

    span.open:nth-child(3) {
      transform: translateY(-10px) rotate(-45deg);
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
    }
  }

  @media (max-width: 768px) {
    padding: 0 20px;

    .title {
      display: none;
    }
    .hamburger {
      display: flex;
    }

    .nav-links {
      position: absolute;
      top: 64px;
      left: 0;
      right: 0;
      flex-direction: column;
      background: linear-gradient(
        180deg,
        rgba(6, 12, 18, 0.98),
        rgba(4, 8, 14, 0.995)
      );
      backdrop-filter: blur(12px);
      gap: 0;
      overflow: hidden;
      max-height: 0;
      transition: max-height 0.34s ease;
      border-top: 1px solid rgba(127, 200, 255, 0.03);

      &.mobile-open {
        max-height: 520px;
      }

      .nav-item {
        padding: 14px 20px;
        border-bottom: 1px solid rgba(255, 255, 255, 0.03);
      }
    }
  }
}

/* 动效关键帧（风波 / 羽流漂移 / 轻浮脉动） */
@keyframes wind-wave {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

@keyframes float-pulse {
  0% {
    transform: translateY(0);
  }
  45% {
    transform: translateY(-2px) scale(1.01);
    filter: drop-shadow(0 6px 14px rgba(90, 150, 200, 0.03));
  }
  100% {
    transform: translateY(0);
  }
}

@keyframes fadeInDown {
  0% {
    opacity: 0;
    transform: translateY(-8px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes feather-drift {
  0% {
    transform: translateY(0) rotate(-4deg) translateX(0);
    opacity: 0.92;
    filter: blur(6px);
  }
  50% {
    transform: translateY(-6px) rotate(2deg) translateX(-8px);
    opacity: 1;
    filter: blur(4px) saturate(1.02);
  }
  100% {
    transform: translateY(0) rotate(-4deg) translateX(0);
    opacity: 0.92;
  }
}

@keyframes feather-emblem {
  0% {
    transform: translateY(-6%) rotate(-6deg);
    opacity: 0.8;
    filter: drop-shadow(0 2px 6px rgba(120, 180, 220, 0.02));
  }
  50% {
    transform: translateY(6%) rotate(2deg);
    opacity: 1;
    filter: drop-shadow(0 6px 14px rgba(140, 200, 240, 0.04));
  }
  100% {
    transform: translateY(-6%) rotate(-6deg);
    opacity: 0.8;
  }
}
</style>
