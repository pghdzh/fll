<template>
  <div class="yuzuki-resources">
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
    <header class="hero">
      <div class="hero-inner">
        <h1>资源分享</h1>
        <p class="subtitle">可自由上传关于弗洛洛的相关链接</p>
      </div>
    </header>

    <main class="container">
      <section class="uploader" :class="{ collapsed: uploaderCollapsed }">
        <div class="uploader-head">
          <button
            class="toggle"
            @click="toggleUploader"
            :aria-expanded="!uploaderCollapsed"
          >
            <span v-if="uploaderCollapsed">展开上传区</span>
            <span v-else>收起上传区</span>
          </button>
        </div>

        <form
          @submit.prevent="addResource"
          class="upload-form"
          :aria-hidden="uploaderCollapsed"
        >
          <div class="row">
            <input
              v-model="form.title"
              type="text"
              placeholder="标题（必填，如果有解压码之类的也写这里吧）"
              aria-label="标题"
            />
            <input
              v-model="form.type"
              type="text"
              placeholder="链接类型(网页链接、b站视频、网盘链接等等)"
              aria-label="来源"
            />
          </div>

          <div class="row">
            <input
              v-model="form.uploader"
              type="text"
              placeholder="上传人（可选）"
              aria-label="上传人"
            />
            <input
              v-model="form.link"
              type="url"
              placeholder="链接(只输入网址不能有中文)"
              aria-label="链接"
            />
          </div>

          <div class="actions">
            <button type="submit" class="btn primary">上传</button>
          </div>
        </form>
      </section>

      <section class="list">
        <div class="list-header">
          <h2>资源列表（{{ resources.length }}）</h2>
          <div class="sort">
            <label>
              排序：
              <select v-model="sortBy">
                <option value="time">按时间（新→旧）</option>
                <option value="likes">按点赞（高→低）</option>
              </select>
            </label>
          </div>
        </div>

        <ul class="items">
          <li v-for="item in sortedResources" :key="item.id" class="item">
            <a
              :href="item.link"
              target="_blank"
              rel="noopener noreferrer"
              class="title"
              >{{ item.title }}</a
            >

            <div class="meta">
              <div class="left">
                <span class="uploader">{{ item.uploader || "匿名" }}</span>
                <span class="dot">•</span>
                <time :datetime="item.time">{{ formatTime(item.time) }}</time>
              </div>

              <div class="right">
                <button
                  @click.prevent="handleLike(item)"
                  :aria-pressed="likedIds.has(String(item.id))"
                  class="like-btn"
                  :class="{ active: likedIds.has(String(item.id)) }"
                >
                  <img
                    :src="
                      likedIds.has(String(item.id))
                        ? '/icons/heart-red-filled.svg'
                        : '/icons/heart-red-outline.svg'
                    "
                    class="heart-icon"
                    alt="heart"
                  />
                  <span class="count">{{ item.likes }}</span>
                </button>

                <span class="badge">{{ item.type }}</span>
              </div>
            </div>
          </li>
        </ul>

        <p v-if="resources.length === 0" class="empty">
          目前没有资源，快来上传第一条吧！
        </p>
      </section>
    </main>

    <footer class="foot">提示：点击标题将直接跳转</footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from "vue";
// 如果你的工程使用 ts 路径别名 @ 指向 src，可以用 '@/api/resource'，否则根据实际路径调整
import {
  getResourceList,
  createResource,
  likeResource,
} from "@/api/modules/resource";
import { ElMessage } from "element-plus";

interface Resource {
  id: number | string;
  title: string;
  uploader?: string;
  time: string; // ISO 或 created_at
  likes: number;
  link: string;
  type: string;
  role_key?: string;
}

const STORAGE_KEY = "fll_resources_v1";
const DEFAULT_ROLE = "fll";

const form = ref<{
  title: string;
  uploader: string;
  link: string;
  type: string;
}>({
  title: "",
  uploader: "",
  link: "",
  type: "",
});

const resources = ref<Resource[]>([]);
const likedIds = ref(new Set<string>());
const sortBy = ref<"time" | "likes">("time");
const uploaderCollapsed = ref(false);

function mapServerToLocal(row: any): Resource {
  return {
    id: row.id,
    title: row.title,
    uploader: row.uploader || "匿名",
    time: row.created_at || row.time || new Date().toISOString(),
    likes: row.likes ?? 0,
    link: row.link,
    type: row.storage_type || row.type || "other",
    role_key: row.role_key,
  };
}

async function loadResources() {
  try {
    // 尝试从后端拉取（分页可扩展，这里一次拉足够 demo）
    const res: any = await getResourceList({
      role_key: DEFAULT_ROLE,
      page: 1,
      pageSize: 100,
    });
    if (res && res.success && Array.isArray(res.data)) {
      resources.value = res.data.map(mapServerToLocal);
      // 可恢复本地点赞状态（仅 UI 记忆）
      const raw = localStorage.getItem(STORAGE_KEY);
      if (raw) {
        try {
          const parsed = JSON.parse(raw);
          if (parsed.liked && Array.isArray(parsed.liked)) {
            parsed.liked.forEach((id: string) => likedIds.value.add(id));
          }
        } catch (e) {
          /* ignore */
        }
      }
      return;
    }
  } catch (err) {
    console.warn("拉取资源失败，使用本地缓存", err);
  }
  // 回退：本地缓存（仅恢复点赞状态）
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (raw) {
      const parsed = JSON.parse(raw);
      if (parsed.liked && Array.isArray(parsed.liked)) {
        parsed.liked.forEach((id: string) => likedIds.value.add(id));
      }
    }
  } catch (e) {
    console.warn("本地加载失败", e);
  }
}

function saveLocalCache() {
  try {
    const liked = Array.from(likedIds.value);
    localStorage.setItem(STORAGE_KEY, JSON.stringify({ liked }));
  } catch (e) {
    console.warn("保存本地缓存失败", e);
  }
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
  loadResources();
  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);
  uploaderCollapsed.value = window.innerWidth <= 640;
});
function toggleUploader() {
  uploaderCollapsed.value = !uploaderCollapsed.value;
}
onBeforeUnmount(() => {
  if (Imgtimer) clearInterval(Imgtimer);
});

async function addResource() {
  const t = form.value.title.trim();
  const l = form.value.link.trim();
  if (!form.value.title.trim() || !form.value.link.trim()) {
    return ElMessage.warning("请填写完整信息");
  }
  if (!/^https?:\/\//i.test(l)) {
    return ElMessage.error("请输入正确的链接(https开头)");
  }
  // 尝试调用后端接口
  try {
    const payload = {
      title: t,
      uploader: form.value.uploader.trim() || "匿名",
      link: l,
      storage_type: form.value.type,
      role_key: DEFAULT_ROLE,
    };
    const res: any = await createResource(payload);
    if (res && res.success && res.data) {
      const added = mapServerToLocal(res.data);
      resources.value.unshift(added);
      // 自动展开到顶部展示（可选）
      saveLocalCache();
      resetForm();
      ElMessage.success("上传成功");
      return;
    }
    ElMessage.error("上传失败");
  } catch (err) {
    console.warn("创建资源失败", err);
  }
}

function resetForm() {
  form.value.title = "";
  form.value.uploader = "";
  form.value.link = "";
  form.value.type = "";
}

async function handleLike(item: Resource) {
  // UI 乐观更新
  const id = item.id;
  const wasLiked = likedIds.value.has(String(id));
  if (wasLiked) {
    likedIds.value.delete(String(id));
    item.likes = Math.max(0, item.likes - 1);
  } else {
    likedIds.value.add(String(id));
    item.likes++;
  }
  saveLocalCache();

  // 同步后端（不依赖返回值进行 UI 回滚，简单处理：若失败则回退）
  try {
    const action = wasLiked ? "unlike" : "like";
    const res: any = await likeResource(id, action);
    if (
      res &&
      res.success &&
      res.data &&
      typeof res.data.likes !== "undefined"
    ) {
      item.likes = res.data.likes;
    }
  } catch (err) {
    console.warn("点赞接口调用失败，回滚本地状态", err);
    // 回滚
    if (wasLiked) {
      // 本来是已赞，取消失败 -> 重新添加
      likedIds.value.add(String(id));
      item.likes++;
    } else {
      likedIds.value.delete(String(id));
      item.likes = Math.max(0, item.likes - 1);
    }
    saveLocalCache();
  }
}

const sortedResources = computed(() => {
  const arr = [...resources.value];
  if (sortBy.value === "time") {
    arr.sort((a, b) => +new Date(b.time) - +new Date(a.time));
  } else {
    arr.sort((a, b) => b.likes - a.likes);
  }
  return arr;
});

function formatTime(iso: string) {
  try {
    const d = new Date(iso);
    return new Intl.DateTimeFormat("zh-CN", {
      month: "2-digit",
      day: "2-digit",
      hour: "2-digit",
      minute: "2-digit",
    }).format(d);
  } catch (e) {
    return iso;
  }
}
</script>

<style scoped lang="scss">
.yuzuki-resources {
  --deep-space: #06080c; // 深空黑
  --void-purple: #1a0a1a; // 虚空紫
  --accent-magenta: #b86be0; // 主色调-暗紫
  --accent-crimson: #ff6b85; // 高光-血玫
  --accent-deep: #8c3c60; // 深色-暗紫红
  --glow-magenta: rgba(184, 107, 224, 0.4); // 紫光晕
  --glow-crimson: rgba(255, 107, 133, 0.3); // 血光晕
  --text-primary: #f0e6f2; // 主文字-苍白紫
  --text-secondary: #c9b4cf; // 次文字-淡紫灰
  --glass-dark: rgba(10, 8, 16, 0.35); // 深色玻璃
  --glass-light: rgba(30, 20, 40, 0.6); // 浅色玻璃
  --border-faint: rgba(184, 107, 224, 0.15); // 微弱边框
  --mobile-bg: rgba(8, 6, 12, 0.98); // 移动端背景

  color: var(--text-primary);
  min-height: 100vh;
  position: relative;
  overflow-x: hidden;
 
  font-family: "Segoe UI", system-ui, -apple-system, sans-serif;
  -webkit-font-smoothing: antialiased;
  padding-top: 60px;
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
      animation: gradientShift 15s ease-in-out infinite;
    }

    .carousel-image {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
      filter: blur(2px) brightness(0.7) contrast(1.2) saturate(1.1);
      opacity: 0;
      transition: opacity 1.5s cubic-bezier(0.4, 0, 0.2, 1);
      transform: scale(1.01);

      &.active {
        opacity: 1;
        animation: floatImage 15s ease-in-out infinite;
      }
    }
  }

  .carousel2 {
    display: none;
  }

  .hero {
    position: relative;
    z-index: 10;
    padding: clamp(1.5rem, 4vw, 3rem) 1rem;
    background: var(--glass-dark);
    backdrop-filter: blur(6px) saturate(180%);
    border-bottom: 1px solid var(--border-faint);
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4),
      inset 0 1px 0 rgba(255, 255, 255, 0.05);
    margin-bottom: 2rem;

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
      animation: shimmer 3s infinite;
    }

    .hero-inner {
      max-width: 1200px;
      margin: 0 auto;
      text-align: center;

      h1 {
        margin: 0;
        font-size: clamp(2rem, 5vw, 3.5rem);
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
        text-shadow: 0 0 30px var(--glow-magenta), 0 0 60px var(--glow-crimson);
        animation: textGlow 4s ease-in-out infinite;
      }

      .subtitle {
        margin-top: 1rem;
        color: var(--text-secondary);
        font-size: clamp(0.9rem, 2vw, 1.1rem);
        font-weight: 300;
        letter-spacing: 0.05em;
        opacity: 0.9;
      }
    }
  }

  .container {
    max-width: 1200px;
    margin: 0 auto 3rem;
    padding: 0 1rem;
    position: relative;
    z-index: 10;

    .uploader {
  
      overflow: hidden;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3),
        inset 0 1px 0 rgba(255, 255, 255, 0.05);
      transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
      margin-bottom: 2rem;

      &:hover {
        transform: translateY(-4px);
        box-shadow: 0 30px 80px rgba(184, 107, 224, 0.15),
          0 20px 60px rgba(0, 0, 0, 0.4),
          inset 0 1px 0 rgba(255, 255, 255, 0.05);
      }

      .uploader-head {
        padding: 1.5rem;
        border-bottom: 1px solid var(--border-faint);

        .toggle {
          background: linear-gradient(
            135deg,
            var(--accent-deep) 0%,
            var(--accent-magenta) 100%
          );
          border: none;
          color: var(--text-primary);
          padding: 0.75rem 1.5rem;
          border-radius: 12px;
          cursor: pointer;
          font-weight: 600;
          font-size: 0.95rem;
          letter-spacing: 0.05em;
          transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
          position: relative;
          overflow: hidden;

          &::before {
            content: "";
            position: absolute;
            inset: 0;
            background: linear-gradient(
              135deg,
              var(--accent-magenta) 0%,
              var(--accent-crimson) 100%
            );
            opacity: 0;
            transition: opacity 0.3s ease;
          }

          &:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 30px var(--glow-magenta),
              0 5px 15px var(--glow-crimson);

            &::before {
              opacity: 1;
            }
          }

          span {
            position: relative;
            z-index: 1;
          }
        }
      }

      .upload-form {
        padding: 2rem;
        display: grid;
        gap: 1.5rem;
        max-height: 1000px;
        opacity: 1;
        transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);

        .row {
          display: grid;
          grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
          gap: 1rem;

          input {
            padding: 1rem 1.25rem;
            border-radius: 12px;
            border: 1px solid var(--border-faint);
            background: rgba(10, 8, 16, 0.4);
            color: var(--text-primary);
            font-size: 0.95rem;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

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
          }
        }

        .actions {
          display: flex;
          justify-content: flex-end;

          .btn {
            padding: 1rem 2rem;
            border-radius: 12px;
            border: none;
            font-weight: 600;
            font-size: 1rem;
            letter-spacing: 0.05em;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;

            &.primary {
              background: linear-gradient(
                135deg,
                var(--accent-magenta) 0%,
                var(--accent-crimson) 100%
              );
              color: var(--text-primary);

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

              &:hover {
                transform: translateY(-3px);
                box-shadow: 0 15px 40px var(--glow-magenta),
                  0 10px 30px var(--glow-crimson);

                &::before {
                  opacity: 1;
                }
              }

              &:active {
                transform: translateY(-1px);
              }

              span {
                position: relative;
                z-index: 1;
              }
            }
          }
        }
      }

      &.collapsed {
        .upload-form {
          max-height: 0;
          padding-top: 0;
          padding-bottom: 0;
          opacity: 0;
          overflow: hidden;
        }
      }
    }

    .list {
      .list-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 2rem;
        padding: 1.5rem;
        background: var(--glass-light);
        backdrop-filter: blur(3px);
        border-radius: 20px;
        border: 1px solid var(--border-faint);

        h2 {
          margin: 0;
          font-size: 1.5rem;
          font-weight: 700;
          background: linear-gradient(
            135deg,
            var(--text-primary) 0%,
            var(--accent-magenta) 100%
          );
          -webkit-background-clip: text;
          -webkit-text-fill-color: transparent;
          background-clip: text;
        }

        .sort {
          label {
            color: var(--text-secondary);
            font-size: 0.9rem;
          }

          select {
            padding: 0.5rem 1rem;
            border-radius: 8px;
            border: 1px solid var(--border-faint);
            background: rgba(10, 8, 16, 0.6);
            color: var(--text-primary);
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.3s ease;

            &:hover {
              border-color: var(--accent-magenta);
            }

            &:focus {
              outline: none;
              box-shadow: 0 0 0 2px var(--glow-magenta);
            }
          }
        }
      }

      .items {
        display: grid;
        gap: 1rem;
        list-style: none;
        padding: 0;
        margin: 0;

        .item {
          background: var(--glass-light);
          backdrop-filter: blur(2px);
          border-radius: 20px;
          border: 1px solid var(--border-faint);
          padding: 1.5rem;
          transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
          position: relative;
          overflow: hidden;

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
            transition: left 0.6s ease;
          }

          &:hover {
            transform: translateY(-6px) translateX(4px);
            border-color: var(--accent-magenta);
            box-shadow: 0 20px 50px var(--glow-magenta),
              0 10px 30px rgba(0, 0, 0, 0.3);

            &::before {
              left: 100%;
            }

            .title {
              color: var(--accent-crimson);
            }
          }

          .title {
            display: block;
            color: var(--text-primary);
            font-size: 1.1rem;
            font-weight: 600;
            text-decoration: none;
            margin-bottom: 1rem;
            transition: color 0.3s ease;
            line-height: 1.4;

            &:hover {
              text-decoration: none;
            }
          }

          .meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.9rem;
            color: var(--text-secondary);

            .left {
              display: flex;
              align-items: center;
              gap: 1rem;

              .uploader {
                color: var(--accent-magenta);
                font-weight: 500;
                margin: auto 0;
              }

              .dot {
                color: var(--text-secondary);
                opacity: 0.5;
              }

              time {
                font-family: "Monaco", "Consolas", monospace;
              }
            }

            .right {
              display: flex;
              align-items: center;
              gap: 1rem;

              .like-btn {
                display: flex;
                align-items: center;
                gap: 0.5rem;
                background: none;
                border: none;
                color: var(--text-secondary);
                cursor: pointer;
                padding: 0.5rem 1rem;
                border-radius: 8px;
                transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
                position: relative;

                &::before {
                  content: "";
                  position: absolute;
                  inset: 0;
                  border-radius: 8px;
                  background: var(--glow-magenta);
                  opacity: 0;
                  transition: opacity 0.3s ease;
                }

                &:hover {
                  color: var(--accent-crimson);

                  &::before {
                    opacity: 0.1;
                  }

                  .heart-icon {
                    transform: scale(1.2);
                  }
                }

                &.active {
                  color: var(--accent-crimson);

                  .heart-icon {
                    filter: drop-shadow(0 0 8px var(--glow-crimson));
                    animation: pulseHeart 2s ease-in-out infinite;
                  }

                  .count {
                    color: var(--accent-crimson);
                  }
                }

                .heart-icon {
                  width: 20px;
                  height: 20px;
                  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
                  position: relative;
                  z-index: 1;
                }

                .count {
                  font-weight: 600;
                  position: relative;
                  z-index: 1;
                  transition: color 0.3s ease;
                }
              }

              .badge {
                padding: 0.375rem 0.875rem;
                border-radius: 100px;
                font-size: 0.8rem;
                font-weight: 500;
                background: linear-gradient(
                  135deg,
                  rgba(184, 107, 224, 0.15) 0%,
                  rgba(255, 107, 133, 0.15) 100%
                );
                border: 1px solid var(--border-faint);
                color: var(--text-secondary);
                transition: all 0.3s ease;

                &:hover {
                  background: linear-gradient(
                    135deg,
                    rgba(184, 107, 224, 0.25) 0%,
                    rgba(255, 107, 133, 0.25) 100%
                  );
                  border-color: var(--accent-magenta);
                  color: var(--text-primary);
                }
              }
            }
          }
        }
      }

      .empty {
        text-align: center;
        padding: 4rem 2rem;
        color: var(--text-secondary);
        font-size: 1.1rem;
        background: var(--glass-light);
        backdrop-filter: blur(10px);
        border-radius: 20px;
        border: 1px solid var(--border-faint);

        &::before {
          content: "📂";
          display: block;
          font-size: 3rem;
          margin-bottom: 1rem;
          opacity: 0.6;
        }
      }
    }
  }

  .foot {
    text-align: center;
    padding: 2rem;
    color: var(--text-secondary);
    font-size: 0.9rem;
    opacity: 0.7;
    border-top: 1px solid var(--border-faint);
    margin-top: 3rem;
    position: relative;
    z-index: 10;

    &::before {
      content: "✨";
      margin-right: 0.5rem;
    }
  }
}

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
    transform: scale(1.01) translateY(0px);
  }
  50% {
    transform: scale(1.02) translateY(-10px);
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
  0% {
    opacity: 0.3;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0.3;
  }
}

@keyframes textGlow {
  0%,
  100% {
    text-shadow: 0 0 30px var(--glow-magenta), 0 0 60px var(--glow-crimson);
  }
  50% {
    text-shadow: 0 0 40px var(--glow-magenta), 0 0 80px var(--glow-crimson),
      0 0 100px rgba(255, 107, 133, 0.2);
  }
}

@keyframes pulseHeart {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

@media (max-width: 768px) {
  .yuzuki-resources {
    .carousel1 {
      display: none;
    }

    .carousel2 {
      display: block;
    }

    .hero {
      margin-bottom: 1.5rem;
      padding: 1.5rem 1rem;
    }

    .container {
      .uploader {
        border-radius: 16px;

        .upload-form {
          padding: 1.5rem;

          .row {
            grid-template-columns: 1fr;
          }
        }
      }

      .list {
        .list-header {
          flex-direction: column;
          align-items: stretch;
          gap: 1rem;
          padding: 1rem;

          .sort {
            select {
              width: 100%;
            }
          }
        }

        .items {
          .item {
            padding: 1rem;

            .meta {
              flex-direction: column;
              align-items: stretch;
              gap: 1rem;

              .left,
              .right {
                width: 100%;
              }

              .right {
                justify-content: space-between;
              }
            }
          }
        }
      }
    }
  }
}

@media (max-width: 640px) {
  .yuzuki-resources {
  
    .container {
      padding: 0 0.75rem;
    }
  }
}
</style>