<template>
  <div id="app">
    <transition name="flor-intro-fade" v-if="showIntro">
      <div class="flor-intro-container" @click="skipIntro">
        <!-- 视频背景 -->
        <video
          class="flor-video-background"
          :src="videoSrc"
          autoplay
          muted
          loop
          playsinline
        ></video>
        
        <!-- 视频叠加层 -->
        <div class="flor-video-overlay"></div>
        
        <!-- 动态光晕效果 -->
        <div class="flor-glow-effects">
          <div class="flor-glow flor-glow-1"></div>
          <div class="flor-glow flor-glow-2"></div>
          <div class="flor-glow flor-glow-3"></div>
        </div>
        
        <!-- 音符飘落装饰 -->
        <div class="flor-note-rain">
          <div v-for="i in 8" :key="i" class="flor-note-drop" :style="noteStyle(i)">♪</div>
        </div>

        <!-- 主内容 -->
        <div class="flor-intro-content" aria-live="polite">
          <div class="flor-intro-inner">
            <!-- 顶部装饰 -->
            <div class="flor-top-decoration">
              <div class="flor-top-runes">
                <span class="flor-rune">⟡</span>
                <span class="flor-rune">⟣</span>
                <span class="flor-rune">✢</span>
              </div>
            </div>

            <!-- 主标题 -->
            <div class="flor-main-title-container">
              <h1 class="flor-main-title">
                <span class="flor-title-main">失亡彼岸</span>
                <span class="flor-title-sub">The Return of PHROLOVA</span>
              </h1>
              
              <!-- 标题装饰线 -->
              <div class="flor-title-decoration">
                <div class="flor-dec-line left"></div>
                <div class="flor-dec-center">
                  <span class="flor-dec-dot"></span>
                  <span class="flor-dec-symbol">❖</span>
                  <span class="flor-dec-dot"></span>
                </div>
                <div class="flor-dec-line right"></div>
              </div>
            </div>

            <!-- 打字机核心区域 -->
            <div class="flor-typewriter-core">
              <div class="flor-typewriter-border">
                <div class="flor-typewriter-glow"></div>
                <div class="flor-typewriter-content">
                  <p class="flor-typewriter-text">
                    {{ displayText }}
             
                  </p>
                </div>
              </div>
              
              <!-- 装饰符文 -->
              <div class="flor-typewriter-runes">
                <span class="flor-rune-small">♫</span>
                <span class="flor-rune-small">♪</span>
                <span class="flor-rune-small">♬</span>
              </div>
            </div>

            <!-- 交互提示 -->
            <div class="flor-interaction-hint">
              <div class="flor-hint-content">
                <span class="flor-hint-text">触响深空乐章</span>
                <div class="flor-hint-arrow">
                  <span class="flor-arrow-line"></span>
                  <span class="flor-arrow-head">›</span>
                </div>
              </div>
            </div>

            <!-- 底部信息 -->
            <div class="flor-bottom-info">
              <div class="flor-bottom-content">
                <span class="flor-bottom-text">湮灭纪元 {{ year }} AE</span>
                <span class="flor-bottom-divider">|</span>
                <span class="flor-bottom-text">弗洛洛永恒回响</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </transition>
    <div v-else>
      <navbar />
      <main class="main-content">
        <RouterView />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, computed } from "vue";
import { RouterView } from "vue-router";
import navbar from "@/components/navbar.vue";
import { gsap } from "gsap";

const showIntro = ref(true);
const videoSrc = ref("");
const displayText = ref("");

// 当前年份
const year = new Date().getFullYear();

// 弗洛洛风格的欢迎语句
const lines = [
  "奏响命运的乐章，于失亡的彼岸相见吧。",
  "所有的等待与誓言，都已谱成这首永恒的圆舞曲。",
  "听见了吗？那往日深渊的回响，正是为你而奏的迎宾曲。",
  "音符已就位，乐声将起。这一次，你愿意与我共舞吗？",
  "以这残响为引，为你揭开湮灭的序章。",
  "徘徊于生与死的乐章，我即是你的指引。",
  "这曲中尽是未言之语，弦外之音…你能听懂其中的真意吗？",
  "乐声终末，亦是重逢的开始。欢迎你的到来。",
] as const;

let typingTimer: number | null = null;
let animationTimers: number[] = [];

/* 打字机参数 */
const typingSpeed = 120;

function pickRandomLine(): string {
  const idx = Math.floor(Math.random() * lines.length);
  return lines[idx];
}

/* 逐字打字效果 */
function startTypewriter(line: string) {
  // 如果用户偏好减少动效，直接显示整句
  const reduce = window.matchMedia &&
    window.matchMedia("(prefers-reduced-motion: reduce)").matches;
  
  if (reduce) {
    displayText.value = line;
    return;
  }

  let i = 0;
  displayText.value = "";
  
  typingTimer = window.setInterval(() => {
    i++;
    displayText.value = line.slice(0, i);
    if (i >= line.length) {
      if (typingTimer) {
        clearInterval(typingTimer);
        typingTimer = null;
      }
    }
  }, typingSpeed);
}

// 音符样式计算
const noteStyle = (index: number) => {
  const left = 10 + (index - 1) * 11;
  const animationDelay = (index - 1) * 0.3;
  const duration = 4 + Math.random() * 3;
  
  return {
    left: `${left}%`,
    animationDelay: `${animationDelay}s`,
    animationDuration: `${duration}s`,
    fontSize: `${0.8 + Math.random() * 0.6}rem`,
    opacity: 0.3 + Math.random() * 0.4,
  };
};

// 初始化动画
function initAnimations() {
  // 音符飘落动画
  gsap.to('.flor-note-drop', {
    y: '+=120vh',
    duration: () => 4 + Math.random() * 3,
    repeat: -1,
    ease: "sine.inOut",
    stagger: 0.3,
  });

  // 光晕脉动动画
  gsap.to('.flor-glow-1', {
    scale: 1.3,
    opacity: 0.5,
    duration: 4,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
  });

  gsap.to('.flor-glow-2', {
    scale: 1.4,
    opacity: 0.4,
    duration: 5,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
    delay: 1,
  });

  gsap.to('.flor-glow-3', {
    scale: 1.2,
    opacity: 0.3,
    duration: 3.5,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
    delay: 2,
  });

  // 符文旋转动画
  gsap.to('.flor-top-runes .flor-rune', {
    rotation: 360,
    duration: 20,
    repeat: -1,
    ease: "none",
    stagger: 0.5,
  });

  // 标题入场动画
  gsap.from('.flor-main-title', {
    y: 40,
    opacity: 0,
    duration: 1.5,
    ease: "back.out(1.7)",
  });

  // 装饰线展开动画
  gsap.from('.flor-dec-line', {
    scaleX: 0,
    opacity: 0,
    duration: 1.8,
    delay: 0.3,
    ease: "power3.out",
    stagger: 0.2,
  });

  // 打字机区域入场
  gsap.from('.flor-typewriter-core', {
    y: 30,
    opacity: 0,
    duration: 1.2,
    delay: 0.6,
    ease: "power2.out",
  });

  // 提示动画
  gsap.to('.flor-hint-arrow', {
    x: 10,
    duration: 1.5,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
    delay: 1,
  });

  // 底部信息入场
  gsap.from('.flor-bottom-info', {
    y: 20,
    opacity: 0,
    duration: 1,
    delay: 1.2,
    ease: "power2.out",
  });

  // 小符文脉动
  gsap.to('.flor-rune-small', {
    scale: 1.3,
    opacity: 0.9,
    duration: 2,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
    stagger: 0.3,
  });
}

// 跳过介绍
function skipIntro() {
  showIntro.value = false;
  
  // 清理所有动画和定时器
  if (typingTimer) clearInterval(typingTimer);
  animationTimers.forEach(timer => clearTimeout(timer));
  animationTimers = [];
  gsap.killTweensOf("*");
}

onMounted(() => {
  // 检测是否为移动端并设置视频源
  const isMobile = window.innerWidth <= 768;
  const folder = isMobile ? "/mp2" : "/mp1";
  const index = Math.floor(Math.random() * 4) + 1;
  videoSrc.value = `${folder}/1 (${index}).mp4`;

  // 启动打字机效果
  const line = pickRandomLine();
  const timer = setTimeout(() => startTypewriter(line), 800);
  animationTimers.push(timer);

  // 初始化动画
  const initTimer = setTimeout(() => {
    initAnimations();
  }, 500);
  animationTimers.push(initTimer);

  // 9秒后自动进入主页
  const autoSkipTimer = setTimeout(() => {
    if (showIntro.value) {
      skipIntro();
    }
  }, 5000);
  animationTimers.push(autoSkipTimer);
});

onBeforeUnmount(() => {
  if (typingTimer) clearInterval(typingTimer);
  animationTimers.forEach(timer => clearTimeout(timer));
  gsap.killTweensOf("*");
});
</script>

<style scoped lang="scss">
#app {
  position: relative;
  min-height: 100vh;
}

/* 弗洛洛开屏容器 */
.flor-intro-container {
  position: fixed;
  inset: 0;
  background: #000;
  z-index: 9999;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  cursor: pointer;
  user-select: none;
}

/* 视频背景 */
.flor-video-background {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 1;
  pointer-events: none;
  filter: contrast(1.1) saturate(1.2);
}

/* 视频叠加层 */
.flor-video-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    to bottom,
    rgba(6, 8, 12, 0.85) 0%,
    rgba(26, 10, 26, 0.75) 30%,
    rgba(140, 60, 96, 0.4) 70%,
    rgba(6, 8, 12, 0.9) 100%
  );
  z-index: 2;
  pointer-events: none;
}

/* 光晕效果 */
.flor-glow-effects {
  position: absolute;
  inset: 0;
  z-index: 3;
  pointer-events: none;

  .flor-glow {
    position: absolute;
    border-radius: 50%;
    filter: blur(80px);
    opacity: 0.3;

    &.flor-glow-1 {
      width: 500px;
      height: 500px;
      top: 10%;
      left: 10%;
      background: linear-gradient(45deg, #b86be0, transparent 70%);
    }

    &.flor-glow-2 {
      width: 400px;
      height: 400px;
      bottom: 15%;
      right: 10%;
      background: linear-gradient(135deg, #ff6b85, transparent 70%);
    }

    &.flor-glow-3 {
      width: 300px;
      height: 300px;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: linear-gradient(90deg, #8c3c60, transparent 70%);
    }
  }
}

/* 音符飘落装饰 */
.flor-note-rain {
  position: absolute;
  inset: 0;
  z-index: 4;
  pointer-events: none;

  .flor-note-drop {
    position: absolute;
    top: -50px;
    color: rgba(255, 107, 133, 0.6);
    font-size: 1rem;
    opacity: 0.4;
    filter: drop-shadow(0 0 8px rgba(255, 107, 133, 0.3));
    animation: flor-note-fall linear infinite;
  }
}

/* 主内容区域 */
.flor-intro-content {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 5;

  .flor-intro-inner {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 2.5rem;
    max-width: 1200px;
    width: 90%;
    padding: 2rem;
    text-align: center;
  }
}

/* 顶部装饰 */
.flor-top-decoration {
  margin-bottom: 1rem;

  .flor-top-runes {
    display: flex;
    gap: 2rem;

    .flor-rune {
      font-size: 2.5rem;
      color: rgba(184, 107, 224, 0.7);
      opacity: 0.6;
      filter: drop-shadow(0 0 15px rgba(184, 107, 224, 0.4));
    }
  }
}

/* 主标题容器 */
.flor-main-title-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1.5rem;

  .flor-main-title {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.3rem;

    .flor-title-main {
      font-size: 5rem;
      font-weight: 900;
      background: linear-gradient(
        135deg,
        #b86be0 0%,
        #ff6b85 50%,
        #b86be0 100%
      );
      background-size: 200% auto;
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
      animation: flor-title-glow 3s ease infinite;
      letter-spacing: 0.15em;
      text-shadow: 
        0 0 40px rgba(184, 107, 224, 0.5),
        0 0 80px rgba(184, 107, 224, 0.3);
      line-height: 1;
    }

    .flor-title-sub {
      font-size: 1.2rem;
      font-weight: 300;
      color: rgba(249, 230, 242, 0.8);
      letter-spacing: 0.3em;
      text-transform: uppercase;
      margin-top: 0.5rem;
    }
  }
}

/* 标题装饰 */
.flor-title-decoration {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  width: 100%;
  max-width: 500px;

  .flor-dec-line {
    flex: 1;
    height: 1px;
    background: linear-gradient(
      90deg,
      transparent,
      rgba(255, 107, 133, 0.6),
      transparent
    );
    
    &.left {
      background: linear-gradient(
        90deg,
        transparent,
        rgba(184, 107, 224, 0.6),
        rgba(255, 107, 133, 0.6)
      );
    }
    
    &.right {
      background: linear-gradient(
        90deg,
        rgba(255, 107, 133, 0.6),
        rgba(184, 107, 224, 0.6),
        transparent
      );
    }
  }

  .flor-dec-center {
    display: flex;
    align-items: center;
    gap: 0.8rem;

    .flor-dec-dot {
      width: 6px;
      height: 6px;
      border-radius: 50%;
      background: rgba(255, 107, 133, 0.8);
      animation: flor-dot-pulse 2s ease-in-out infinite;

      &:nth-child(1) {
        animation-delay: 0s;
      }
      
      &:nth-child(3) {
        animation-delay: 1s;
      }
    }

    .flor-dec-symbol {
      color: #b86be0;
      font-size: 1.5rem;
      filter: drop-shadow(0 0 10px rgba(184, 107, 224, 0.5));
      animation: flor-symbol-rotate 10s linear infinite;
    }
  }
}

/* 打字机核心区域 */
.flor-typewriter-core {
  position: relative;
  width: 100%;
  max-width: 900px;
  margin: 1rem 0;

  .flor-typewriter-border {
    position: relative;
    padding: 2px;
    background: linear-gradient(
      90deg,
      transparent,
      rgba(184, 107, 224, 0.3),
      rgba(255, 107, 133, 0.3),
      rgba(184, 107, 224, 0.3),
      transparent
    );
    border-radius: 12px;
    margin-bottom: 1.5rem;

    .flor-typewriter-glow {
      position: absolute;
      inset: 0;
      background: linear-gradient(
        90deg,
        transparent,
        rgba(184, 107, 224, 0.1),
        rgba(255, 107, 133, 0.1),
        rgba(184, 107, 224, 0.1),
        transparent
      );
      border-radius: 10px;
      filter: blur(4px);
      opacity: 0.5;
      animation: flor-border-glow 3s ease-in-out infinite;
    }

    .flor-typewriter-content {
      position: relative;
      background: rgba(10, 8, 16, 0.7);
      backdrop-filter: blur(10px);
      border-radius: 10px;
      padding: 2rem;
      border: 1px solid rgba(184, 107, 224, 0.1);

      .flor-typewriter-text {
        margin: 0;
        font-weight: 300;
        font-size: 2rem;
        line-height: 1.6;
        color: rgba(249, 230, 242, 0.95);
        text-shadow: 0 2px 8px rgba(0, 0, 0, 0.5);
        letter-spacing: 0.05em;
        min-height: 1.6em;

    
      }
    }
  }

  .flor-typewriter-runes {
    display: flex;
    justify-content: center;
    gap: 1.5rem;

    .flor-rune-small {
      font-size: 1.2rem;
      color: rgba(184, 107, 224, 0.7);
      opacity: 0.6;
      filter: drop-shadow(0 0 8px rgba(184, 107, 224, 0.3));
    }
  }
}

/* 交互提示 */
.flor-interaction-hint {
  margin-top: 1.5rem;
  opacity: 0.8;
  animation: flor-hint-pulse 3s ease-in-out infinite;

  .flor-hint-content {
    display: flex;
    align-items: center;
    gap: 0.8rem;
    color: rgba(249, 230, 242, 0.8);
    font-size: 0.95rem;
    font-weight: 300;
    letter-spacing: 0.1em;
    text-transform: uppercase;

    .flor-hint-text {
      opacity: 0.9;
    }

    .flor-hint-arrow {
      display: flex;
      align-items: center;
      gap: 2px;

      .flor-arrow-line {
        width: 20px;
        height: 1px;
        background: linear-gradient(90deg, #b86be0, #ff6b85);
      }

      .flor-arrow-head {
        color: #ff6b85;
        font-size: 1.2rem;
        filter: drop-shadow(0 0 5px rgba(255, 107, 133, 0.5));
      }
    }
  }
}

/* 底部信息 */
.flor-bottom-info {
  margin-top: 2rem;

  .flor-bottom-content {
    display: flex;
    align-items: center;
    gap: 1rem;
    color: rgba(201, 180, 207, 0.7);
    font-size: 0.9rem;
    font-weight: 300;
    letter-spacing: 0.08em;

    .flor-bottom-text {
      opacity: 0.8;
    }

    .flor-bottom-divider {
      color: rgba(184, 107, 224, 0.5);
      opacity: 0.5;
    }
  }
}

/* 淡入淡出动画 */
.flor-intro-fade-enter-active,
.flor-intro-fade-leave-active {
  transition: opacity 1s cubic-bezier(0.4, 0, 0.2, 1);
}

.flor-intro-fade-enter-from,
.flor-intro-fade-to {
  opacity: 0;
}

/* 动画关键帧 */
@keyframes flor-note-fall {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.3;
  }
  100% {
    transform: translateY(120vh) rotate(360deg);
    opacity: 0;
  }
}

@keyframes flor-title-glow {
  0%, 100% {
    background-position: 0% 50%;
    text-shadow: 
      0 0 40px rgba(184, 107, 224, 0.5),
      0 0 80px rgba(184, 107, 224, 0.3);
  }
  50% {
    background-position: 100% 50%;
    text-shadow: 
      0 0 50px rgba(255, 107, 133, 0.6),
      0 0 100px rgba(255, 107, 133, 0.4);
  }
}

@keyframes flor-dot-pulse {
  0%, 100% {
    opacity: 0.6;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.3);
  }
}

@keyframes flor-symbol-rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes flor-border-glow {
  0%, 100% {
    opacity: 0.4;
  }
  50% {
    opacity: 0.8;
  }
}

@keyframes flor-cursor-blink {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.2;
  }
}

@keyframes flor-hint-pulse {
  0%, 100% {
    opacity: 0.6;
  }
  50% {
    opacity: 1;
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .flor-main-title-container {
    .flor-main-title {
      .flor-title-main {
        font-size: 3.2rem;
      }
      
      .flor-title-sub {
        font-size: 1rem;
        letter-spacing: 0.2em;
      }
    }
  }

  .flor-typewriter-core {
    .flor-typewriter-border {
      .flor-typewriter-content {
        padding: 1.5rem;
        
        .flor-typewriter-text {
          font-size: 1.5rem;
          line-height: 1.5;
        }
      }
    }
  }

  .flor-top-decoration {
    .flor-top-runes {
      gap: 1.5rem;
      
      .flor-rune {
        font-size: 2rem;
      }
    }
  }

  .flor-title-decoration {
    max-width: 350px;
    gap: 1rem;
  }

  .flor-glow-effects {
    .flor-glow {
      filter: blur(50px);
      
      &.flor-glow-1 {
        width: 300px;
        height: 300px;
      }
      
      &.flor-glow-2 {
        width: 250px;
        height: 250px;
      }
      
      &.flor-glow-3 {
        width: 200px;
        height: 200px;
      }
    }
  }
}

@media (max-width: 480px) {
  .flor-main-title-container {
    .flor-main-title {
      .flor-title-main {
        font-size: 2.5rem;
      }
      
      .flor-title-sub {
        font-size: 0.9rem;
      }
    }
  }

  .flor-typewriter-core {
    .flor-typewriter-border {
      .flor-typewriter-content {
        padding: 1.2rem;
        
        .flor-typewriter-text {
          font-size: 1.2rem;
        }
      }
    }
  }

  .flor-interaction-hint {
    .flor-hint-content {
      font-size: 0.85rem;
    }
  }

  .flor-bottom-info {
    .flor-bottom-content {
      font-size: 0.8rem;
      gap: 0.8rem;
    }
  }
}

/* 减少动效偏好 */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
  

  
  .flor-note-drop {
    animation: none !important;
    opacity: 0;
  }
}

/* 深色模式优化 */
@media (prefers-color-scheme: dark) {
  .flor-video-overlay {
    background: linear-gradient(
      to bottom,
      rgba(4, 6, 10, 0.9) 0%,
      rgba(18, 6, 18, 0.8) 30%,
      rgba(108, 43, 76, 0.5) 70%,
      rgba(4, 6, 10, 0.95) 100%
    );
  }
}
</style>