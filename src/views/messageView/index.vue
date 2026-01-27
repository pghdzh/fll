<template>
  <div class="megumi-message-board" aria-live="polite">
    <!-- 背景轮播（两组用于桌面/移动不同裁切） -->
    <div class="carousel carousel1" aria-hidden="true">
      <img
        v-for="(src, idx) in randomFive"
        :key="idx"
        :src="src"
        class="carousel-image"
        :class="{ active: idx === currentIndex }"
      />
    </div>
    <div class="carousel carousel2" aria-hidden="true">
      <img
        v-for="(src, idx) in randomFive2"
        :key="idx"
        :src="src"
        class="carousel-image"
        :class="{ active: idx === currentIndex }"
      />
    </div>
    <!-- 半透明顶部标题 -->
    <header class="board-header" role="banner">
      <div class="title-wrap">
        <h1>留言板</h1>
        <span class="title-count">（共{{ count }}条）</span>

        <p class="subtitle">以你之语，谱我乐章</p>
      </div>
    </header>

    <!-- 留言展示区 -->
    <section class="message-list">
      <transition-group name="msg" tag="div" class="message-list-inner">
        <div v-if="loading" class="skeleton-wrap" key="skeleton">
          <div class="skeleton" v-for="i in 1" :key="i">
            <div class="sk-avatar"></div>
            <div class="sk-lines">
              <div class="sk-line short"></div>
              <div class="sk-line"></div>
            </div>
          </div>
        </div>
        <div
          v-for="(msg, idx) in messages"
          :key="msg.id || msg._tempId || idx"
          class="message-card"
          :data-index="idx"
          tabindex="0"
          role="article"
          :aria-label="`留言来自 ${msg.name || '匿名'}，内容：${msg.content}`"
        >
          <div class="message-meta">
            <div class="left-meta">
              <div class="name-avatar" :title="msg.name || '匿名'">
                {{ getInitial(msg.name) }}
              </div>
              <div class="meta-texts">
                <div class="message-name">{{ msg.name || "匿名" }}</div>
                <div class="message-time">{{ formatTime(msg.created_at) }}</div>
              </div>
            </div>
            <div
              class="shouan-icon"
              role="button"
              tabindex="0"
              aria-label="共鸣之晶"
              aria-pressed="false"
            >
              <svg
                viewBox="0 0 48 48"
                width="36"
                height="36"
                aria-hidden="true"
                focusable="false"
                role="img"
                class="shouan-svg"
              >
                <!-- 背景环（会由 CSS 控制颜色/透明度） -->
                <g
                  class="core-ring"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="0.8"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path
                    d="M24 6 C34 6, 42 14, 42 24 C42 34, 34 42, 24 42 C14 42, 6 34, 6 24 C6 14, 14 6, 24 6 Z"
                  />
                </g>

                <!-- 眼形 + 瞳（核心） -->
                <g
                  class="core-eye"
                  fill="none"
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <path
                    class="eye-outline"
                    d="M6 24 C10 14, 18 10, 24 10 C30 10, 38 14, 42 24 C38 32, 30 36, 24 36 C18 36, 10 32, 6 24 Z"
                    stroke-width="1.0"
                  />
                  <circle
                    class="pupil"
                    cx="24"
                    cy="24"
                    r="3"
                    fill="currentColor"
                    stroke="none"
                  />
                </g>

                <!-- 指挥棒 / 音符 合成体（可单独动画）-->
                <g
                  class="bat-note"
                  transform="translate(0,0)"
                  fill="currentColor"
                  stroke="none"
                >
                  <!-- 指挥棒柄（细长） -->
                  <rect
                    class="bat"
                    x="30.5"
                    y="6.5"
                    width="1.6"
                    height="18.6"
                    rx="0.8"
                    transform="rotate(32 31.3 15.8)"
                  />
                  <!-- 音符头（小圆） -->
                  <ellipse
                    class="note-head"
                    cx="34.6"
                    cy="10.4"
                    rx="2.6"
                    ry="3.0"
                  />
                  <!-- 连接弧（使形状更具动感） -->
                  <path
                    class="note-flag"
                    d="M33 8 C35 6, 38 6.5, 36 9"
                    fill="none"
                    stroke="currentColor"
                    stroke-width="0.9"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  />
                </g>

                <!-- 小星屑 / 飞烬（四个点）-->
                <g
                  class="ember-sparks"
                  fill="currentColor"
                  stroke="none"
                  opacity="0.9"
                >
                  <circle class="s1" cx="8" cy="10" r="0.95" />
                  <circle class="s2" cx="42" cy="14" r="0.8" />
                  <circle class="s3" cx="38" cy="36" r="0.75" />
                  <circle class="s4" cx="10" cy="34" r="0.7" />
                </g>
              </svg>
            </div>
          </div>

          <p class="message-content">{{ msg.content }}</p>
        </div>
      </transition-group>
    </section>

    <!-- 底部发送区 -->
    <section class="message-form" aria-label="写下你的留言">
      <label class="sr-only" for="mb-name">你的昵称</label>
      <input
        id="mb-name"
        v-model="name"
        type="text"
        placeholder="你的昵称"
        @keydown.enter.prevent
      />

      <label class="sr-only" for="mb-content">留言内容</label>
      <textarea
        id="mb-content"
        v-model="content"
        placeholder="写下你的留言..."
        @keydown.ctrl.enter.prevent="submitMessage"
        @input="autoGrow"
        ref="textareaRef"
      />

      <div class="form-row">
        <div class="hint">按 <kbd>Ctrl</kbd> + <kbd>Enter</kbd> 快捷发送</div>
        <button @click="submitMessage" :disabled="isSending || !content.trim()">
          <span v-if="!isSending">发送</span>
          <span v-else>发送中…</span>
        </button>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick, onBeforeUnmount } from "vue";
import { getMessageList, createMessage } from "@/api/modules/message";

const messages = ref<any[]>([]);
const count = ref(0);
const name = ref(localStorage.getItem("message_name") || "");
const content = ref("");
const loading = ref(true);
const isSending = ref(false);

const textareaRef = ref<HTMLTextAreaElement | null>(null);

const fetchMessages = async () => {
  loading.value = true;
  try {
    const res = await getMessageList({ page: 1, pageSize: 9999 });
    messages.value = res.data || [];
    count.value = res.pagination.total;
    await nextTick();
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
};

const submitMessage = async () => {
  if (!content.value.trim() || isSending.value) return;
  isSending.value = true;
  const payload = { name: name.value || "匿名", content: content.value };
  try {
    localStorage.setItem("message_name", name.value);
    content.value = "";
    await nextTick();
    // 发送请求
    await createMessage(payload);
    // 重新同步列表（更可靠）
    await fetchMessages();
  } catch (err) {
    console.error(err);
  } finally {
    isSending.value = false;
  }
};

const formatTime = (time: string) => {
  if (!time) return "";
  const d = new Date(time);
  // 例如：2025-08-11 15:30
  const y = d.getFullYear();
  const m = String(d.getMonth() + 1).padStart(2, "0");
  const day = String(d.getDate()).padStart(2, "0");
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  return `${y}-${m}-${day} ${hh}:${mm}`;
};

const getInitial = (n?: string) => {
  if (!n) return "匿";
  return n.trim().slice(0, 1).toUpperCase();
};

const autoGrow = (e?: Event) => {
  const ta = textareaRef.value;
  if (!ta) return;
  ta.style.height = "auto";
  const h = Math.min(ta.scrollHeight, 220);
  ta.style.height = h + "px";
};

// ========== 背景图片导入与轮播 ==========
const modules = import.meta.glob("@/assets/images1/*.{jpg,png,jpeg,webp}", {
  eager: true,
});
const allSrcs: string[] = Object.values(modules).map((mod: any) => mod.default);

const modules2 = import.meta.glob("@/assets/images2/*.{jpg,png,jpeg,webp}", {
  eager: true,
});
const allSrcs2: string[] = Object.values(modules2).map(
  (mod: any) => mod.default
);

function shuffle<T>(arr: T[]): T[] {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
const randomFive = ref<string[]>(shuffle(allSrcs).slice(0, 5));
const randomFive2 = ref<string[]>(shuffle(allSrcs2).slice(0, 5));

const currentIndex = ref(0);
let Imgtimer: number | undefined;

onMounted(() => {
  fetchMessages();
  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);
  nextTick(() => autoGrow());
});

onBeforeUnmount(() => {
  if (Imgtimer) clearInterval(Imgtimer);
});
</script>

<style scoped lang="scss">
/* ==== 弗洛洛风格配色变量 ==== */

.megumi-message-board {
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
  color: var(--text-primary);
  position: relative;
  overflow-x: hidden;
  padding-top: 100px;
  font-family: "Segoe UI", system-ui, -apple-system, sans-serif;
  -webkit-font-smoothing: antialiased;

 

  

  /* ==== 背景轮播图 ==== */
  .carousel {
    position: fixed;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    
    mix-blend-mode: overlay;

    &::after {
      content: "";
      position: absolute;
      inset: 0;
      background: linear-gradient(
        45deg,
        var(--void-purple) 0%,
        transparent 50%,
        var(--void-purple) 100%
      );
      animation: gradientShift 20s ease-in-out infinite;
    }

    .carousel-image {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
     
      opacity: 0;
      transition: opacity 2s cubic-bezier(0.4, 0, 0.2, 1);
      transform: scale(1.02);

      &.active {
        opacity: 1;
        animation: floatImage 25s ease-in-out infinite;
      }
    }
  }

  .carousel2 {
    display: none;
  }

  /* ==== 页面头部 ==== */
  .board-header {
    position: relative;
    z-index: 10;
    max-width: 800px;
    margin: 0 auto 2rem;
    padding: 1.5rem 2rem;
    background: var(--glass-dark);
    backdrop-filter: blur(20px) saturate(180%);
    border-radius: 24px;
    border: 1px solid var(--border-faint);
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4), 0 8px 32px rgba(0, 0, 0, 0.3),
      inset 0 1px 0 rgba(255, 255, 255, 0.05);

    /* 标题流光效果 */
    &::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 1px;
      background: linear-gradient(
        90deg,
        transparent,
        var(--accent-magenta),
        var(--accent-crimson),
        transparent
      );
      animation: shimmer 4s ease-in-out infinite;
    }

    .title-wrap {
      text-align: center;

      h1 {
        margin: 0;
        font-size: clamp(1.75rem, 4vw, 2.5rem);
        font-weight: 800;
        letter-spacing: -0.02em;
        background: linear-gradient(
          135deg,
          var(--text-primary) 0%,
          var(--accent-magenta) 50%,
          var(--accent-crimson) 100%
        );
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
        text-shadow: 0 0 40px var(--glow-magenta), 0 0 80px var(--glow-crimson);
        animation: textGlow 5s ease-in-out infinite;
      }

      .title-count {
        display: block;
        margin-top: 0.5rem;
        color: var(--text-secondary);
        font-size: 1rem;
        font-weight: 300;
        letter-spacing: 0.05em;
      }

      .subtitle {
        margin-top: 0.75rem;
        color: var(--text-secondary);
        font-size: clamp(0.9rem, 2vw, 1.1rem);
        font-weight: 300;
        letter-spacing: 0.1em;
        text-transform: uppercase;
        opacity: 0.8;
      }
    }
  }

  /* ==== 留言列表区域 ==== */
  .message-list {
    position: relative;
    z-index: 10;
    max-width: 800px;
    margin: 0 auto 3rem;
    padding: 0 1rem;
    padding-bottom: 160px;
    .message-list-inner {
      display: grid;
      gap: 1.25rem;
      list-style: none;
      padding: 0;
      margin: 0;
    }

    /* 骨架屏加载状态 */
    .skeleton-wrap {
      display: grid;
      gap: 1rem;

      .skeleton {
        background: var(--glass-light);
        backdrop-filter: blur(10px);
        border-radius: 20px;
        border: 1px solid var(--border-faint);
        padding: 1.5rem;
        display: flex;
        gap: 1rem;
        align-items: center;

        .sk-avatar {
          width: 48px;
          height: 48px;
          border-radius: 12px;
          background: linear-gradient(
            135deg,
            var(--accent-magenta) 0%,
            var(--accent-crimson) 100%
          );
          opacity: 0.5;
          animation: pulse 2s ease-in-out infinite;
        }

        .sk-lines {
          flex: 1;

          .sk-line {
            height: 12px;
            border-radius: 6px;
            background: rgba(255, 255, 255, 0.05);
            margin-bottom: 0.75rem;

            &.short {
              width: 40%;
            }

            &:last-child {
              margin-bottom: 0;
            }
          }
        }
      }
    }

    /* 单条留言卡片 */
    .message-card {
      background: var(--glass-light);
      backdrop-filter: blur(20px) saturate(180%);
      border-radius: 20px;
      border: 1px solid var(--border-faint);
      padding: 1.5rem;
      transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
      position: relative;
      overflow: hidden;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3),
        inset 0 1px 0 rgba(255, 255, 255, 0.05);

      /* 悬停流光效果 */
      &::before {
        content: "";
        position: absolute;
        top: 0;
        left: -100%;
        width: 100%;
        height: 100%;
        background: linear-gradient(
          90deg,
          transparent,
          rgba(184, 107, 224, 0.1),
          transparent
        );
        transition: left 0.8s ease;
      }

      &:hover {
        transform: translateY(-8px);
        border-color: var(--accent-magenta);
        box-shadow: 0 30px 80px var(--glow-magenta),
          0 20px 60px rgba(0, 0, 0, 0.4);

        &::before {
          left: 100%;
        }

        .message-name {
          color: var(--accent-crimson);
        }

        .shouan-icon {
          transform: rotate(15deg);
          box-shadow: 0 15px 40px var(--glow-magenta),
            0 10px 30px var(--glow-crimson);
        }
      }

      .message-meta {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        gap: 1.5rem;
        margin-bottom: 1rem;

        .left-meta {
          display: flex;
          gap: 1rem;
          align-items: center;

          .name-avatar {
            width: 56px;
            height: 56px;
            border-radius: 14px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 800;
            font-size: 1.5rem;
            color: var(--text-primary);
            background: linear-gradient(
              135deg,
              var(--accent-magenta) 0%,
              var(--accent-crimson) 100%
            );
            box-shadow: 0 10px 30px rgba(184, 107, 224, 0.2),
              inset 0 1px 0 rgba(255, 255, 255, 0.1);
            position: relative;
            overflow: hidden;

            /* 头像微光效 */
            &::after {
              content: "";
              position: absolute;
              inset: 0;
              background: radial-gradient(
                circle at 30% 30%,
                rgba(255, 255, 255, 0.1) 0%,
                transparent 70%
              );
            }
          }

          .meta-texts {
            .message-name {
              font-size: 1.1rem;
              font-weight: 600;
              color: var(--text-primary);
              margin-bottom: 0.25rem;
              transition: color 0.3s ease;
            }

            .message-time {
              font-size: 0.85rem;
              color: var(--text-secondary);
              font-family: "Monaco", "Consolas", monospace;
            }
          }
        }

        /* 共鸣之晶图标 */
        .shouan-icon {
          display: flex;
          align-items: center;
          justify-content: center;
          width: 60px;
          height: 60px;
          border-radius: 16px;
          background: var(--glass-dark);
          backdrop-filter: blur(10px);
          border: 1px solid var(--border-faint);
          cursor: pointer;
          transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
          position: relative;
          overflow: hidden;

          /* 图标内部光晕 */
          &::before {
            content: "";
            position: absolute;
            inset: 0;
            background: radial-gradient(
              circle at center,
              var(--glow-magenta) 0%,
              transparent 70%
            );
            opacity: 0;
            transition: opacity 0.3s ease;
          }

          &:hover {
            &::before {
              opacity: 0.3;
            }

            svg {
              transform: scale(1.1);
              filter: drop-shadow(0 0 20px var(--glow-crimson));

              .bat-note {
                animation: batonSwing 1s ease-in-out infinite;
              }

              .ember-sparks circle {
                animation: sparkle 1.5s ease-in-out infinite;
              }
            }
          }

          svg {
            width: 36px;
            height: 36px;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            z-index: 1;

            .core-ring {
              stroke: var(--accent-magenta);
              opacity: 0.8;
              animation: rotateRing 20s linear infinite;
            }

            .core-eye {
              stroke: var(--accent-crimson);

              .pupil {
                fill: var(--accent-crimson);
                animation: eyePulse 3s ease-in-out infinite;
              }
            }

            .bat-note {
              color: var(--accent-crimson);
              transform-origin: 31.3px 15.8px;
            }

            .ember-sparks {
              circle {
                fill: var(--accent-crimson);

                &.s1 {
                  animation-delay: 0s;
                }
                &.s2 {
                  animation-delay: 0.2s;
                }
                &.s3 {
                  animation-delay: 0.4s;
                }
                &.s4 {
                  animation-delay: 0.6s;
                }
              }
            }
          }
        }
      }

      .message-content {
        font-size: 1.05rem;
        line-height: 1.6;
        color: var(--text-primary);
        margin: 0;
        white-space: pre-wrap;
        word-break: break-word;
      }
    }
  }

  
  /* ==== 底部发送表单 ==== */
  .message-form {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 100;
    padding: 1.5rem;
    background: var(--glass-dark);
    backdrop-filter: blur(30px);
    border-top: 1px solid var(--border-faint);
    box-shadow: 0 -20px 60px rgba(0, 0, 0, 0.5),
      0 -10px 30px rgba(184, 107, 224, 0.1);

    /* 修正：直接设置表单容器布局，移除错误的 > div 选择器 */
    display: grid;
    grid-template-columns: 1fr 2fr 1fr;
    gap: 1rem;
    max-width: 1200px;
    margin: 0 auto;
    align-items: start;

    /* 移动端适配 */
    @media (max-width: 768px) {
      grid-template-columns: 1fr;
      padding: 1rem;
      gap: 0.75rem;
    }

    /* 隐藏辅助标签 - 确保sr-only类正确应用 */
    .sr-only {
      position: absolute;
      width: 1px;
      height: 1px;
      padding: 0;
      margin: -1px;
      overflow: hidden;
      clip: rect(0, 0, 0, 0);
      white-space: nowrap;
      border: 0;
    }

    /* 昵称输入框 - 第一列 */
    #mb-name {
      grid-column: 1 / 2;
      padding: 1rem 1.25rem;
      border-radius: 12px;
      border: 1px solid var(--border-faint);
      background: rgba(10, 8, 16, 0.4);
      color: var(--text-primary);
      font-size: 1rem;
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
      width: 100%;
      box-sizing: border-box;

      &::placeholder {
        color: var(--text-secondary);
        opacity: 0.6;
      }

      &:focus {
        outline: none;
        border-color: var(--accent-magenta);
        box-shadow: 0 0 0 3px var(--glow-magenta),
          0 10px 30px rgba(184, 107, 224, 0.2);
        transform: translateY(-2px);
      }

      &:hover {
        border-color: rgba(184, 107, 224, 0.3);
      }

      /* 移动端适配 */
      @media (max-width: 768px) {
        grid-column: 1 / -1;
      }
    }

    /* 内容输入框 - 第二列 */
    #mb-content {
      grid-column: 2 / 3;
      padding: 1rem 1.25rem;
      border-radius: 12px;
      border: 1px solid var(--border-faint);
      background: rgba(10, 8, 16, 0.4);
      color: var(--text-primary);
      font-size: 1rem;
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
      resize: vertical;
      width: 100%;
      box-sizing: border-box;
      min-height: 100px;
      max-height: 200px;
      line-height: 1.6;

      &::placeholder {
        color: var(--text-secondary);
        opacity: 0.6;
      }

      &:focus {
        outline: none;
        border-color: var(--accent-magenta);
        box-shadow: 0 0 0 3px var(--glow-magenta),
          0 10px 30px rgba(184, 107, 224, 0.2);
        transform: translateY(-2px);
      }

      &:hover {
        border-color: rgba(184, 107, 224, 0.3);
      }

      /* 移动端适配 */
      @media (max-width: 768px) {
        grid-column: 1 / -1;
        min-height: 80px;
      }
    }

    /* 操作区域 - 第三列（PC端）或独立行（移动端） */
    .form-row {
      grid-column: 3 / 4;
      display: flex;
      flex-direction: column;
      gap: 1rem;
      align-items: flex-end;
      justify-content: space-between;
      height: 100%;
      min-height: 100px;

      @media (max-width: 768px) {
        grid-column: 1 / -1;
        flex-direction: row;
        align-items: center;
        min-height: auto;
        margin-top: 0.5rem;
      }

      .hint {
        color: var(--text-secondary);
        font-size: 0.9rem;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        flex-wrap: wrap;

        kbd {
          padding: 0.25rem 0.5rem;
          border-radius: 6px;
          background: rgba(184, 107, 224, 0.1);
          border: 1px solid var(--border-faint);
          font-family: "Monaco", "Consolas", monospace;
          font-size: 0.85rem;
          color: var(--accent-magenta);
          white-space: nowrap;
        }
      }

      button {
        padding: 0.875rem 1.75rem;
        border-radius: 12px;
        border: none;
        background: linear-gradient(
          135deg,
          var(--accent-magenta) 0%,
          var(--accent-crimson) 100%
        );
        color: var(--text-primary);
        font-size: 1rem;
        font-weight: 600;
        letter-spacing: 0.05em;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        position: relative;
        overflow: hidden;
        white-space: nowrap;
        min-width: 120px;

        &::before {
          content: "";
          position: absolute;
          inset: 0;
          background: linear-gradient(
            135deg,
            var(--accent-crimson) 0%,
            var(--accent-magenta) 100%
          );
          opacity: 0;
          transition: opacity 0.3s ease;
        }

        &:hover:not(:disabled) {
          transform: translateY(-3px);
          box-shadow: 0 15px 40px var(--glow-magenta),
            0 10px 30px var(--glow-crimson);

          &::before {
            opacity: 1;
          }
        }

        &:active:not(:disabled) {
          transform: translateY(-1px);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
          transform: none !important;
          box-shadow: none !important;
          background: linear-gradient(
            135deg,
            rgba(184, 107, 224, 0.5) 0%,
            rgba(255, 107, 133, 0.5) 100%
          );
        }

        span {
          position: relative;
          z-index: 1;
        }
      }
    }
  }

  /* 过渡动画 */
  .msg-enter-active,
  .msg-leave-active {
    transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .msg-enter-from,
  .msg-leave-to {
    opacity: 0;
    transform: translateY(20px);
  }
}

/* ==== 动画定义 ==== */
@keyframes gradientShift {
  0%,
  100% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
}

@keyframes floatImage {
  0%,
  100% {
    transform: scale(1.02) translateY(0px) rotate(0deg);
  }
  33% {
    transform: scale(1.03) translateY(-10px) rotate(0.5deg);
  }
  66% {
    transform: scale(1.02) translateY(5px) rotate(-0.5deg);
  }
}

@keyframes scanlines {
  from {
    background-position: 0 0;
  }
  to {
    background-position: 0 100px;
  }
}

@keyframes shimmer {
  0%,
  100% {
    opacity: 0.3;
    transform: translateX(-100%);
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.3;
    transform: translateX(100%);
  }
}

@keyframes textGlow {
  0%,
  100% {
    text-shadow: 0 0 40px var(--glow-magenta), 0 0 80px var(--glow-crimson);
  }
  50% {
    text-shadow: 0 0 60px var(--glow-magenta), 0 0 120px var(--glow-crimson),
      0 0 160px rgba(255, 107, 133, 0.3);
  }
}

@keyframes pulse {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 0.8;
  }
}

@keyframes rotateRing {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

@keyframes eyePulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
}

@keyframes batonSwing {
  0%,
  100% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(-10deg);
  }
  75% {
    transform: rotate(10deg);
  }
}

@keyframes sparkle {
  0%,
  100% {
    opacity: 0.3;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.5);
  }
}

/* ==== 响应式设计 ==== */
@media (max-width: 768px) {
  .megumi-message-board {
    padding-top: 80px;
    background: var(--mobile-bg);

    .carousel1 {
      display: none;
    }

    .carousel2 {
      display: block;
    }

    .board-header {
      margin: 0 1rem 1.5rem;
      padding: 1.25rem;
      border-radius: 16px;
    }

    .message-list {
      padding: 0 1rem 1.5rem;

      .message-card {
        padding: 1.25rem;

        .message-meta {
         
          gap: 1rem;

          .left-meta {
            .name-avatar {
              width: 48px;
              height: 48px;
              font-size: 1.25rem;
            }
          }

          .shouan-icon {
            align-self: flex-end;
            width: 52px;
            height: 52px;

            svg {
              width: 32px;
              height: 32px;
            }
          }
        }
      }
    }

  }
}
</style>