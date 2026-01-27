<template>
  <div class="chat-page">
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

    <div class="chat-container">
      <!-- 统计面板（放在聊天容器顶部） -->
      <div class="stats-panel">
        <div class="stat-item">
          总对话次数：<span>{{ stats.totalChats }}</span>
        </div>
        <div class="stat-item">
          首次使用：<span>{{
            new Date(stats.firstTimestamp).toISOString().slice(0, 10)
          }}</span>
        </div>
        <div class="stat-item">
          活跃天数：<span>{{ stats.activeDates.length }}</span> 天
        </div>
        <div class="stat-item">
          今日对话：<span>{{
            stats.dailyChats[new Date().toISOString().slice(0, 10)] || 0
          }}</span>
          次
        </div>
        <button class="detail-btn" @click="showModal = true">全部</button>
      </div>
      <div class="messages" ref="msgList">
        <transition-group name="msg" tag="div">
          <div
            v-for="msg in chatLog"
            :key="msg.id"
            :class="[
              'message',
              msg.role,
              { error: msg.isError, egg: msg.isEgg },
            ]"
          >
            <div class="avatar" :class="msg.role"></div>
            <div class="bubble">
              <div class="content" v-html="msg.text"></div>
            </div>
          </div>
          <div v-if="loading" class="message bot" key="loading">
            <div class="avatar bot"></div>
            <div class="bubble loading">
              正在思考中
              <span class="dots">
                <span class="dot">.</span>
                <span class="dot">.</span>
                <span class="dot">.</span>
              </span>
            </div>
          </div>
        </transition-group>
      </div>
      <form class="input-area" @submit.prevent="sendMessage">
        <textarea
          v-model="input"
          placeholder="向弗洛洛提问…"
          :disabled="loading"
          @keydown="handleKeydown"
          rows="1"
        ></textarea>

        <div class="btn-group">
          <button
            type="button"
            class="clear-btn"
            @click="clearChat"
            :disabled="loading"
            title="清空对话"
          >
            ✖
          </button>
          <button
            type="button"
            class="copy-btn"
            :disabled="!chatLog.length || loading"
            @click="() => copyChat()"
          >
            {{ copyButtonText }}
          </button>
          <!-- 发送按钮 -->
          <button
            type="submit"
            class="send-btn"
            :disabled="!input.trim() || loading"
          >
            发送
          </button>
          <!-- 统计数据按钮 -->
          <button
            type="button"
            class="Alldetail-btn"
            @click="showModal = true"
            title="查看统计"
          >
            统计数据
          </button>
        </div>
      </form>
    </div>

    <!-- 详细统计弹窗 -->
    <div v-if="showModal" class="modal-overlay" @click.self="showModal = false">
      <div class="modal-content">
        <h3>详细统计</h3>
        <ul class="detail-list">
          <li>总对话次数：{{ stats.totalChats }}</li>
          <li>
            首次使用：{{
              new Date(stats.firstTimestamp).toISOString().slice(0, 10)
            }}
          </li>
          <li>活跃天数：{{ stats.activeDates.length }} 天</li>
          <li>
            今日对话：{{
              stats.dailyChats[new Date().toISOString().slice(0, 10)] || 0
            }}
            次
          </li>
          <li>总使用时长：{{ formatDuration(stats.totalTime) }}</li>
          <li>当前连续活跃：{{ stats.currentStreak }} 天</li>
          <li>最长连续活跃：{{ stats.longestStreak }} 天</li>
          <li>
            最活跃日：{{ mostActiveDayComputed }} （{{
              stats.dailyChats[mostActiveDayComputed] || 0
            }}
            次）
          </li>
        </ul>
        <button class="close-btn" @click="showModal = false">关闭</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  reactive,
  ref,
  computed,
  onMounted,
  nextTick,
  watch,
  onBeforeUnmount,
} from "vue";
import { sendMessageToHui } from "@/api/deepseekApi";

const STORAGE_KEY = "hui_chat_log";

// 本地存储键名
const STORAGE_STATS_KEY = "hui_chat_stats";
const showModal = ref(false);
// Stats 类型声明，确保所有字段都有默认值
interface Stats {
  firstTimestamp: number; // 首次使用时间戳
  totalChats: number; // 总对话次数
  activeDates: string[]; // 有发言的日期列表（yyyy‑mm‑dd）
  dailyChats: Record<string, number>; // 每日对话次数
  currentStreak: number; // 当前连续活跃天数
  longestStreak: number; // 历史最长连续活跃天数

  totalTime: number; // 累计使用时长（毫秒）
}

// 默认值，用于补齐本地存储中可能缺失的字段
const defaultStats: Stats = {
  firstTimestamp: Date.now(),
  totalChats: 0,
  activeDates: [],
  dailyChats: {},
  currentStreak: 0,
  longestStreak: 0,

  totalTime: 0,
};

// 从 localStorage 加载并合并默认值
function loadStats(): Stats {
  const saved = localStorage.getItem(STORAGE_STATS_KEY);
  if (saved) {
    try {
      const parsed = JSON.parse(saved);
      return { ...defaultStats, ...parsed };
    } catch {
      console.warn("加载统计数据失败，使用默认值");
    }
  }
  return { ...defaultStats };
}

// 保存到 localStorage
function saveStats() {
  localStorage.setItem(STORAGE_STATS_KEY, JSON.stringify(stats));
}

// 更新「活跃天数」及「连续活跃」逻辑
function updateActive(date: string) {
  if (!stats.activeDates.includes(date)) {
    stats.activeDates.push(date);
    updateStreak();
    saveStats(); // 持久化活跃天数变化
  }
}
function updateStreak() {
  const dates = [...stats.activeDates].sort();
  let curr = 0,
    max = stats.longestStreak,
    prevTs = 0;
  const todayStr = new Date().toISOString().slice(0, 10);
  dates.forEach((d) => {
    const ts = new Date(d).getTime();
    if (prevTs && ts - prevTs === 86400000) curr++;
    else curr = 1;
    max = Math.max(max, curr);
    prevTs = ts;
  });
  stats.currentStreak = dates[dates.length - 1] === todayStr ? curr : 0;
  stats.longestStreak = max;
  saveStats();
}

// 更新「每日对话次数」
function updateDaily(date: string) {
  stats.dailyChats[date] = (stats.dailyChats[date] || 0) + 1;
  saveStats(); // 持久化活跃天数变化
}

// 计算最活跃日
const mostActiveDayComputed = computed(() => {
  let day = "",
    max = 0;
  for (const [d, c] of Object.entries(stats.dailyChats)) {
    if (c > max) {
      max = c;
      day = d;
    }
  }
  return day || new Date().toISOString().slice(0, 10);
});

// 格式化总使用时长
function formatDuration(ms: number): string {
  const totalMin = Math.floor(ms / 60000);
  const h = Math.floor(totalMin / 60);
  const m = totalMin % 60;
  return h ? `${h} 小时 ${m} 分钟` : `${m} 分钟`;
}

// —— Vue 响应式状态 ——
const stats = reactive<Stats>(loadStats());
// 会话开始时间，用于计算本次时长
const sessionStart = Date.now();

interface ChatMsg {
  id: number;
  role: "user" | "bot";
  text: string;
  isError?: boolean;
  isEgg?: boolean;
}

const chatLog = ref<ChatMsg[]>(loadChatLog());
const input = ref("");
const loading = ref(false);
const msgList = ref<HTMLElement>();

async function sendMessage() {
  if (!input.value.trim()) return;
  if (stats.totalChats === 0 && !localStorage.getItem(STORAGE_STATS_KEY)) {
    stats.firstTimestamp = Date.now();
    saveStats();
  }
  const date = new Date().toISOString().slice(0, 10); // 每次都取最新“今天”
  stats.totalChats++;
  updateActive(date);
  updateDaily(date);
  saveStats();

  const userText = input.value;
  chatLog.value.push({
    id: Date.now(),
    role: "user",
    text: userText,
  });
  input.value = "";
  loading.value = true;

  try {
    //  throw new Error("测试错误");
    const history = chatLog.value.filter((msg) => !msg.isEgg && !msg.isError);
    const botReply = await sendMessageToHui(userText, history);
    if (botReply == "error") {
      chatLog.value.push({
        id: Date.now() + 2,
        role: "bot",
        text: "API余额耗尽了，去b站提醒我充钱吧",
        isError: true,
      });
    } else {
      chatLog.value.push({
        id: Date.now() + 1,
        role: "bot",
        text: botReply,
      });
    }

    // —— 彩蛋结束 ——
  } catch (e) {
    console.error(e);
  } finally {
    loading.value = false;
    await scrollToBottom();
  }
}

function handleKeydown(e: KeyboardEvent) {
  if (e.key === "Enter") sendMessage();
}

function clearChat() {
  if (confirm("确定要清空全部对话吗？")) {
    chatLog.value = [
      {
        id: Date.now(),
        role: "bot",
        text: "……",
      },
    ];
    localStorage.removeItem(STORAGE_KEY);
  }
}

function loadChatLog(): ChatMsg[] {
  const saved = localStorage.getItem(STORAGE_KEY);
  if (saved) {
    try {
      return JSON.parse(saved);
    } catch (e) {
      console.error("chatLog 解析失败：", e);
    }
  }
  return [
    {
      id: Date.now(),
      role: "bot",
      text: "……",
    },
  ];
}

async function scrollToBottom() {
  await nextTick();
  if (msgList.value) {
    msgList.value.scrollTop = msgList.value.scrollHeight;
  }
}

//复制文本相关
// 复制按钮文字与定时器（加入到 script setup）
const copyButtonText = ref("复制");
let _copyTimer: number | null = null;

// 把 HTML 文本转为纯文本（保留 <br> 换行）
function stripHtml(html = ""): string {
  if (typeof document === "undefined") {
    // SSR 安全返回
    return html.replace(/<br\s*\/?>/gi, "\n").replace(/<[^>]+>/g, "");
  }
  const div = document.createElement("div");
  const withBreaks = String(html).replace(/<br\s*\/?>/gi, "\n");
  div.innerHTML = withBreaks;
  return div.textContent || div.innerText || "";
}

// 根据 chatLog.build 出可读的纯文本（含时间戳）
function buildChatPlainText(): string {
  // chatLog 是你已有的 ref<ChatMsg[]>
  return chatLog.value
    .map((msg) => {
      // 尝试把 msg.id 当作时间戳（你的代码里确实用 Date.now() 作为 id）
      const time = new Date(msg.id).toLocaleString();
      const who = msg.role === "user" ? "你" : "弗洛洛";
      const text = stripHtml(msg.text);
      return `[${time}] ${who}: ${text}`;
    })
    .join("\n\n");
}

// 回退复制：execCommand
function fallbackCopyText(text: string) {
  const textarea = document.createElement("textarea");
  textarea.value = text;
  textarea.style.position = "fixed"; // 防止滚动到页面底部
  textarea.style.left = "-9999px";
  textarea.style.top = "0";
  document.body.appendChild(textarea);
  textarea.select();
  textarea.setSelectionRange(0, textarea.value.length);
  const ok = document.execCommand("copy");
  document.body.removeChild(textarea);
  if (!ok) throw new Error("execCommand copy failed");
}

// 主复制函数（在模板里绑定 @click="copyChat"）
async function copyChat() {
  const text = buildChatPlainText();
  if (!text) {
    copyButtonText.value = "无内容可复制";
    clearTimeout(_copyTimer as number);
    _copyTimer = window.setTimeout(() => (copyButtonText.value = "复制"), 1600);
    return;
  }

  try {
    if (navigator.clipboard && navigator.clipboard.writeText) {
      await navigator.clipboard.writeText(text);
    } else {
      fallbackCopyText(text);
    }
    copyButtonText.value = "已复制";
  } catch (err) {
    try {
      // 再试一次回退方案
      fallbackCopyText(text);
      copyButtonText.value = "已复制";
    } catch (e) {
      console.error("复制失败：", e);
      copyButtonText.value = "复制失败";
    }
  } finally {
    clearTimeout(_copyTimer as number);
    _copyTimer = window.setTimeout(() => (copyButtonText.value = "复制"), 1600);
  }
}

watch(
  chatLog,
  async () => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(chatLog.value));
    await scrollToBottom();
  },
  { deep: true }
);

function handleBeforeUnload() {
  stats.totalTime += Date.now() - sessionStart;
  saveStats();
}

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
  scrollToBottom();
  window.addEventListener("beforeunload", handleBeforeUnload);
  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);
});

onBeforeUnmount(() => {
  window.removeEventListener("beforeunload", handleBeforeUnload);
  if (Imgtimer) clearInterval(Imgtimer);
  if (_copyTimer) clearTimeout(_copyTimer);
});
</script>

<style scoped lang="scss">
/* ==== 弗洛洛风格配色变量 ==== */

.chat-page {
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

  &::before {
    content: "";
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      90deg,
      transparent 0px,
      transparent 1px,
      rgba(184, 107, 224, 0.02) 1px,
      rgba(184, 107, 224, 0.02) 2px
    );
    pointer-events: none;
    z-index: 0;
    animation: scanlines 20s linear infinite;
  }

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
      filter: blur(1px) brightness(0.6) contrast(1.1) saturate(1.2);
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

  /* ==== 聊天容器 ==== */
  .chat-container {
    position: relative;
    z-index: 10;
    max-width: 900px;
    margin: 0 auto;
    padding: 0 1rem 120px;
    display: flex;
    flex-direction: column;
    height: calc(100vh - 120px);

    /* 统计面板 */
    .stats-panel {
      background: var(--glass-dark);
      backdrop-filter: blur(20px) saturate(180%);
      border-radius: 20px;
      border: 1px solid var(--border-faint);
      padding: 1rem 1.5rem;
      margin-bottom: 1.5rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 1rem;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4),
        inset 0 1px 0 rgba(255, 255, 255, 0.05);

      .stat-item {
        font-size: 0.95rem;
        color: var(--text-secondary);
        display: flex;
        align-items: center;
        gap: 0.5rem;

        span {
          color: var(--text-primary);
          font-weight: 700;
          font-size: 1.1rem;
          background: linear-gradient(
            135deg,
            var(--accent-magenta) 0%,
            var(--accent-crimson) 100%
          );
          -webkit-background-clip: text;
          -webkit-text-fill-color: transparent;
          background-clip: text;
        }
      }

      .detail-btn {
        padding: 0.5rem 1rem;
        border-radius: 10px;
        border: 1px solid var(--border-faint);
        background: rgba(184, 107, 224, 0.1);
        color: var(--text-primary);
        font-size: 0.9rem;
        font-weight: 500;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

        &:hover {
          background: rgba(184, 107, 224, 0.2);
          border-color: var(--accent-magenta);
          transform: translateY(-2px);
          box-shadow: 0 10px 30px var(--glow-magenta);
        }
      }
    }

    /* 消息列表 */
    .messages {
      flex: 1;
      overflow-y: auto;
      padding: 1rem 0.5rem;
      display: flex;
      flex-direction: column;
      gap: 1.25rem;
      scroll-behavior: smooth;

      /* 消息过渡动画 */
      .msg-enter-active,
      .msg-leave-active {
        transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
      }

      .msg-enter-from,
      .msg-leave-to {
        opacity: 0;
        transform: translateY(20px);
      }

      /* 消息项 */
      .message {
        display: flex;
        gap: 1rem;
        max-width: 85%;
        animation: messageSlide 0.4s cubic-bezier(0.4, 0, 0.2, 1);

        &.user {
          align-self: flex-end;
          flex-direction: row-reverse;

          .avatar {
            background: linear-gradient(
              135deg,
              var(--accent-crimson) 0%,
              var(--accent-magenta) 100%
            );
          }

          .bubble {
            background: linear-gradient(
              135deg,
              rgba(184, 107, 224, 0.15) 0%,
              rgba(255, 107, 133, 0.1) 100%
            );
            border-radius: 20px 20px 6px 20px;
            border-color: rgba(184, 107, 224, 0.2);

            &::before {
              right: -8px;
              left: auto;
              border-width: 8px 0 8px 8px;
              border-color: transparent transparent transparent
                rgba(184, 107, 224, 0.15);
            }
          }
        }

        &.bot {
          align-self: flex-start;

          .avatar {
            background: url("@/assets/avatar/changli.png");
            background-size: cover;
            background-position: center;
          }

          .bubble {
            background: linear-gradient(
              135deg,
              rgba(30, 20, 40, 0.3) 0%,
              rgba(10, 8, 16, 0.4) 100%
            );
            border-radius: 20px 20px 20px 6px;
            border-color: rgba(255, 107, 133, 0.2);

            &::before {
              left: -8px;
              border-width: 8px 8px 8px 0;
              border-color: transparent rgba(255, 107, 133, 0.15) transparent
                transparent;
            }
          }

          &.loading .bubble {
            .dots {
              display: inline-flex;
              margin-left: 0.5rem;

              .dot {
                width: 4px;
                height: 4px;
                border-radius: 50%;
                background: var(--accent-crimson);
                margin: 0 2px;
                animation: loadingDots 1.4s ease-in-out infinite;

                &:nth-child(2) {
                  animation-delay: 0.2s;
                }
                &:nth-child(3) {
                  animation-delay: 0.4s;
                }
              }
            }
          }
        }

        &.error .bubble {
          background: linear-gradient(
            135deg,
            rgba(140, 60, 96, 0.2) 0%,
            rgba(255, 107, 133, 0.15) 100%
          );
          border-color: var(--accent-crimson);
        }

        &.egg .bubble {
          background: linear-gradient(
            135deg,
            rgba(184, 107, 224, 0.25) 0%,
            rgba(255, 107, 133, 0.2) 100%
          );
          border-color: var(--accent-magenta);
          animation: eggGlow 2s ease-in-out infinite;
        }

        .avatar {
          width: 48px;
          height: 48px;
          border-radius: 50%;
          flex-shrink: 0;
          display: flex;
          align-items: center;
          justify-content: center;
          color: var(--text-primary);
          font-weight: 700;
          font-size: 1.2rem;
          box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3),
            inset 0 1px 0 rgba(255, 255, 255, 0.1);
          position: relative;
          overflow: hidden;

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

        .bubble {
          padding: 1rem 1.25rem;
          background: var(--glass-light);
          backdrop-filter: blur(10px);
          border-radius: 20px;
          border: 1px solid var(--border-faint);
          position: relative;
          box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3),
            inset 0 1px 0 rgba(255, 255, 255, 0.05);
          transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

          &::before {
            content: "";
            position: absolute;
            top: 20px;
            width: 0;
            height: 0;
            border-style: solid;
          }

          &:hover {
            transform: translateY(-2px);
            box-shadow: 0 30px 80px var(--glow-magenta),
              0 20px 60px rgba(0, 0, 0, 0.4);
          }

          .content {
            font-size: 1.05rem;
            line-height: 1.6;
            color: var(--text-primary);
            white-space: pre-wrap;
            word-break: break-word;
          }
        }
      }
    }

    /* 输入区域 */
    .input-area {
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
        0 -10px 30px var(--glow-magenta);

      > div {
        max-width: 900px;
        margin: 0 auto;
        display: grid;

        gap: 1rem;
        align-items: center;

        @media (max-width: 768px) {
          grid-template-columns: 1fr;
          gap: 0.75rem;
        }
      }

      textarea {
        grid-column: 1 / 2;
        padding: 1rem 1.25rem;
        border-radius: 12px;
        border: 1px solid var(--border-faint);
        background: rgba(10, 8, 16, 0.4);
        color: var(--text-primary);
        font-size: 1rem;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        resize: none;
        min-height: 60px;
        max-height: 200px;
        line-height: 1.6;
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
        }

        &:hover {
          border-color: rgba(184, 107, 224, 0.3);
        }

        @media (max-width: 768px) {
          grid-column: 1 / -1;
        }
      }

      .clear-btn {
        grid-column: 2 / 3;
        padding: 0.75rem 1.5rem;
        border-radius: 12px;
        border: 1px solid var(--border-faint);
        background: rgba(140, 60, 96, 0.2);
        color: var(--text-primary);
        font-size: 1rem;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        white-space: nowrap;

        &:hover:not(:disabled) {
          background: rgba(140, 60, 96, 0.3);
          border-color: var(--accent-deep);
          transform: translateY(-2px);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
        }

        @media (max-width: 768px) {
          grid-column: 1 / -1;
          width: 100%;
        }
      }

      .copy-btn {
        grid-column: 3 / 4;
        padding: 0.75rem 1.5rem;
        border-radius: 12px;
        border: 1px solid var(--border-faint);
        background: rgba(184, 107, 224, 0.1);
        color: var(--text-primary);
        font-size: 1rem;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        white-space: nowrap;

        &:hover:not(:disabled) {
          background: rgba(184, 107, 224, 0.2);
          border-color: var(--accent-magenta);
          transform: translateY(-2px);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
        }

        @media (max-width: 768px) {
          grid-column: 1 / -1;
          width: 100%;
        }
      }

      .send-btn {
        grid-column: 4 / 5;
        padding: 0.75rem 1.75rem;
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
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        white-space: nowrap;

        &:hover:not(:disabled) {
          transform: translateY(-3px);
          box-shadow: 0 15px 40px var(--glow-magenta),
            0 10px 30px var(--glow-crimson);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
          background: linear-gradient(
            135deg,
            rgba(184, 107, 224, 0.5) 0%,
            rgba(255, 107, 133, 0.5) 100%
          );
        }

        @media (max-width: 768px) {
          grid-column: 1 / -1;
          width: 100%;
        }
      }

      .Alldetail-btn {
        grid-column: 5 / 6;
        padding: 0.75rem 1.5rem;
        border-radius: 12px;
        border: 1px solid var(--border-faint);
        background: rgba(255, 107, 133, 0.1);
        color: var(--text-primary);
        font-size: 1rem;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        white-space: nowrap;

        &:hover:not(:disabled) {
          background: rgba(255, 107, 133, 0.2);
          border-color: var(--accent-crimson);
          transform: translateY(-2px);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
        }

        @media (max-width: 768px) {
          grid-column: 1 / -1;
          width: 100%;
        }
      }
    }
  }

  /* ==== 模态框 ==== */
  .modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(6, 8, 12, 0.8);
    backdrop-filter: blur(10px);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
    padding: 1rem;

    .modal-content {
      width: min(500px, 100%);
      max-height: 80vh;
      overflow-y: auto;
      background: var(--glass-dark);
      backdrop-filter: blur(30px);
      border-radius: 24px;
      border: 1px solid var(--border-faint);
      padding: 2rem;
      box-shadow: 0 40px 100px rgba(0, 0, 0, 0.5),
        0 20px 60px var(--glow-magenta);

      h3 {
        margin: 0 0 1.5rem 0;
        font-size: 1.5rem;
        font-weight: 700;
        text-align: center;
        background: linear-gradient(
          135deg,
          var(--text-primary) 0%,
          var(--accent-magenta) 100%
        );
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
      }

      .detail-list {
        list-style: none;
        padding: 0;
        margin: 0 0 2rem 0;

        li {
          padding: 0.75rem 0;
          border-bottom: 1px solid var(--border-faint);
          color: var(--text-primary);
          font-size: 1rem;
          display: flex;
          justify-content: space-between;
          align-items: center;

          &:last-child {
            border-bottom: none;
          }
        }
      }

      .close-btn {
        width: 100%;
        padding: 1rem;
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
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

        &:hover {
          transform: translateY(-2px);
          box-shadow: 0 15px 40px var(--glow-magenta),
            0 10px 30px var(--glow-crimson);
        }
      }
    }
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

@keyframes messageSlide {
  from {
    opacity: 0;
    transform: translateY(20px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes loadingDots {
  0%,
  100% {
    opacity: 0.3;
    transform: translateY(0);
  }
  50% {
    opacity: 1;
    transform: translateY(-4px);
  }
}

@keyframes eggGlow {
  0%,
  100% {
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3),
      inset 0 1px 0 rgba(255, 255, 255, 0.05);
  }
  50% {
    box-shadow: 0 30px 80px var(--glow-magenta), 0 20px 60px rgba(0, 0, 0, 0.4),
      inset 0 1px 0 rgba(255, 255, 255, 0.1);
  }
}

/* ==== 响应式设计 ==== */
@media (max-width: 768px) {
  .chat-page {
    padding-top: 80px;
    background: var(--mobile-bg);

    .carousel1 {
      display: none;
    }

    .carousel2 {
      display: block;
    }

    .chat-container {
      padding: 0 0.5rem 100px;

      .stats-panel {
        display: none;
      }

      .messages {
        padding: 0.5rem 0;

        .message {
          max-width: 90%;

          .avatar {
            width: 40px;
            height: 40px;
            font-size: 1rem;
          }

          .bubble {
            padding: 0.75rem 1rem;

            .content {
              font-size: 0.95rem;
            }
          }
        }
      }
    }

    .modal-overlay {
      .modal-content {
        padding: 1.5rem;

        h3 {
          font-size: 1.25rem;
          margin-bottom: 1rem;
        }

        .detail-list li {
          font-size: 0.9rem;
          padding: 0.5rem 0;
        }

        .close-btn {
          padding: 0.75rem;
        }
      }
    }
  }
}
</style>