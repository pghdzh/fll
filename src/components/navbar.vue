<template>
  <header class="app-header">
    <!-- 背景音效波纹 -->
    <div class="sound-waves" :class="{ 'waves-active': isPlaying }">
      <div class="wave wave-1"></div>
      <div class="wave wave-2"></div>
      <div class="wave wave-3"></div>
      <div class="wave wave-4"></div>
    </div>

    <!-- 移动端顶部栏 -->
    <div class="mobile-top-bar" v-if="isMobile">
      <div class="mobile-left">
        <div class="mobile-logo">
          <span class="logo-icon">♪</span>
          <span class="logo-text">声骸共鸣</span>
        </div>
        <div class="mobile-online" v-if="onlineCount !== null">
          <span class="online-dot"></span>
          <span class="online-text">{{ onlineCount }}人</span>
        </div>
      </div>

      <div class="mobile-right">
        <!-- 移动端音乐控制 -->
        <div
          class="mobile-music-control"
          @click="toggleMusic"
          :class="{ playing: isPlaying }"
        >
          <div class="mobile-music-icon">
            <div class="mobile-note">♪</div>
          </div>
        </div>

        <!-- 汉堡按钮 -->
        <button
          class="mobile-hamburger"
          @click="toggleMobileNav"
          aria-label="Toggle navigation"
          :class="{ open: mobileNavOpen }"
        >
          <span class="hamburger-line"></span>
          <span class="hamburger-line"></span>
          <span class="hamburger-line"></span>
        </button>
      </div>
    </div>

    <!-- PC端头部 -->
    <div class="desktop-header" v-if="!isMobile">
      <!-- 左侧：标题和在线人数 -->
      <div class="header-left">
        <!-- 标题区域 -->
        <div class="title-container">
          <div class="title-glow"></div>
          <h1 class="title">
            <span class="title-text">弗洛洛电子设定集</span>
            <span class="title-note">♪</span>
          </h1>
          <div class="title-sub">PHROLOVA's Resonance Archive</div>
        </div>

        <!-- 在线人数展示 -->
        <div class="online-count" v-if="onlineCount !== null">
          <div class="count-glow"></div>
          <div class="count-icon">
            <div class="pulse-dot"></div>
            <span class="note-icon">♫</span>
          </div>
          <div class="count-content">
            <div class="count-label">声骸共鸣</div>
            <div class="count-value">
              {{ onlineCount }} <span class="count-unit">人</span>
            </div>
          </div>
          <div
            class="count-ripple"
            :style="{ animationDelay: `${onlineCount % 3}s` }"
          ></div>
        </div>
      </div>

      <!-- 右侧：导航和音乐控制 -->
      <div class="header-right">
        <!-- BGM播放控制 -->
        <div class="music-control" @click="toggleMusic">
          <div class="music-container" :class="{ playing: isPlaying }">
            <div class="music-icon">
              <div class="music-note note-1">♪</div>
              <div class="music-note note-2">♫</div>
              <div class="music-note note-3">♬</div>
              <div class="music-disc"></div>
            </div>
            <div class="music-status">
              <span class="status-text">{{
                isPlaying ? "律动中" : "静默"
              }}</span>
              <span class="status-pulse" :class="{ active: isPlaying }"></span>
            </div>
          </div>
          <div class="music-tooltip">
            {{ isPlaying ? "点击停止声骸共鸣" : "点击唤醒声骸共鸣" }}
          </div>
        </div>

        <!-- 导航菜单 -->
        <nav class="nav-links">
          <div class="nav-items">
            <RouterLink
              v-for="item in mainNavItems"
              :key="item.name"
              :to="item.path"
              class="nav-item"
              active-class="active-link"
            >
              <span class="nav-bg"></span>
              <span class="nav-glow"></span>

              <span class="nav-text">{{ item.name }}</span>
              <span class="nav-sub">{{ item.subtitle }}</span>
              <span class="nav-wave"></span>
              <span class="nav-ripple"></span>
            </RouterLink>

            <!-- PC端下拉菜单 -->
            <div
              class="dropdown-container"
              @mouseenter="showDropdown = true"
              @mouseleave="showDropdown = false"
            >
              <div
                class="nav-item dropdown-trigger"
                :class="{ 'dropdown-open': showDropdown }"
              >
                <span class="nav-bg"></span>
                <span class="nav-glow"></span>

                <span class="nav-text">声骸回响</span>
                <span class="nav-sub">More</span>
                <span class="dropdown-arrow">
                  <svg width="12" height="12" viewBox="0 0 12 12">
                    <path
                      d="M1 4L6 9L11 4"
                      stroke="currentColor"
                      stroke-width="1.5"
                      fill="none"
                      stroke-linecap="round"
                    />
                  </svg>
                </span>
                <span class="nav-wave"></span>
                <span class="nav-ripple"></span>
              </div>

              <transition name="dropdown">
                <div v-if="showDropdown" class="dropdown-menu">
                  <div class="dropdown-backdrop"></div>
                  <div class="dropdown-content">
               
                    <div class="dropdown-items">
                      <RouterLink
                        v-for="item in dropdownItems"
                        :key="item.name"
                        :to="item.path"
                        class="dropdown-item"
                        @click="showDropdown = false"
                      >
                        <span class="item-bg"></span>
                        <span class="item-icon">{{ item.icon }}</span>
                        <div class="item-content">
                          <div class="item-title">{{ item.name }}</div>
                          <div class="item-desc">{{ item.desc }}</div>
                        </div>
                        <span class="item-wave"></span>
                      </RouterLink>
                      <a
                        href="https://slty.site/#/redirector"
                        target="_blank"
                        rel="noopener"
                        class="dropdown-item external"
                      >
                        <span class="item-bg"></span>
                        <span class="item-icon">❄</span>
                        <div class="item-content">
                          <div class="item-title">霜落映界</div>
                          <div class="item-desc">Frost Realm Portal</div>
                        </div>
                        <span class="external-icon">↗</span>
                      </a>
                    </div>
                    <div class="dropdown-footer">
                      <div class="footer-note">♪ 弗洛洛的低语萦绕 ♪</div>
                      <div class="footer-sub">PHROLOVA's Whisper Echoes</div>
                    </div>
                  </div>
                </div>
              </transition>
            </div>
          </div>
        </nav>
      </div>
    </div>

    <!-- 移动端导航菜单 -->
    <transition name="mobile-nav">
      <div
        class="mobile-nav-overlay"
        v-if="mobileNavOpen && isMobile"
        @click="toggleMobileNav"
      ></div>
    </transition>

    <transition name="mobile-nav">
      <nav class="mobile-nav" v-if="mobileNavOpen && isMobile">
        <div class="mobile-nav-header">
          <div class="mobile-nav-title">
            <span class="nav-title-icon">🎵</span>
            <span class="nav-title-text">弗洛洛领域</span>
          </div>
          <div class="mobile-nav-subtitle">PHROLOVA's Domain</div>
          <button class="mobile-nav-close" @click="toggleMobileNav">
            <span class="close-icon">✕</span>
          </button>
        </div>

        <div class="mobile-nav-content">
          <!-- 主导航 -->
          <div class="mobile-nav-section">
            <div class="mobile-section-title">
              <span class="section-icon">🌀</span>
              <span class="section-text">主旋律</span>
            </div>
            <div class="mobile-nav-items">
              <RouterLink
                v-for="item in mainNavItems"
                :key="item.name"
                :to="item.path"
                class="mobile-nav-item"
                active-class="mobile-active"
                @click="toggleMobileNav"
              >
                <div class="mobile-item-content">
                  <div class="mobile-item-title">{{ item.name }}</div>
                  <div class="mobile-item-desc">{{ item.subtitle }}</div>
                </div>
                <span class="mobile-item-arrow">›</span>
              </RouterLink>
            </div>
          </div>

          <!-- 下拉菜单项 -->
          <div class="mobile-nav-section">
            <div class="mobile-section-title">
              <span class="section-icon">🎼</span>
              <span class="section-text">声骸回响</span>
            </div>
            <div class="mobile-nav-items">
              <RouterLink
                v-for="item in dropdownItems"
                :key="item.name"
                :to="item.path"
                class="mobile-nav-item"
                active-class="mobile-active"
                @click="toggleMobileNav"
              >
                <span class="mobile-item-icon">{{ item.icon }}</span>
                <div class="mobile-item-content">
                  <div class="mobile-item-title">{{ item.name }}</div>
                  <div class="mobile-item-desc">{{ item.desc }}</div>
                </div>
                <span class="mobile-item-arrow">›</span>
              </RouterLink>

              <a
                href="https://slty.site/#/redirector"
                target="_blank"
                rel="noopener"
                class="mobile-nav-item external"
                @click="toggleMobileNav"
              >
                <span class="mobile-item-icon">❄</span>
                <div class="mobile-item-content">
                  <div class="mobile-item-title">霜落映界</div>
                  <div class="mobile-item-desc">Frost Realm Portal</div>
                </div>
                <span class="mobile-external-icon">↗</span>
              </a>
            </div>
          </div>

          <!-- 音乐控制 -->
          <div class="mobile-music-section">
            <div class="mobile-music-info">
              <div class="mobile-music-title">背景旋律</div>
              <div class="mobile-music-track">酣梦于彼岸深红</div>
            </div>
            <div
              class="mobile-music-toggle"
              @click="toggleMusic"
              :class="{ playing: isPlaying }"
            >
              <div class="mobile-toggle-icon">
                <div class="mobile-toggle-note">♪</div>
              </div>
              <div class="mobile-toggle-status">
                {{ isPlaying ? "演奏中" : "静音" }}
              </div>
            </div>
          </div>

          <!-- 在线状态 -->
          <div class="mobile-status-section">
            <div class="mobile-status-item">
              <div class="status-icon">👥</div>
              <div class="status-content">
                <div class="status-label">当前共鸣者</div>
                <div class="status-value" v-if="onlineCount !== null">
                  {{ onlineCount }} 人
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="mobile-nav-footer">
          <div class="mobile-footer-note">♪ 声骸低语，旋律永存 ♪</div>
        </div>
      </nav>
    </transition>

    <!-- 音频元素 -->
    <audio ref="audioPlayer" loop preload="auto" :src="bgmUrl"></audio>
  </header>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, computed } from "vue";
import { io } from "socket.io-client";

const mainNavItems = [
  { name: "失亡彼岸", path: "/", icon: "🌀", subtitle: "Home" },
  { name: "乐章年谱", path: "/timeLine", icon: "📜", subtitle: "Character Profile" },
  { name: "音符札记", path: "/message", icon: "✉️", subtitle: "Message Board" },
  { name: "残影绘卷", path: "/gallery", icon: "🖼️", subtitle: "Gallery" },
  {
    name: "声骸低语",
    path: "/talk",
    icon: "🗣️",
    subtitle: "AI Chat",
  },
];

const dropdownItems = [
  { name: "深渊秘典", path: "/resources", icon: "📚", desc: "Resources" },
  { name: "回响低语", path: "/voice", icon: "🎙️", desc: "Voice" },
  {
    name: "共鸣曲库",
    path: "/music",
    icon: "🎵",
    desc: "Music",
  },
  {
    name: "回响秘文",
    path: "/wiki",
    icon: "📖",
    desc: "Wiki",
  },
];

// 响应式状态
const mobileNavOpen = ref(false);
const showDropdown = ref(false);
const isMobile = ref(false);
const isPlaying = ref(false);
const audioPlayer = ref<HTMLAudioElement | null>(null);

// BGM URL
const bgmUrl = import.meta.env.VITE_API_BASE_URL+"/music/酣梦于彼岸深红.mp3";

// 在线人数
const siteId = "fll";
const onlineCount = ref<number | null>(null);

// 方法
function toggleMobileNav() {
  mobileNavOpen.value = !mobileNavOpen.value;
  // 阻止背景滚动
  if (mobileNavOpen.value) {
    document.body.style.overflow = "hidden";
  } else {
    document.body.style.overflow = "";
  }
}

function toggleMusic() {
  if (!audioPlayer.value) return;

  if (isPlaying.value) {
    audioPlayer.value.pause();
    isPlaying.value = false;
  } else {
    audioPlayer.value.play().catch((e) => {
      console.error("音频播放失败:", e);
      // 如果自动播放被阻止，显示提示
      alert("音乐播放被浏览器阻止，请点击页面任意位置后重试");
    });
    isPlaying.value = true;
  }
}

function checkMobile() {
  isMobile.value = window.innerWidth <= 1024;
}

// 生命周期
onMounted(() => {
  checkMobile();
  window.addEventListener("resize", checkMobile);

  // 初始化音频
  if (audioPlayer.value) {
    audioPlayer.value.volume = 0.6;

    // 监听音频结束
    audioPlayer.value.addEventListener("ended", () => {
      isPlaying.value = false;
    });

    // 监听音频播放错误
    audioPlayer.value.addEventListener("error", (e) => {
      console.error("音频加载错误:", e);
      isPlaying.value = false;
    });
  }

  // 连接在线人数socket
  const socket: any = io(import.meta.env.VITE_API_BASE_URL, {
    query: { siteId },
  });

  socket.on("onlineCount", (count: number) => {
    onlineCount.value = count;
  });
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", checkMobile);

  // 停止音乐
  if (audioPlayer.value) {
    audioPlayer.value.pause();
    audioPlayer.value.src = "";
  }

  // 恢复滚动
  document.body.style.overflow = "";
});
</script>

<style scoped lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Noto+Serif+SC:wght@400;700&family=ZCOOL+QingKe+HuangYou&display=swap");

.app-header {
  /* 弗洛洛核心色彩体系 */
  --deep-space: #06080c; // 深空黑
  --void-purple: #1a0a1a; // 虚空紫
  --accent-magenta: #b86be0; // 主色调-暗紫
  --accent-crimson: #ff6b85; // 高光-血玫
  --accent-deep: #8c3c60; // 深色-暗紫红
  --glow-magenta: rgba(184, 107, 224, 0.4); // 紫光晕
  --glow-crimson: rgba(255, 107, 133, 0.3); // 血光晕
  --text-primary: #f0e6f2; // 主文字-苍白紫
  --text-secondary: #c9b4cf; // 次文字-淡紫灰
  --glass-dark: rgba(10, 8, 16, 0.85); // 深色玻璃
  --glass-light: rgba(30, 20, 40, 0.6); // 浅色玻璃
  --border-faint: rgba(184, 107, 224, 0.15); // 微弱边框
  --mobile-bg: rgba(8, 6, 12, 0.98); // 移动端背景

  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;

  /* 背景：深空渐变 + 暗紫光晕 */
  background: radial-gradient(
      ellipse at 20% 50%,
      rgba(140, 60, 96, 0.1) 0%,
      transparent 60%
    ),
    radial-gradient(
      ellipse at 80% 50%,
      rgba(184, 107, 224, 0.08) 0%,
      transparent 60%
    ),
    linear-gradient(180deg, var(--deep-space) 0%, var(--void-purple) 100%);

  backdrop-filter: blur(12px) saturate(1.2);
  -webkit-backdrop-filter: blur(12px) saturate(1.2);

  box-shadow: 0 8px 40px rgba(3, 4, 6, 0.8), 0 0 0 1px var(--border-faint),
    inset 0 1px 0 rgba(255, 255, 255, 0.05);

  border-bottom: 1px solid rgba(184, 107, 224, 0.1);
  animation: fadeInDown 0.7s cubic-bezier(0.23, 1, 0.32, 1) both;

  /* 音效波纹背景 */
  .sound-waves {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    pointer-events: none;
    z-index: 0;

    .wave {
      position: absolute;
      border-radius: 50%;
      border: 1px solid rgba(184, 107, 224, 0.05);
      opacity: 0;
    }

    .wave-1 {
      top: 50%;
      left: 10%;
      width: 100px;
      height: 100px;
    }
    .wave-2 {
      top: 30%;
      right: 15%;
      width: 150px;
      height: 150px;
    }
    .wave-3 {
      bottom: 20%;
      left: 20%;
      width: 120px;
      height: 120px;
    }
    .wave-4 {
      top: 60%;
      right: 30%;
      width: 80px;
      height: 80px;
    }

    &.waves-active {
      .wave {
        animation: sound-wave 4s infinite;
      }
      .wave-1 {
        animation-delay: 0s;
      }
      .wave-2 {
        animation-delay: 0.5s;
      }
      .wave-3 {
        animation-delay: 1s;
      }
      .wave-4 {
        animation-delay: 1.5s;
      }
    }
  }

  /* 移动端顶部栏 */
  .mobile-top-bar {
    display: none;
    height: 60px;
    padding: 0 15px;
    align-items: center;
    justify-content: space-between;
    z-index: 1001;

    .mobile-left {
      display: flex;
      align-items: center;
      gap: 12px;

      .mobile-logo {
        display: flex;
        align-items: center;
        gap: 8px;

        .logo-icon {
          font-size: 22px;
          color: var(--accent-magenta);
          filter: drop-shadow(0 0 8px var(--glow-magenta));
        }

        .logo-text {
          font-family: "Cinzel", serif;
          font-size: 20px;
          font-weight: 700;
          background: linear-gradient(
            90deg,
            var(--accent-magenta),
            var(--accent-crimson)
          );
          background-clip: text;
          -webkit-background-clip: text;
          -webkit-text-fill-color: transparent;
          letter-spacing: 1px;
        }
      }

      .mobile-online {
        display: flex;
        align-items: center;
        gap: 6px;
        padding: 4px 10px;
        background: rgba(184, 107, 224, 0.1);
        border-radius: 12px;
        border: 1px solid rgba(184, 107, 224, 0.2);

        .online-dot {
          width: 6px;
          height: 6px;
          border-radius: 50%;
          background: var(--accent-crimson);
          animation: pulse 1.5s infinite;
        }

        .online-text {
          font-family: "Noto Serif SC", serif;
          font-size: 14px;
          color: var(--text-primary);
          font-weight: 600;
        }
      }
    }

    .mobile-right {
      display: flex;
      align-items: center;
      gap: 15px;

      .mobile-music-control {
        width: 40px;
        height: 40px;
        display: flex;
        align-items: center;
        justify-content: center;
        background: rgba(184, 107, 224, 0.1);
        border-radius: 12px;
        border: 1px solid rgba(184, 107, 224, 0.2);
        transition: all 0.3s ease;

        &:active {
          transform: scale(0.95);
        }

        &.playing {
          background: rgba(184, 107, 224, 0.2);
          border-color: var(--accent-magenta);
          box-shadow: 0 0 15px var(--glow-magenta);

          .mobile-note {
            animation: note-spin 2s linear infinite;
          }
        }

        .mobile-music-icon {
          position: relative;

          .mobile-note {
            font-size: 20px;
            color: var(--accent-crimson);
          }
        }
      }

      .mobile-hamburger {
        width: 40px;
        height: 40px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background: rgba(184, 107, 224, 0.1);
        border-radius: 12px;
        border: 1px solid rgba(184, 107, 224, 0.2);
        transition: all 0.3s ease;

        .hamburger-line {
          width: 20px;
          height: 2px;
          background: var(--text-primary);
          margin: 3px 0;
          border-radius: 1px;
          transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
        }

        &.open {
          .hamburger-line:nth-child(1) {
            transform: translateY(8px) rotate(45deg);
            background: var(--accent-crimson);
          }
          .hamburger-line:nth-child(2) {
            opacity: 0;
          }
          .hamburger-line:nth-child(3) {
            transform: translateY(-8px) rotate(-45deg);
            background: var(--accent-crimson);
          }
        }
      }
    }
  }

  /* PC端头部 */
  .desktop-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 80px;
    padding: 0 40px;

    /* 左侧：标题和在线人数 */
    .header-left {
      display: flex;
      align-items: center;
      gap: 30px;
      z-index: 2;

      .title-container {
        position: relative;

        .title-glow {
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          width: 200%;
          height: 200%;
          background: radial-gradient(
            circle,
            var(--glow-magenta) 0%,
            transparent 70%
          );
          opacity: 0.3;
          filter: blur(20px);
          z-index: 0;
        }

        .title {
          position: relative;
          font-family: "Cinzel", "Noto Serif SC", serif;
          font-style: italic;
          font-size: 30px;
          font-weight: 700;
          color: transparent;
          background: linear-gradient(
            90deg,
            var(--accent-magenta),
            var(--accent-crimson),
            var(--accent-magenta)
          );
          background-size: 200% auto;
          background-clip: text;
          -webkit-background-clip: text;
          letter-spacing: 1px;
          margin: 0;
          padding: 5px 0;
          z-index: 1;
          animation: gradient-shift 8s linear infinite;

          .title-text {
            display: inline-block;
          }

          .title-note {
            display: inline-block;
            margin-left: 10px;
            font-size: 24px;
            animation: note-bounce 3s ease-in-out infinite;
          }
        }

        .title-sub {
          font-family: "Noto Serif SC", serif;
          font-size: 12px;
          color: var(--text-secondary);
          letter-spacing: 2px;
          text-transform: uppercase;
          text-align: center;
          margin-top: 2px;
          opacity: 0.7;
        }
      }

      /* 在线人数 */
      .online-count {
        position: relative;
        display: flex;
        align-items: center;
        gap: 12px;
        padding: 8px 18px;
        background: var(--glass-dark);
        border-radius: 20px;
        border: 1px solid var(--border-faint);
        box-shadow: 0 8px 24px rgba(3, 4, 6, 0.4),
          inset 0 1px 0 rgba(255, 255, 255, 0.05);
        transition: all 0.3s ease;
        z-index: 1;

        &:hover {
          transform: translateY(-2px);
          box-shadow: 0 12px 32px rgba(3, 4, 6, 0.6),
            inset 0 1px 0 rgba(255, 255, 255, 0.08);
          border-color: rgba(184, 107, 224, 0.3);
        }

        .count-glow {
          position: absolute;
          top: 0;
          left: 0;
          right: 0;
          bottom: 0;
          background: radial-gradient(
            circle at center,
            var(--glow-magenta) 0%,
            transparent 70%
          );
          opacity: 0.2;
          border-radius: 20px;
          z-index: 0;
        }

        .count-icon {
          position: relative;
          width: 36px;
          height: 36px;
          display: flex;
          align-items: center;
          justify-content: center;
          z-index: 1;

          .pulse-dot {
            position: absolute;
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: var(--accent-crimson);
            opacity: 0.1;
            animation: pulse 2s infinite;
          }

          .note-icon {
            font-size: 18px;
            color: var(--accent-magenta);
            filter: drop-shadow(0 0 8px var(--glow-magenta));
          }
        }

        .count-content {
          z-index: 1;

          .count-label {
            font-family: "Noto Serif SC", serif;
            font-size: 12px;
            color: var(--text-secondary);
            margin-bottom: 2px;
          }

          .count-value {
            font-family: "Cinzel", serif;
            font-size: 20px;
            font-weight: 700;
            color: var(--accent-crimson);
            text-shadow: 0 0 10px var(--glow-crimson);

            .count-unit {
              font-size: 14px;
              color: var(--text-secondary);
              margin-left: 2px;
            }
          }
        }

        .count-ripple {
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          width: 0;
          height: 0;
          border-radius: 50%;
          border: 1px solid var(--accent-magenta);
          opacity: 0;
          animation: ripple 3s infinite;
        }
      }
    }

    /* 右侧：导航和音乐控制 */
    .header-right {
      display: flex;
      align-items: center;
      gap: 30px;
      z-index: 2;

      /* 音乐控制 */
      .music-control {
        position: relative;
        cursor: pointer;

        &:hover .music-tooltip {
          opacity: 1;
          transform: translateY(0);
        }

        .music-container {
          display: flex;
          flex-direction: column;
          align-items: center;
          padding: 8px 12px;
          background: var(--glass-dark);
          border-radius: 16px;
          border: 1px solid var(--border-faint);
          transition: all 0.3s ease;

          &:hover {
            border-color: var(--accent-magenta);
            box-shadow: 0 0 20px var(--glow-magenta),
              inset 0 1px 0 rgba(255, 255, 255, 0.08);

            .music-disc {
              transform: scale(1.1);
            }
          }

          &.playing {
            .music-disc {
              animation: disc-rotate 4s linear infinite;
            }

            .music-note {
              animation: note-float 2s ease-in-out infinite;
            }
          }

          .music-icon {
            position: relative;
            width: 40px;
            height: 40px;
            margin-bottom: 6px;

            .music-disc {
              position: absolute;
              top: 0;
              left: 0;
              width: 100%;
              height: 100%;
              border-radius: 50%;
              background: radial-gradient(
                  circle at 30% 30%,
                  var(--accent-magenta) 0%,
                  transparent 70%
                ),
                conic-gradient(
                  from 0deg,
                  transparent,
                  var(--accent-crimson),
                  transparent
                );
              border: 2px solid rgba(255, 255, 255, 0.1);
              transition: transform 0.3s ease;
            }

            .music-note {
              position: absolute;
              font-size: 14px;
              color: var(--accent-crimson);
              opacity: 0;

              &.note-1 {
                top: -5px;
                left: 10px;
                animation-delay: 0s;
              }
              &.note-2 {
                top: 5px;
                right: 0;
                animation-delay: 0.3s;
              }
              &.note-3 {
                bottom: -5px;
                left: 5px;
                animation-delay: 0.6s;
              }
            }
          }

          .music-status {
            display: flex;
            align-items: center;
            gap: 6px;

            .status-text {
              font-family: "Noto Serif SC", serif;
              font-size: 12px;
              color: var(--text-secondary);
            }

            .status-pulse {
              width: 6px;
              height: 6px;
              border-radius: 50%;
              background: var(--accent-crimson);
              opacity: 0.3;

              &.active {
                opacity: 1;
                animation: pulse 1.5s infinite;
              }
            }
          }
        }

        .music-tooltip {
          position: absolute;
          top: 100%;
          left: 50%;
          transform: translateX(-50%) translateY(-10px);
          padding: 6px 12px;
          background: var(--glass-dark);
          border-radius: 8px;
          font-family: "Noto Serif SC", serif;
          font-size: 12px;
          color: var(--text-secondary);
          white-space: nowrap;
          opacity: 0;
          transition: all 0.3s ease;
          pointer-events: none;
          z-index: 100;
          border: 1px solid var(--border-faint);
          box-shadow: 0 8px 24px rgba(3, 4, 6, 0.4);

          &::before {
            content: "";
            position: absolute;
            top: -4px;
            left: 50%;
            transform: translateX(-50%) rotate(45deg);
            width: 8px;
            height: 8px;
            background: var(--glass-dark);
            border-left: 1px solid var(--border-faint);
            border-top: 1px solid var(--border-faint);
          }
        }
      }

      /* 导航菜单 */
      .nav-links {
        display: flex;
        align-items: center;

        .nav-items {
          display: flex;
          gap: 4px;

          .nav-item {
            position: relative;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 10px 16px;
            color: var(--text-primary);
            text-decoration: none;
            border-radius: 12px;
            transition: all 0.3s ease;
            min-width: 80px;
            overflow: hidden;

            .nav-bg {
              position: absolute;
              top: 0;
              left: 0;
              right: 0;
              bottom: 0;
              background: var(--glass-dark);
              border: 1px solid transparent;
              border-radius: 12px;
              z-index: 0;
              transition: all 0.3s ease;
            }

            .nav-glow {
              position: absolute;
              top: 0;
              left: 0;
              right: 0;
              bottom: 0;
              background: radial-gradient(
                circle at center,
                var(--glow-magenta) 0%,
                transparent 70%
              );
              opacity: 0;
              z-index: 1;
              transition: opacity 0.3s ease;
            }

            .nav-icon {
              font-size: 18px;
              margin-bottom: 4px;
              z-index: 2;
            }

            .nav-text {
              font-family: "Noto Serif SC", serif;
              font-size: 14px;
              font-weight: 600;
              margin-bottom: 2px;
              z-index: 2;
            }

            .nav-sub {
              font-family: "Noto Serif SC", serif;
              font-size: 10px;
              color: var(--text-secondary);
              opacity: 0.7;
              z-index: 2;
            }

            .nav-wave {
              position: absolute;
              bottom: 0;
              left: 0;
              right: 0;
              height: 2px;
              background: linear-gradient(
                90deg,
                transparent,
                var(--accent-magenta),
                transparent
              );
              transform: scaleX(0);
              transition: transform 0.4s ease;
              z-index: 2;
            }

            .nav-ripple {
              position: absolute;
              top: 50%;
              left: 50%;
              transform: translate(-50%, -50%);
              width: 0;
              height: 0;
              border-radius: 50%;
              background: radial-gradient(
                circle,
                var(--accent-crimson) 0%,
                transparent 70%
              );
              opacity: 0;
              z-index: 1;
            }

            &:hover {
              transform: translateY(-3px);

              .nav-bg {
                border-color: var(--accent-magenta);
                box-shadow: 0 8px 20px rgba(3, 4, 6, 0.5),
                  inset 0 1px 0 rgba(255, 255, 255, 0.05);
              }

              .nav-glow {
                opacity: 0.2;
              }

              .nav-wave {
                transform: scaleX(0.8);
              }

              .nav-ripple {
                animation: ripple-expand 0.6s ease-out;
              }
            }

            &.active-link {
              .nav-bg {
                background: linear-gradient(
                  135deg,
                  rgba(184, 107, 224, 0.1),
                  rgba(255, 107, 133, 0.05)
                );
                border-color: var(--accent-magenta);
                box-shadow: 0 0 20px var(--glow-magenta),
                  inset 0 1px 0 rgba(255, 255, 255, 0.08);
              }

              .nav-glow {
                opacity: 0.3;
              }

              .nav-wave {
                transform: scaleX(1);
                animation: wave-flow 3s linear infinite;
              }

              .nav-icon,
              .nav-text {
                color: var(--accent-crimson);
                text-shadow: 0 0 10px var(--glow-crimson);
              }
            }

            &.dropdown-trigger {
              padding-right: 32px;

              .dropdown-arrow {
                position: absolute;
                right: 12px;
                top: 50%;
                transform: translateY(-50%);
                color: var(--accent-magenta);
                transition: transform 0.3s ease;
                z-index: 2;
              }

              &.dropdown-open {
                .dropdown-arrow {
                  transform: translateY(-50%) rotate(180deg);
                }
              }
            }
          }
        }

        /* 下拉菜单 */
        .dropdown-container {
          position: relative;

          .dropdown-menu {
            position: absolute;
            top: calc(100%);
            left: 0;
            transform: translateX(-50%);
            width: 280px;
            z-index: 1001;

            .dropdown-backdrop {
              position: absolute;
              top: 0;
              left: 0;
              right: 0;
              bottom: 0;
              backdrop-filter: blur(30px) saturate(1.5);
              -webkit-backdrop-filter: blur(30px) saturate(1.5);
              background: rgba(10, 8, 16, 0.85);
              border-radius: 16px;
              border: 1px solid rgba(184, 107, 224, 0.2);
              box-shadow: 0 25px 60px rgba(3, 4, 6, 0.9),
                0 0 0 1px rgba(184, 107, 224, 0.1),
                inset 0 1px 0 rgba(255, 255, 255, 0.05);
              z-index: 1;
            }

            .dropdown-content {
              position: relative;
              padding: 20px;
              z-index: 2;

          
              .dropdown-items {
                display: flex;
                flex-direction: column;
                gap: 8px;

                .dropdown-item {
                  position: relative;
                  display: flex;
                  align-items: center;
                  padding: 12px 16px;
                  color: var(--text-primary);
                  text-decoration: none;
                  border-radius: 12px;
                  transition: all 0.3s ease;
                  overflow: hidden;

                  .item-bg {
                    position: absolute;
                    top: 0;
                    left: 0;
                    right: 0;
                    bottom: 0;
                    background: rgba(30, 20, 40, 0.3);
                    border: 1px solid transparent;
                    border-radius: 12px;
                    transition: all 0.3s ease;
                    z-index: 0;
                  }

                  .item-icon {
                    font-size: 20px;
                    margin-right: 12px;
                    z-index: 1;
                    transition: transform 0.3s ease;
                  }

                  .item-content {
                    flex: 1;
                    z-index: 1;

                    .item-title {
                      font-family: "Noto Serif SC", serif;
                      font-size: 14px;
                      font-weight: 600;
                      margin-bottom: 2px;
                    }

                    .item-desc {
                      font-family: "Noto Serif SC", serif;
                      font-size: 11px;
                      color: var(--text-secondary);
                    }
                  }

                  .item-wave {
                    position: absolute;
                    bottom: 0;
                    left: 0;
                    right: 0;
                    height: 1px;
                    background: linear-gradient(
                      90deg,
                      transparent,
                      var(--accent-magenta),
                      transparent
                    );
                    transform: scaleX(0);
                    transition: transform 0.4s ease;
                  }

                  &:hover {
                    transform: translateX(5px);

                    .item-bg {
                      background: rgba(184, 107, 224, 0.1);
                      border-color: var(--accent-magenta);
                    }

                    .item-icon {
                      transform: scale(1.2);
                      color: var(--accent-crimson);
                    }

                    .item-wave {
                      transform: scaleX(1);
                    }
                  }

                  &.external {
                    .external-icon {
                      margin-left: 8px;
                      font-size: 12px;
                      color: var(--accent-crimson);
                      z-index: 1;
                      opacity: 0.7;
                      transition: all 0.3s ease;
                    }

                    &:hover .external-icon {
                      opacity: 1;
                      transform: translate(3px, -3px);
                    }
                  }
                }
              }

              .dropdown-footer {
                text-align: center;
                margin-top: 20px;
                padding-top: 15px;
                border-top: 1px solid rgba(184, 107, 224, 0.2);

                .footer-note {
                  font-family: "Cinzel", serif;
                  font-size: 14px;
                  color: var(--accent-magenta);
                  margin-bottom: 4px;
                }

                .footer-sub {
                  font-family: "Noto Serif SC", serif;
                  font-size: 10px;
                  color: var(--text-secondary);
                  letter-spacing: 1px;
                }
              }
            }
          }

          /* 下拉菜单动画 */
          .dropdown-enter-active,
          .dropdown-leave-active {
            transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
          }

          .dropdown-enter-from,
          .dropdown-leave-to {
            opacity: 0;
            transform: translateX(-50%) translateY(-20px);
          }
        }
      }
    }
  }

  /* 移动端导航菜单 */
  .mobile-nav-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(3, 4, 6, 0.8);
    backdrop-filter: blur(5px);
    z-index: 999;
  }

  .mobile-nav {
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    width: 85%;
    max-width: 400px;
    background: var(--mobile-bg);
    backdrop-filter: blur(30px) saturate(1.5);
    -webkit-backdrop-filter: blur(30px) saturate(1.5);
    box-shadow: -10px 0 60px rgba(3, 4, 6, 0.9),
      0 0 0 1px rgba(184, 107, 224, 0.1);
    display: flex;
    flex-direction: column;
    z-index: 1000;
    min-height: 100vh;
    .mobile-nav-header {
      padding: 25px 20px 15px;
      border-bottom: 1px solid rgba(184, 107, 224, 0.2);
      position: relative;

      .mobile-nav-title {
        display: flex;
        align-items: center;
        gap: 10px;
        margin-bottom: 5px;

        .nav-title-icon {
          font-size: 28px;
          color: var(--accent-magenta);
          filter: drop-shadow(0 0 10px var(--glow-magenta));
        }

        .nav-title-text {
          font-family: "Cinzel", serif;
          font-size: 24px;
          font-weight: 700;
          color: var(--accent-crimson);
        }
      }

      .mobile-nav-subtitle {
        font-family: "Noto Serif SC", serif;
        font-size: 12px;
        color: var(--text-secondary);
        letter-spacing: 1px;
        margin-left: 38px;
      }

      .mobile-nav-close {
        position: absolute;
        top: 20px;
        right: 20px;
        width: 40px;
        height: 40px;
        display: flex;
        align-items: center;
        justify-content: center;
        background: rgba(184, 107, 224, 0.1);
        border-radius: 12px;
        border: 1px solid rgba(184, 107, 224, 0.2);
        color: var(--text-primary);
        font-size: 20px;
        transition: all 0.3s ease;

        &:active {
          transform: scale(0.95);
          background: rgba(184, 107, 224, 0.2);
        }
      }
    }

    .mobile-nav-content {
      flex: 1;
      overflow-y: auto;
      padding: 15px 0;

      /* 隐藏滚动条但保持滚动功能 */
      scrollbar-width: none;
      -ms-overflow-style: none;

      &::-webkit-scrollbar {
        display: none;
      }

      .mobile-nav-section {
        margin-bottom: 20px;

        .mobile-section-title {
          display: flex;
          align-items: center;
          gap: 10px;
          padding: 0 20px 10px;
          margin-bottom: 5px;
          border-bottom: 1px solid rgba(184, 107, 224, 0.1);

          .section-icon {
            font-size: 18px;
            color: var(--accent-magenta);
          }

          .section-text {
            font-family: "Cinzel", serif;
            font-size: 16px;
            color: var(--accent-crimson);
            letter-spacing: 1px;
          }
        }

        .mobile-nav-items {
          .mobile-nav-item {
            display: flex;
            align-items: center;
            padding: 15px 20px;
            color: var(--text-primary);
            text-decoration: none;
            transition: all 0.2s ease;
            position: relative;

            &:active {
              background: rgba(184, 107, 224, 0.1);
            }

            .mobile-item-icon {
              font-size: 22px;
              margin-right: 15px;
              width: 30px;
              text-align: center;
            }

            .mobile-item-content {
              flex: 1;

              .mobile-item-title {
                font-family: "Noto Serif SC", serif;
                font-size: 16px;
                font-weight: 600;
                margin-bottom: 3px;
              }

              .mobile-item-desc {
                font-family: "Noto Serif SC", serif;
                font-size: 12px;
                color: var(--text-secondary);
              }
            }

            .mobile-item-arrow {
              font-size: 20px;
              color: var(--accent-magenta);
              opacity: 0.7;
            }

            .mobile-external-icon {
              font-size: 16px;
              color: var(--accent-crimson);
              opacity: 0.7;
            }

            &.mobile-active {
              background: linear-gradient(
                90deg,
                rgba(184, 107, 224, 0.1),
                transparent
              );
              border-left: 3px solid var(--accent-crimson);

              .mobile-item-icon {
                color: var(--accent-crimson);
              }

              .mobile-item-title {
                color: var(--accent-crimson);
                text-shadow: 0 0 8px var(--glow-crimson);
              }
            }
          }
        }
      }

      .mobile-music-section {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 15px 20px;
        margin: 20px;
        background: rgba(184, 107, 224, 0.1);
        border-radius: 12px;
        border: 1px solid rgba(184, 107, 224, 0.2);

        .mobile-music-info {
          .mobile-music-title {
            font-family: "Noto Serif SC", serif;
            font-size: 14px;
            color: var(--text-secondary);
            margin-bottom: 3px;
          }

          .mobile-music-track {
            font-family: "Cinzel", serif;
            font-size: 16px;
            color: var(--accent-crimson);
          }
        }

        .mobile-music-toggle {
          display: flex;
          flex-direction: column;
          align-items: center;
          gap: 5px;

          .mobile-toggle-icon {
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(184, 107, 224, 0.2);
            border-radius: 12px;
            border: 1px solid rgba(184, 107, 224, 0.3);

            .mobile-toggle-note {
              font-size: 20px;
              color: var(--accent-crimson);
            }
          }

          .mobile-toggle-status {
            font-family: "Noto Serif SC", serif;
            font-size: 12px;
            color: var(--text-secondary);
          }

          &.playing {
            .mobile-toggle-icon {
              background: rgba(184, 107, 224, 0.3);
              border-color: var(--accent-magenta);
              box-shadow: 0 0 15px var(--glow-magenta);

              .mobile-toggle-note {
                animation: note-spin 2s linear infinite;
              }
            }

            .mobile-toggle-status {
              color: var(--accent-crimson);
            }
          }
        }
      }

      .mobile-status-section {
        padding: 0 20px;
        margin-top: 20px;

        .mobile-status-item {
          display: flex;
          align-items: center;
          gap: 15px;
          padding: 15px;
          background: rgba(184, 107, 224, 0.1);
          border-radius: 12px;
          border: 1px solid rgba(184, 107, 224, 0.2);

          .status-icon {
            font-size: 24px;
            color: var(--accent-magenta);
          }

          .status-content {
            .status-label {
              font-family: "Noto Serif SC", serif;
              font-size: 12px;
              color: var(--text-secondary);
              margin-bottom: 3px;
            }

            .status-value {
              font-family: "Cinzel", serif;
              font-size: 18px;
              color: var(--accent-crimson);
              font-weight: 700;
            }
          }
        }
      }
    }

    .mobile-nav-footer {
      padding: 15px 20px;
      border-top: 1px solid rgba(184, 107, 224, 0.2);
      text-align: center;

      .mobile-footer-note {
        font-family: "Cinzel", serif;
        font-size: 14px;
        color: var(--accent-magenta);
        opacity: 0.8;
      }
    }
  }

  /* 移动端导航动画 */
  .mobile-nav-enter-active,
  .mobile-nav-leave-active {
    transition: all 0.4s cubic-bezier(0.23, 1, 0.32, 1);
  }

  .mobile-nav-enter-from,
  .mobile-nav-leave-to {
    transform: translateX(100%);
  }

  .mobile-nav-enter-active .mobile-nav,
  .mobile-nav-leave-active .mobile-nav {
    transition: transform 0.4s cubic-bezier(0.23, 1, 0.32, 1);
  }

  .mobile-nav-enter-active .mobile-nav-overlay,
  .mobile-nav-leave-active .mobile-nav-overlay {
    transition: opacity 0.4s cubic-bezier(0.23, 1, 0.32, 1);
  }

  .mobile-nav-enter-from .mobile-nav-overlay,
  .mobile-nav-leave-to .mobile-nav-overlay {
    opacity: 0;
  }

  /* 响应式设计 */
  @media (max-width: 1200px) {
    .desktop-header {
      padding: 0 30px;

      .header-left .title-container .title {
        font-size: 26px;
      }

      .nav-links .nav-items .nav-item {
        padding: 10px 12px;
        min-width: 70px;
      }
    }
  }

  @media (max-width: 1024px) {
    height: 60px;

    .desktop-header {
      display: none;
    }

    .mobile-top-bar {
      display: flex;
    }
  }
}

/* 全局动画关键帧 */
@keyframes fadeInDown {
  0% {
    opacity: 0;
    transform: translateY(-20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

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

@keyframes note-bounce {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
  }
  25% {
    transform: translateY(-5px) rotate(-10deg);
  }
  75% {
    transform: translateY(3px) rotate(5deg);
  }
}

@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
    opacity: 0.6;
  }
  50% {
    transform: scale(1.2);
    opacity: 1;
  }
}

@keyframes ripple {
  0% {
    width: 0;
    height: 0;
    opacity: 0.5;
  }
  100% {
    width: 100px;
    height: 100px;
    opacity: 0;
  }
}

@keyframes sound-wave {
  0% {
    transform: scale(0.5);
    opacity: 0.5;
  }
  100% {
    transform: scale(2);
    opacity: 0;
  }
}

@keyframes disc-rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes note-float {
  0%,
  100% {
    transform: translateY(0);
    opacity: 0;
  }
  50% {
    transform: translateY(-20px);
    opacity: 1;
  }
}

@keyframes wave-flow {
  0% {
    background-position: -100% 0;
  }
  100% {
    background-position: 200% 0;
  }
}

@keyframes ripple-expand {
  0% {
    width: 0;
    height: 0;
    opacity: 0.8;
  }
  100% {
    width: 200px;
    height: 200px;
    opacity: 0;
  }
}

@keyframes note-spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>