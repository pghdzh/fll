<template>
  <header class="app-header">
    <h1 class="title">弗洛洛电子设定集</h1>
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
        href="http://slty.site/#/redirector"
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
  { name: "失亡彼岸", path: "/" }, // 首页 — 弗洛洛的领域，失亡的彼岸
  { name: "乐章年谱", path: "/timeLine" }, // 年谱 — 以音乐章节记录的时间线
  { name: "音符札记", path: "/message" }, // 留言板 — 如音符般跃动的寄语
  { name: "残影绘卷", path: "/gallery" }, // 图集 — 往昔的残影与绘卷
  { name: "深渊秘典", path: "/resources" }, // 资料库 — 来自深渊的秘典与手稿
  { name: "回响低语", path: "/voice" }, // 语音馆 — 深渊回响中的低语
];

const mobileNavOpen = ref(false);
function toggleMobileNav() {
  mobileNavOpen.value = !mobileNavOpen.value;
}

const siteId = "fll";

const onlineCount = ref<number | null>(null);

// 连接时带上 query.siteId
const socket: any = io("http://36.150.237.25:3000", {
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
@import url("https://fonts.googleapis.com/css2?family=Cinzel:ital,wght@0,400;1,700&family=ZCOOL+QingKe+HuangYou&display=swap");

.app-header {
  /* 弗洛洛 - 暗紫湮灭 + 血泪光芒 + 音律指挥 */
  --deep-bg: rgba(6, 8, 12, 0.84); // 更深的夜色底
  --glass-accent: rgba(140, 60, 96, 0.06); // 暗紫玻璃感（偏红）
  --accent: #b86be0; // 主点光 - 暗紫（优雅、冷峻）
  --accent-2: #ff6b85; // 次级光 - 血玫（高光/血泪）
  --muted-text: #efe9ec; // 苍白象牙文字（偏冷）
  --warm-glow: rgba(255, 107, 133, 0.06); // 血色微光（极轻）
  --faint: rgba(184, 107, 224, 0.04); // 微弱紫光背景点

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
  /* 背景：深色带暗紫渐变 + 内嵌窄幅血色光晕 */
  background: linear-gradient(
    180deg,
    rgba(6, 8, 12, 0.9),
    rgba(12, 8, 16, 0.88)
  );
  backdrop-filter: blur(6px) saturate(0.96);
  box-shadow: 0 8px 36px rgba(3, 4, 6, 0.6), 0 0 18px var(--glass-accent) inset;
  border-bottom: 1px solid rgba(184, 107, 224, 0.04);
  animation: fadeInDown 0.6s ease-out both;

  /* 标题 - 暗紫到血玫的渐变 + 单侧血泪高光 */
  .title {
    position: relative;
    font-family: "Cinzel", "Cormorant Garamond", "STKaiti", serif;
    font-style: italic;
    font-size: 26px;
    font-weight: 700;
    color: var(--muted-text);
    background: linear-gradient(90deg, var(--accent), var(--accent-2));
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: 0.4px;
    text-shadow: 0 6px 22px rgba(12, 8, 16, 0.5), 0 2px 0 rgba(0, 0, 0, 0.25);
    transition: transform 0.28s ease, text-shadow 0.28s ease;
    animation: float-slow 10s ease-in-out infinite;

    &:hover {
      transform: translateY(-2px) scale(1.03);
      text-shadow: 0 10px 36px rgba(184, 107, 224, 0.14),
        0 2px 0 rgba(0, 0, 0, 0.26);
    }
  }

  /* 在线人数 / 状态 — 像一个收纳着“声骸盒”的暗匣 */
  .online-count {
    position: relative;
    margin-left: 16px;
    padding: 6px 14px;
    font-family: "Cinzel Decorative", serif;
    font-size: 1rem;
    color: var(--muted-text);
    background: linear-gradient(
      135deg,
      rgba(24, 12, 20, 0.28),
      rgba(12, 8, 18, 0.26)
    );
    border: 1px solid rgba(255, 107, 133, 0.06);
    border-radius: 24px;
    backdrop-filter: blur(8px);
    box-shadow: 0 8px 22px rgba(3, 4, 6, 0.5),
      0 0 12px rgba(184, 107, 224, 0.03) inset;
    overflow: hidden;
    cursor: default;
    transition: transform 0.22s ease, box-shadow 0.22s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 14px 36px rgba(3, 4, 6, 0.56),
        0 0 22px rgba(255, 107, 133, 0.06);
    }

    .count {
      display: inline-block;
      margin-left: 18px;
      font-weight: 700;
      color: var(--accent-2);
      background: linear-gradient(90deg, var(--accent), var(--accent-2));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-shadow: 0 0 10px rgba(255, 107, 133, 0.06);
    }
  }

  /* 导航链接 — 波形下划（模拟声波），鼠标悬停有血色脉冲 */
  .nav-links {
    display: flex;
    gap: 22px;
    align-items: center;

    .nav-item {
      position: relative;
      color: var(--muted-text);
      font-weight: 500;
      text-decoration: none;
      transition: color 0.22s ease, transform 0.16s ease;
      padding: 6px 4px;
      border-radius: 6px;
      overflow: visible;
      font-family:  "STKaiti", "KaiTi", "Noto Serif SC",
        serif;
      font-style: italic; /* 中文字体通常没有 true italic */

      &::after {
        content: "";
        position: absolute;
        left: 50%;
        bottom: -8px;
        width: 0;
        height: 4px;
        border-radius: 3px;
        /* 用渐变 + 噪声感做成波形样式（视觉上像频谱残影）*/
        background: linear-gradient(
          90deg,
          rgba(0, 0, 0, 0),
          rgba(184, 107, 224, 0.9),
          rgba(255, 107, 133, 0.85),
          rgba(0, 0, 0, 0)
        );
        transform: translateX(-50%);
        opacity: 0.95;
        filter: blur(0.7px) contrast(1.02);
        transition: width 0.36s cubic-bezier(0.2, 0.9, 0.2, 1),
          left 0.36s cubic-bezier(0.2, 0.9, 0.2, 1), opacity 0.24s;
        background-size: 200% 100%;
        animation: flow-wave 6.5s linear infinite;
      }

      &:hover {
        color: var(--accent-2);
        transform: translateY(-1.8px);
        text-shadow: 0 0 8px rgba(255, 107, 133, 0.04);
      }

      &:hover::after {
        width: 72%;
        left: 50%;
        opacity: 1;
      }
    }

    .active-link {
      color: var(--accent);
      font-weight: 600;
      /* 小音符伪元素，微微浮动 */
      &::before {
        content: "♪";
        position: absolute;
        right: 0px;
        top: 50%;
        transform: translateY(-50%);
        font-size: 12px;
        color: rgba(255, 107, 133, 0.95);
        text-shadow: 0 2px 8px rgba(184, 107, 224, 0.12);
        animation: note-float 3.6s ease-in-out infinite;
        opacity: 0.9;
      }

      &::after {
        width: 92%;
        opacity: 1;
        box-shadow: 0 6px 22px rgba(184, 107, 224, 0.08);
      }
    }
  }

  /* 汉堡按钮（移动） - 线条更尖锐些、带暗紫光晕 */
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
      background: rgba(239, 233, 236, 0.9);
      border-radius: 2px;
      transition: transform 0.28s ease, opacity 0.28s ease, background 0.28s;
      box-shadow: 0 2px 6px rgba(8, 6, 10, 0.18),
        0 0 8px rgba(184, 107, 224, 0.02);
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
        rgba(8, 6, 12, 0.98),
        rgba(6, 4, 10, 0.995)
      );
      backdrop-filter: blur(12px);
      gap: 0;
      overflow: hidden;
      max-height: 0;
      transition: max-height 0.34s ease;
      border-top: 1px solid rgba(255, 107, 133, 0.03);

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

/* 关键帧：波形流动、标题浮动、音符漂浮、血色脉冲 */
@keyframes flow-wave {
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

@keyframes float-slow {
  0% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-3.5px);
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

@keyframes note-float {
  0% {
    transform: translateY(-6%) rotate(-6deg);
    opacity: 0.85;
  }
  50% {
    transform: translateY(6%) rotate(2deg);
    opacity: 1;
  }
  100% {
    transform: translateY(-6%) rotate(-6deg);
    opacity: 0.85;
  }
}
</style>
