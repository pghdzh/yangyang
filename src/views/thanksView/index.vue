<template>
  <div class="ack-page">
    <div class="ack-container">
      <h1 class="page-title">鸣谢名单</h1>
      <ul class="ack-list">
        <li
          v-for="(item, index) in acknowledgements"
          :key="index"
          class="ack-item"
          :style="{ animationDelay: index * 0.15 + 's' }"
        >
          <span class="nickname">{{ item.nickname }}:</span>

          <span class="suggestion">{{ item.suggestion }}</span>
        </li>
      </ul>
      <div v-if="acknowledgements.length === 0" class="empty">暂无鸣谢内容</div>
    </div>
    <footer class="ack-footer">© 2025 秧秧电子设定集 · 感谢所有支持者</footer>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
const acknowledgements = reactive([
  {
    nickname: "雪_霁__",
    suggestion: "以羽为眸，千风为刃(首页主标题)",
  },
  {
    nickname: "雪_霁__",
    suggestion:
      "我曾亲身感受过末日来临的绝望，所以才要更努力的守护这片安宁(首页副标题)",
  },
  {
    nickname: "雪_霁__",
    suggestion:
      "褪世家华裳，心怀柔肠，化羽护人间灯芒；共振风息长，鞘隐锋芒，出刃佑少女心芳(首页副标题)",
  },
  {
    nickname: "翩若人间惊鸿",
    suggestion: "我和你因风而相遇，而它也将成为你我不变的信标(开屏欢迎语)",
  },
  {
    nickname: "yanfree",
    suggestion:
      "无论一切怎样变化，我都会陪在你身边。只要你需要，我就一直在。(角色介绍副标题)",
  },
  {
    nickname: "克里斯托弗x",
    suggestion: "时间不会等的人，让我来等就好了。(语音彩蛋文本)",
  },
  {
    nickname: "克里斯托弗x",
    suggestion: "时间不会等的人，让我来等就好了。(语音彩蛋文本)",
  },
  {
    nickname: "克里斯托弗x",
    suggestion: "小游戏失败时放秧秧表情包(功能建议)",
  },
  {
    nickname: "克里斯托弗x",
    suggestion: "把秧秧写过的信件放在文本分享页面(功能建议)",
  },
  {
    nickname: "大厨小露",
    suggestion: "把秧秧库街区人物介绍放在资源分享页面(功能建议)",
  },
  {
    nickname: "随风漂泊的冒险家",
    suggestion: "小游戏管道小鸟重构为秧秧风格(功能建议)",
  },
]);
</script>

<style lang="scss" scoped>
// 基础颜色（按你提供的样式命名与配色）
$bg-deep: #031127; // 深海夜色底
$deep-2: #07162a; // 次深底用于渐变
$accent-1: #2e88c9; // 暗紫主光（冷雅）
$accent-2: #7fd3ff; // 冷海蓝高光（湿光感）

$text-main: #f3fbff; // 文字
$silver: #d7e9ee; // 剑光高光

// 补充色与阴影
$card-bg: rgba(6, 20, 20, 0.56);
$card-border: rgba(46, 143, 116, 0.06);
$glass: rgba(207, 238, 232, 0.03);
$shadow-strong: rgba(2, 6, 8, 0.7);
$shadow-accent: rgba(46, 143, 116, 0.12);

// 页面容器
.ack-page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding-top: 64px;

  background: linear-gradient(135deg, $bg-deep 0%, $deep-2 50%, $bg-deep 100%);
  background-size: 400% 400%;
  animation: bambooGradient 20s ease infinite;
}

.ack-container {
  flex: 1;
  padding: 2rem;
  max-width: 600px;
  margin: auto;
  position: relative;
  color: $text-main;
  overflow: hidden;
}

/* 背景位移动画（竹影/流云感） */
@keyframes bambooGradient {
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

/* 标题：竹绿到月色渐变文字 */
.page-title {
  font-size: 2.2rem;
  text-align: center;
  margin-bottom: 1.5rem;
  background: linear-gradient(90deg, $accent-1 0%, $accent-2 80%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  text-shadow: 0 0 8px rgba($accent-2, 0.12);
}

/* 列表 */
.ack-list {
  list-style: none;
  padding: 0;
  margin: 0;

  .ack-item {
    display: flex;
    align-items: baseline;
    margin-bottom: 0.8rem;
    padding: 0.8rem 1rem;
    background: $card-bg;
    backdrop-filter: blur(4px) saturate(1.02);
    border-radius: 8px;
    opacity: 0;
    transform: translateY(10px);
    animation: itemIn 0.6s forwards;
    border: 1px solid $card-border;
    box-shadow: 0 6px 18px $shadow-strong, 0 0 18px $shadow-accent inset;
    transition: transform 0.22s ease, box-shadow 0.22s ease,
      background 0.22s ease;

    &:hover {
      transform: translateY(-4px);
      box-shadow: 0 14px 32px rgba(2, 6, 8, 0.72),
        0 0 36px rgba($accent-1, 0.18) inset;
      background: linear-gradient(
        180deg,
        rgba(6, 28, 26, 0.7),
        rgba(4, 18, 18, 0.6)
      );
    }

    &:hover .nickname {
      transform: scale(1.06) translateY(-1px);
      text-shadow: 0 6px 16px rgba($accent-1, 0.08);
    }

    .nickname {
      font-weight: 700;
      margin-right: 0.6rem;
      background: linear-gradient(90deg, $accent-2 0%, $silver 100%);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
      text-shadow: 0 0 6px rgba($accent-2, 0.06);
      transition: transform 0.28s cubic-bezier(0.2, 0.9, 0.2, 1);
    }

    .suggestion {
      flex: 1;
      color: rgba($text-main, 0.9);
      font-size: 0.98rem;
      line-height: 1.5;
    }
  }
}

/* 进入动效 */
@keyframes itemIn {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 空状态 */
.empty {
  text-align: center;
  color: rgba($text-main, 0.72);
  margin-top: 2rem;
}

/* 页脚 */
.ack-footer {
  text-align: center;
  padding: 1rem 0;
  color: rgba($text-main, 0.75);
  font-size: 0.9rem;
  background: linear-gradient(
    180deg,
    rgba(3, 8, 8, 0.04),
    rgba(6, 18, 16, 0.06)
  );
  border-top: 1px solid rgba($accent-1, 0.02);
  margin-top: 1.5rem;
  border-radius: 6px;
}

/* 轻微竹风动画（可按需在元素上添加 .breeze） */
@keyframes breezeLift {
  0% {
    transform: translateY(0);
    opacity: 0.96;
  }
  50% {
    transform: translateY(-6px);
    opacity: 1;
  }
  100% {
    transform: translateY(0);
    opacity: 0.96;
  }
}
.breeze {
  animation: breezeLift 8s ease-in-out infinite;
}

/* 响应式 */
@media (max-width: 520px) {
  .ack-container {
    padding: 1.25rem;
    max-width: 92%;
  }
  .page-title {
    font-size: 1.6rem;
  }
  .ack-item {
    padding: 0.7rem 0.8rem;
  }
  .nickname {
    font-size: 0.97rem;
  }
  .suggestion {
    font-size: 0.95rem;
  }
}
</style>
