<template>
  <main class="florhome">
    <!-- 动态背景元素 -->
    <div class="flor-bg-elements" aria-hidden="true">
      <div class="bg-element element-1"></div>
      <div class="bg-element element-2"></div>
      <div class="bg-element element-3"></div>
      <div class="bg-element element-4"></div>
    </div>

    <!-- 彼岸花粒子画布 -->
    <canvas ref="canvasEl" class="flor-canvas" aria-hidden="true"></canvas>

    <!-- 光晕层 -->
    <div class="glow-layer">
      <div class="glow glow-1"></div>
      <div class="glow glow-2"></div>
      <div class="glow glow-3"></div>
    </div>

    <!-- 主内容区域 -->
    <section class="main-content" role="main" ref="contentRef">
      <!-- 符文装饰 -->
      <div class="rune-decorations">
        <div class="rune rune-left" ref="runeLeftRef">⟡</div>
        <div class="rune rune-right" ref="runeRightRef">⟣</div>
      </div>

      <!-- 标题区域 -->
      <div class="title-container">
        <div class="title-wrapper" ref="titleWrapperRef">
          <h1 class="flor-title">
            <span class="title-main">弗洛洛</span>
            <span class="title-sub">PHROLOVA · 深渊咏叹者</span>
          </h1>

          <div class="title-decoration">
            <div class="decoration-line"></div>
            <div class="decoration-symbols">
              <span class="symbol">✢</span>
              <span class="symbol">✣</span>
              <span class="symbol">✤</span>
            </div>
            <div class="decoration-line"></div>
          </div>
        </div>

        <!-- 副标题打字机 -->
        <div class="subtitle-container" ref="subtitleRef">
          <div class="subtitle-wrapper">
            <span class="typed">{{ typed }}</span>
            <span class="cursor" aria-hidden="true">❙</span>
          </div>
        </div>
      </div>

      <!-- 交互区域 -->
      <div class="interactive-section">
        <!-- 音符装饰 -->
        <div class="note-rain">
          <div
            v-for="i in 5"
            :key="i"
            class="note-drop"
            :ref="(el) => setNoteDropRef(i - 1, el)"
          >
            <span class="note-char">♪</span>
          </div>
        </div>

        <!-- 召唤按钮 -->
        <button class="summon-btn" @click="triggerSummon" ref="summonBtnRef">
          <span class="btn-text">深渊回响</span>
          <span class="btn-icon">⟡</span>
        </button>
      </div>
    </section>

    <!-- 页脚 -->
    <footer
      class="flor-footer"
      role="contentinfo"
      aria-label="页脚信息"
      ref="footerRef"
    >
      <div class="footer-inner">
        <div class="footer-content">
          <div class="footer-logo">
            <span class="logo-text">PHROLOVA</span>
            <span class="logo-symbol">❖</span>
          </div>

          <div class="footer-info">
            <div class="info-line">
              <span class="info-label">湮灭纪元</span>
              <span class="info-value">{{ year }} AE</span>
            </div>
            <div class="info-line">
              <span class="info-label">乐章编码</span>
              <span class="info-value">FlRo-{{ year }}-{{ randomCode }}</span>
            </div>
          </div>

          <div class="footer-credits">
            <div class="credit-item">深空回响 · 永恒咏叹</div>
            <div class="credit-item">奏者 · 霜落天亦</div>
          </div>
        </div>
      </div>
    </footer>

    <!-- 特效层 -->
    <div class="effects-layer" aria-hidden="true">
      <div
        v-for="i in 8"
        :key="i"
        class="spark"
        :ref="(el) => setSparkRef(i - 1, el)"
      ></div>
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

// 注册GSAP插件
gsap.registerPlugin(ScrollTrigger);

// 变量定义
const year = new Date().getFullYear();
const randomCode = ref(
  Math.random().toString(36).substring(2, 8).toUpperCase()
);
const canvasEl = ref<HTMLCanvasElement | null>(null);
const contentRef = ref<HTMLElement | null>(null);
const titleWrapperRef = ref<HTMLElement | null>(null);
const subtitleRef = ref<HTMLElement | null>(null);
const footerRef = ref<HTMLElement | null>(null);
const summonBtnRef = ref<HTMLElement | null>(null);
const runeLeftRef = ref<HTMLElement | null>(null);
const runeRightRef = ref<HTMLElement | null>(null);

// 修复类型问题的ref设置函数
const noteDropsRef = ref<(HTMLElement | null)[]>([]);
const sparkRefs = ref<(HTMLElement | null)[]>([]);

// 设置音符引用
const setNoteDropRef = (index: number, el: any) => {
  // 确保el是HTMLElement类型
  if (el instanceof HTMLElement) {
    noteDropsRef.value[index] = el;
  } else if (el?.$el instanceof HTMLElement) {
    // 如果是组件实例，获取其根DOM元素
    noteDropsRef.value[index] = el.$el;
  } else {
    noteDropsRef.value[index] = null;
  }
};

// 设置火花引用
const setSparkRef = (index: number, el: any) => {
  if (el instanceof HTMLElement) {
    sparkRefs.value[index] = el;
  } else if (el?.$el instanceof HTMLElement) {
    sparkRefs.value[index] = el.$el;
  } else {
    sparkRefs.value[index] = null;
  }
};

// Canvas 变量
let ctx: CanvasRenderingContext2D;
let animationId = 0;
let lastTime = 0;
let elapsed = 0;
// 图片路径修复
import violet from "@/assets/violet.png";

interface Rose {
  baseX: number;
  y: number;
  size: number;
  speed: number;
  swayAmp: number;
  swayFreq: number;
  phase: number;
  angle: number;
  angularSpeed: number;
}
// 打字机效果
const lines = [
  "以湮灭为始，奏响深空终章",
  "彼岸花开，奏者归来",
  "破碎的音符终将重聚",
  "深渊回响，为你而鸣",
  "在乐声的裂隙中窥见永恒",
  "每个休止符都是未尽的誓言",
  "旋律编织的命运牢笼",
  "当终章奏响，回望即是重生",
  "弗洛洛之名，即为永恒咏叹",
  "深空彼岸，回响不息",
];

const typed = ref("");
let lineIndex = 0;
let charIndex = 0;
let deleting = false;
let typingTimer: number | null = null;

const TYPING_SPEED = 80;
const DELETING_SPEED = 40;
const PAUSE_DURATION = 1800;

// 打字机函数
function typeWriterTick() {
  const currentLine = lines[lineIndex];

  if (!deleting) {
    typed.value = currentLine.slice(0, charIndex + 1);
    charIndex++;

    if (charIndex >= currentLine.length) {
      typingTimer = window.setTimeout(() => {
        deleting = true;
        typeWriterTick();
      }, PAUSE_DURATION);
      return;
    }

    typingTimer = window.setTimeout(typeWriterTick, TYPING_SPEED);
  } else {
    typed.value = currentLine.slice(0, charIndex - 1);
    charIndex--;

    if (charIndex <= 0) {
      deleting = false;
      lineIndex = (lineIndex + 1) % lines.length;
      typingTimer = window.setTimeout(typeWriterTick, 400);
      return;
    }

    typingTimer = window.setTimeout(typeWriterTick, DELETING_SPEED);
  }
}
const roses: Rose[] = [];
const ROSE_COUNT_DESKTOP = 18;
const ROSE_COUNT_MOBILE = 6;
const ROSE_IMG = new Image();
ROSE_IMG.src = violet;

function initRoses(count: number) {
  roses.length = 0;
  const canvas = canvasEl.value!;
  const w = canvas.width / (window.devicePixelRatio || 1);
  const h = canvas.height / (window.devicePixelRatio || 1);

  for (let i = 0; i < count; i++) {
    const baseX = Math.random() * w;
    roses.push({
      baseX,
      y: Math.random() * -h,
      size: 48 + Math.random() * 68, // 稍微精简尺寸
      speed: 12 + Math.random() * 36, // 速度更缓
      swayAmp: 12 + Math.random() * 26,
      swayFreq: 0.15 + Math.random() * 0.7,
      phase: Math.random() * Math.PI * 2,
      angle: Math.random() * Math.PI * 2,
      angularSpeed: (Math.random() - 0.5) * 1.2,
    });
  }
  elapsed = 0;
}

let resizeTimeout: number;
function resizeCanvas() {
  window.clearTimeout(resizeTimeout);
  resizeTimeout = window.setTimeout(() => {
    cancelAnimationFrame(animationId);
    const canvas = canvasEl.value!;
    const parent = canvas.parentElement!;
    const dpr = window.devicePixelRatio || 1;
    const w = parent.clientWidth;
    const h = Math.max(parent.clientHeight, 420); // 给个最小高度，避免太窄时粒子不明显

    canvas.style.width = w + "px";
    canvas.style.height = h + "px";
    canvas.width = w * dpr;
    canvas.height = h * dpr;

    ctx.setTransform(1, 0, 0, 1, 0, 0);
    ctx.scale(dpr, dpr);

    const isMobile = w < 768;
    initRoses(isMobile ? ROSE_COUNT_MOBILE : ROSE_COUNT_DESKTOP);
    lastTime = 0;
    animationId = requestAnimationFrame(tickCanvas);
  }, 160);
}

function tickCanvas(now: number) {
  if (!lastTime) lastTime = now;
  const dt = (now - lastTime) / 1000;
  lastTime = now;
  elapsed += dt;

  const canvas = canvasEl.value!;
  const w = canvas.clientWidth;
  const h = canvas.clientHeight;

  ctx.clearRect(0, 0, w, h);

  // 轻微整体雾层，增强深度（透明度低，避免影响可读性）
  ctx.fillStyle = "rgba(2,8,14,0.08)";
  ctx.fillRect(0, 0, w, h);

  roses.forEach((r) => {
    r.y += r.speed * dt;
    const sway = r.swayAmp * Math.sin(r.phase + elapsed * r.swayFreq);
    const x = r.baseX + sway;
    r.angle += r.angularSpeed * dt;

    if (r.y > h + r.size) {
      r.y = -r.size * 0.6;
      r.baseX = Math.random() * w;
      r.phase = Math.random() * Math.PI * 2;
    }

    if (x > w + r.size || x < -r.size) return;

    // 计算透明度：越远看上去越淡
    const alpha = Math.max(0, Math.min(1, 1 - (r.y / h) * 0.6));

    ctx.save();
    ctx.globalAlpha = alpha;
    ctx.translate(x, r.y);
    ctx.rotate(r.angle);

    if (ROSE_IMG && ROSE_IMG.complete && ROSE_IMG.naturalWidth > 0) {
      // 使用图片绘制，但加上一层冷色调叠加（globalCompositeOperation 简单处理）
      ctx.drawImage(ROSE_IMG, -r.size / 2, -r.size / 2, r.size, r.size);

      // 轻微冷光叠加，提升风格一致性
      ctx.globalCompositeOperation = "lighter";
      const grad = ctx.createRadialGradient(0, 0, 0, 0, 0, r.size);
      grad.addColorStop(0, `rgba(79,233,223,${0.08 * alpha})`);
      grad.addColorStop(1, "rgba(0,0,0,0)");
      ctx.fillStyle = grad;
      ctx.fillRect(-r.size / 2, -r.size / 2, r.size, r.size);
      ctx.globalCompositeOperation = "source-over";
    }

    ctx.restore();
  });

  animationId = requestAnimationFrame(tickCanvas);
}
// GSAP 动画
function initAnimations() {
  // 符文旋转动画
  if (runeLeftRef.value && runeRightRef.value) {
    gsap.to(runeLeftRef.value, {
      rotation: 360,
      duration: 20,
      repeat: -1,
      ease: "none",
    });

    gsap.to(runeRightRef.value, {
      rotation: -360,
      duration: 20,
      repeat: -1,
      ease: "none",
    });
  }

  // 标题入场动画
  if (titleWrapperRef.value) {
    gsap.from(titleWrapperRef.value.children, {
      y: 50,
      opacity: 0,
      duration: 1.2,
      stagger: 0.2,
      ease: "back.out(1.7)",
    });
  }

  // 副标题打字机入场
  if (subtitleRef.value) {
    gsap.from(subtitleRef.value, {
      y: 30,
      opacity: 0,
      duration: 1,
      delay: 0.8,
      ease: "power2.out",
    });
  }

  // 音符雨动画
  noteDropsRef.value.forEach((note, index) => {
    if (note) {
      gsap.to(note, {
        y: "+=200",
        duration: 2 + Math.random() * 2,
        delay: index * 0.3,
        repeat: -1,
        repeatDelay: 1,
        ease: "sine.inOut",
        modifiers: {
          y: gsap.utils.unitize((y) => parseFloat(y) % 200),
        },
      });
    }
  });

  // 闪光粒子动画
  sparkRefs.value.forEach((spark, index) => {
    if (spark) {
      const x = Math.random() * 100;
      const y = Math.random() * 100;

      gsap.set(spark, {
        x: `${x}vw`,
        y: `${y}vh`,
      });

      gsap.to(spark, {
        x: `+=${(Math.random() - 0.5) * 100}`,
        y: `+=${(Math.random() - 0.5) * 100}`,
        duration: 3 + Math.random() * 4,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut",
      });
    }
  });

  // 背景元素动画
  gsap.to(".bg-element", {
    scale: 1.1,
    duration: 4,
    repeat: -1,
    yoyo: true,
    stagger: 0.5,
    ease: "sine.inOut",
  });

  // 光晕脉动
  gsap.to(".glow", {
    scale: 1.2,
    opacity: 0.8,
    duration: 3,
    repeat: -1,
    yoyo: true,
    stagger: 0.3,
    ease: "sine.inOut",
  });

  // 滚动触发动画
  ScrollTrigger.create({
    trigger: footerRef.value,
    start: "top bottom",
    onEnter: () => {
      if (footerRef.value) {
        gsap.from(footerRef.value.children, {
          y: 30,
          opacity: 0,
          duration: 1,
          stagger: 0.1,
          ease: "power2.out",
        });
      }
    },
  });
}

// 召唤特效
function triggerSummon() {
  // 按钮动画
  if (summonBtnRef.value) {
    gsap.to(summonBtnRef.value, {
      scale: 0.95,
      duration: 0.1,
      yoyo: true,
      repeat: 1,
      ease: "power2.inOut",
    });

    // 添加涟漪效果
    const ripple = document.createElement("div");
    ripple.className = "summon-ripple";
    summonBtnRef.value.appendChild(ripple);

    gsap.to(ripple, {
      scale: 3,
      opacity: 0,
      duration: 1,
      ease: "power2.out",
      onComplete: () => {
        if (ripple.parentNode) {
          ripple.parentNode.removeChild(ripple);
        }
      },
    });
  }

  // 随机切换打字机内容
  const oldIndex = lineIndex;
  lineIndex = Math.floor(Math.random() * lines.length);
  if (lineIndex === oldIndex) lineIndex = (lineIndex + 1) % lines.length;

  charIndex = 0;
  deleting = false;

  if (typingTimer) clearTimeout(typingTimer);
  typeWriterTick();
}

onMounted(() => {
  // 启动打字机
  typingTimer = window.setTimeout(typeWriterTick, 1000);

  // 初始化画布
  if (canvasEl.value) {
    const canvas = canvasEl.value!;
    ctx = canvas.getContext("2d")!;

    // 当图片加载或资源就绪后调整 canvas 大小并启动渲染
    ROSE_IMG.onload = () => {
      resizeCanvas();
    };
    // 如果图片已经加载完（缓存情况），也要触发 init
    if (ROSE_IMG.complete && ROSE_IMG.naturalWidth > 0) {
      resizeCanvas();
    }

    window.addEventListener("resize", resizeCanvas);
  }

  // 初始化GSAP动画
  initAnimations();
});

onBeforeUnmount(() => {
  if (typingTimer) clearTimeout(typingTimer);
  cancelAnimationFrame(animationId);
  window.removeEventListener("resize", resizeCanvas);

  // 清理GSAP动画
  ScrollTrigger.getAll().forEach((trigger) => trigger.kill());
  gsap.killTweensOf("*");
});
</script>

<style lang="scss" scoped>
.florhome {
  --deep-space: #06080c;
  --void-purple: #1a0a1a;
  --accent-magenta: #b86be0;
  --accent-crimson: #ff6b85;
  --accent-deep: #8c3c60;
  --glow-magenta: rgba(184, 107, 224, 0.4);
  --glow-crimson: rgba(255, 107, 133, 0.3);
  --text-primary: #f0e6f2;
  --text-secondary: #c9b4cf;
  --glass-dark: rgba(10, 8, 16, 0.85);
  --glass-light: rgba(30, 20, 40, 0.6);
  --border-faint: rgba(184, 107, 224, 0.15);
  --mobile-bg: rgba(8, 6, 12, 0.98);

  min-height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
  background: radial-gradient(
      circle at 20% 30%,
      var(--void-purple) 0%,
      transparent 50%
    ),
    radial-gradient(circle at 80% 70%, var(--accent-deep) 0%, transparent 50%),
    linear-gradient(135deg, var(--deep-space) 0%, #0a080e 100%);
  font-family: "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif;
  color: var(--text-primary);

  /* 背景装饰元素 */
  .flor-bg-elements {
    position: absolute;
    inset: 0;
    z-index: 1;
    pointer-events: none;

    .bg-element {
      position: absolute;
      border-radius: 50%;
      filter: blur(40px);
      opacity: 0.1;

      &.element-1 {
        width: 300px;
        height: 300px;
        top: 10%;
        left: 10%;
        background: var(--accent-magenta);
      }

      &.element-2 {
        width: 200px;
        height: 200px;
        bottom: 20%;
        right: 10%;
        background: var(--accent-crimson);
      }

      &.element-3 {
        width: 400px;
        height: 400px;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: var(--accent-deep);
        opacity: 0.05;
      }

      &.element-4 {
        width: 150px;
        height: 150px;
        top: 70%;
        left: 20%;
        background: var(--glow-magenta);
      }
    }
  }

  /* 粒子画布 */
  .flor-canvas {
    position: absolute;
    inset: 0;
    z-index: 2;
    pointer-events: none;
  }

  /* 光晕层 */
  .glow-layer {
    position: absolute;
    inset: 0;
    z-index: 3;
    pointer-events: none;

    .glow {
      position: absolute;
      border-radius: 50%;
      filter: blur(60px);

      &.glow-1 {
        width: 400px;
        height: 400px;
        top: 20%;
        left: 15%;
        background: var(--glow-magenta);
        opacity: 0.3;
      }

      &.glow-2 {
        width: 300px;
        height: 300px;
        bottom: 30%;
        right: 20%;
        background: var(--glow-crimson);
        opacity: 0.2;
      }

      &.glow-3 {
        width: 200px;
        height: 200px;
        top: 60%;
        left: 70%;
        background: rgba(140, 60, 96, 0.3);
        opacity: 0.15;
      }
    }
  }

  /* 主内容区域 */
  .main-content {
    position: relative;
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 2rem;
    z-index: 4;

    /* 符文装饰 */
    .rune-decorations {
      position: absolute;
      width: 100%;
      height: 100%;
      pointer-events: none;

      .rune {
        position: absolute;
        font-size: 4rem;
        color: var(--accent-magenta);
        opacity: 0.2;
        filter: drop-shadow(0 0 10px var(--glow-magenta));

        &.rune-left {
          left: 10%;
          top: 20%;
        }

        &.rune-right {
          right: 10%;
          bottom: 30%;
        }
      }
    }

    /* 标题容器 */
    .title-container {
      text-align: center;
      max-width: 800px;
      margin-bottom: 3rem;

      .title-wrapper {
        margin-bottom: 2rem;

        .flor-title {
          margin-bottom: 1.5rem;

          .title-main {
            display: block;
            font-size: 5rem;
            font-weight: 900;
            background: linear-gradient(
              135deg,
              var(--accent-magenta) 0%,
              var(--accent-crimson) 50%,
              var(--accent-magenta) 100%
            );
            background-size: 200% auto;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: gradient-shift 3s ease infinite;
            letter-spacing: 0.1em;
            text-shadow: 0 0 30px var(--glow-magenta),
              0 0 60px rgba(184, 107, 224, 0.3);
            line-height: 1.2;
          }

          .title-sub {
            display: block;
            font-size: 1.2rem;
            font-weight: 300;
            color: var(--text-secondary);
            letter-spacing: 0.3em;
            margin-top: 0.5rem;
            opacity: 0.8;
          }
        }

        .title-decoration {
          display: flex;
          align-items: center;
          justify-content: center;
          gap: 1.5rem;
          margin: 0 auto;
          max-width: 400px;

          .decoration-line {
            flex: 1;
            height: 1px;
            background: linear-gradient(
              90deg,
              transparent,
              var(--accent-deep),
              transparent
            );
            position: relative;

            &::before,
            &::after {
              content: "";
              position: absolute;
              top: 50%;
              transform: translateY(-50%);
              width: 8px;
              height: 8px;
              border-radius: 50%;
              background: var(--accent-crimson);
            }

            &::before {
              left: -4px;
            }

            &::after {
              right: -4px;
            }
          }

          .decoration-symbols {
            display: flex;
            gap: 0.5rem;

            .symbol {
              color: var(--accent-magenta);
              font-size: 1.2rem;
              opacity: 0.7;
              animation: symbol-pulse 2s ease-in-out infinite;

              &:nth-child(1) {
                animation-delay: 0s;
              }
              &:nth-child(2) {
                animation-delay: 0.5s;
              }
              &:nth-child(3) {
                animation-delay: 1s;
              }
            }
          }
        }
      }

      .subtitle-container {
        .subtitle-wrapper {
          font-size: 1.8rem;
          min-height: 2.5em;
          color: var(--text-primary);
          position: relative;
          padding: 0 1rem;

          .typed {
            display: inline-block;
            font-weight: 300;
            color: var(--text-primary);
            text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
            letter-spacing: 0.05em;
            background: linear-gradient(
              to right,
              var(--text-primary),
              var(--text-secondary)
            );
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
          }

          .cursor {
            display: inline-block;
            width: 2px;
            height: 1.2em;
            margin-left: 0.3rem;
            background: linear-gradient(
              to bottom,
              var(--accent-crimson),
              var(--accent-magenta)
            );
            animation: blink 1s steps(2) infinite;
            transform: translateY(0.2em);
            border-radius: 1px;
          }
        }
      }
    }

    /* 交互区域 */
    .interactive-section {
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 2rem;
      margin-top: 2rem;

      .note-rain {
        position: absolute;
        width: 100%;
        height: 200px;
        top: -100px;
        pointer-events: none;

        .note-drop {
          position: absolute;
          top: 0;
          color: var(--accent-magenta);
          opacity: 0.4;
          filter: drop-shadow(0 0 5px var(--glow-magenta));

          .note-char {
            font-size: 1.2rem;
            display: block;
            transform: rotate(20deg);
          }

          &:nth-child(1) {
            left: 10%;
          }
          &:nth-child(2) {
            left: 25%;
          }
          &:nth-child(3) {
            left: 40%;
          }
          &:nth-child(4) {
            left: 60%;
          }
          &:nth-child(5) {
            left: 75%;
          }
        }
      }

      .summon-btn {
        background: linear-gradient(
          135deg,
          rgba(184, 107, 224, 0.15) 0%,
          rgba(255, 107, 133, 0.15) 100%
        );
        border: 1px solid var(--border-faint);
        color: var(--text-primary);
        padding: 1rem 2.5rem;
        border-radius: 50px;
        font-size: 1.1rem;
        font-weight: 500;
        cursor: pointer;
        transition: all 0.3s ease;
        display: flex;
        align-items: center;
        gap: 0.8rem;
        backdrop-filter: blur(10px);
        position: relative;
        overflow: hidden;

        &::before {
          content: "";
          position: absolute;
          inset: 0;
          background: linear-gradient(
            135deg,
            transparent 30%,
            var(--glow-magenta) 50%,
            transparent 70%
          );
          opacity: 0;
          transition: opacity 0.3s ease;
        }

        &:hover {
          background: linear-gradient(
            135deg,
            rgba(184, 107, 224, 0.25) 0%,
            rgba(255, 107, 133, 0.25) 100%
          );
          border-color: var(--accent-magenta);
          transform: translateY(-2px);
          box-shadow: 0 4px 25px var(--glow-magenta),
            0 0 0 1px rgba(184, 107, 224, 0.2);

          &::before {
            opacity: 0.3;
            animation: shine 2s ease-in-out infinite;
          }
        }

        &:active {
          transform: translateY(0);
        }

        .btn-text {
          background: linear-gradient(
            to right,
            var(--text-primary),
            var(--text-secondary)
          );
          -webkit-background-clip: text;
          background-clip: text;
          color: transparent;
          font-weight: 500;
          letter-spacing: 0.05em;
        }

        .btn-icon {
          color: var(--accent-crimson);
          font-size: 1rem;
          filter: drop-shadow(0 0 5px var(--glow-crimson));
        }
      }
    }
  }

  /* 页脚 */
  .flor-footer {
    position: relative;
    z-index: 4;
    padding: 2rem 1.5rem;
    background: linear-gradient(
      to top,
      rgba(10, 8, 16, 0.9) 0%,
      transparent 100%
    );
    border-top: 1px solid var(--border-faint);
    backdrop-filter: blur(20px);
    margin-top: auto;

    .footer-inner {
      max-width: 1200px;
      margin: 0 auto;
    }

    .footer-content {
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 2rem;

      @media (max-width: 768px) {
        flex-direction: column;
        text-align: center;
        gap: 1.5rem;
      }
    }

    .footer-logo {
      display: flex;
      align-items: center;
      gap: 0.5rem;

      .logo-text {
        font-size: 1.5rem;
        font-weight: 700;
        background: linear-gradient(
          to right,
          var(--accent-magenta),
          var(--accent-crimson)
        );
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
      }

      .logo-symbol {
        color: var(--accent-magenta);
        font-size: 1.2rem;
        opacity: 0.8;
      }
    }

    .footer-info {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;

      .info-line {
        display: flex;
        gap: 0.5rem;
        font-size: 0.9rem;
        color: var(--text-secondary);

        .info-label {
          opacity: 0.7;
        }

        .info-value {
          font-weight: 500;
          color: var(--text-primary);
        }
      }
    }

    .footer-credits {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      text-align: right;

      @media (max-width: 768px) {
        text-align: center;
      }

      .credit-item {
        font-size: 0.9rem;
        color: var(--text-secondary);
        opacity: 0.8;

        &:first-child {
          font-weight: 500;
          color: var(--accent-magenta);
          opacity: 0.9;
        }
      }
    }
  }

  /* 特效层 */
  .effects-layer {
    position: absolute;
    inset: 0;
    z-index: 3;
    pointer-events: none;

    .spark {
      position: absolute;
      width: 2px;
      height: 2px;
      background: var(--accent-crimson);
      border-radius: 50%;
      filter: blur(1px);
      opacity: 0.6;
    }
  }
}

/* 动画关键帧 */
@keyframes gradient-shift {
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

@keyframes symbol-pulse {
  0%,
  100% {
    opacity: 0.5;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.2);
  }
}

@keyframes blink {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.3;
  }
}

@keyframes shine {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .florhome {
    .main-content {
      padding: 1rem;

      .title-container {
        .title-wrapper {
          .flor-title {
            .title-main {
              font-size: 3rem;
            }

            .title-sub {
              font-size: 1rem;
            }
          }
        }

        .subtitle-container {
          .subtitle-wrapper {
            font-size: 1.3rem;
            min-height: 3em;
          }
        }
      }
    }

    .flor-footer {
      padding: 1.5rem 1rem;
    }
  }
}

/* 深色模式优化 */
@media (prefers-color-scheme: dark) {
  .florhome {
    background: radial-gradient(circle at 20% 30%, #120612 0%, transparent 50%),
      radial-gradient(circle at 80% 70%, #6c2b4c 0%, transparent 50%),
      linear-gradient(135deg, #04060a 0%, #08060a 100%);
  }
}
</style>