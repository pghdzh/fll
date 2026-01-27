<template>
  <div class="wiki-page">
    <!-- 背景轮播放在最底层 -->
    <div class="carousel">
      <img
        v-for="(src, idx) in randomFive"
        :key="idx"
        :src="src"
        class="carousel-image"
        :class="{ active: idx === currentIndex }"
      />
    </div>
    <header class="wiki-header">
      <div class="title">
        <h1>弗洛洛文本分享</h1>
        <p class="subtitle">在彼岸回响中，编织知识的篇章</p>
      </div>
      <div class="actions">
        <input
          v-model="search"
          class="search"
          placeholder="搜索标题或者标签..."
        />
        <button class="btn btn-new" @click="openCreate">新建词条</button>
      </div>
    </header>

    <main class="wiki-body">
      <div v-if="filteredEntries.length === 0" class="empty">
        没有找到匹配的词条 ✨
      </div>

      <ul class="entry-list">
        <li v-for="entry in filteredEntries" :key="entry.id" class="entry-card">
          <div class="entry-head">
            <div class="entry-meta" @click="openDetail(entry)">
              <div class="entry-title-wrap">
                <h2 class="entry-title">{{ entry.title }}</h2>
                <span class="entry-badge">#{{ entry.slug || "未设置" }}</span>
              </div>
              <div class="entry-info">
                <span class="meta-item">作者：{{ entry.author }}</span>
                <span class="meta-item"
                  >时间：{{ formatTime(entry.updatedAt) }}</span
                >
              </div>
            </div>

            <div class="entry-actions">
              <button
                class="like"
                :class="{ active: isLiked(entry.id) }"
                :aria-pressed="isLiked(entry.id)"
                @click.stop="toggleLike(entry.id)"
              >
                <img
                  :src="
                    isLiked(entry.id)
                      ? '/icons/heart-red-filled.svg'
                      : '/icons/heart-red-outline.svg'
                  "
                  alt="like"
                />
                <span class="like-count">{{ entry.likes }}</span>
              </button>
              <div class="edit-delete" v-if="canEdit(entry.id)">
                <button class="small" @click="openEdit(entry)">编辑</button>
                <button class="small danger" @click="remove(entry.id)">
                  删除
                </button>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </main>

    <!-- Edit/Create Modal -->
    <transition name="fade-zoom">
      <div class="modal-overlay" v-if="showModal">
        <div class="modal">
          <header class="modal-header">
            <h3>{{ editing ? "编辑词条" : "新建词条" }}</h3>
            <button class="close" @click="closeModal">✕</button>
          </header>
          <section class="modal-body">
            <label>
              标题
              <input v-model="form.title" placeholder="输入标题" />
            </label>

            <label>
              词条（短标签）
              <input
                v-model="form.slug"
                placeholder="比如：彩蛋、考据、点位等等"
              />
            </label>

            <label>
              作者
              <input v-model="form.author" placeholder="作者昵称" />
            </label>

            <label>
              内容
              <textarea
                v-model="form.content"
                rows="8"
                placeholder="在这里输入词条内容"
              ></textarea>
            </label>
          </section>
          <footer class="modal-footer">
            <button class="btn ghost" @click="closeModal">取消</button>
            <button class="btn" @click="submit">
              {{ editing ? "保存" : "创建" }}
            </button>
          </footer>
        </div>
      </div>
    </transition>

    <!-- Detail Modal -->
    <transition name="fade-zoom">
      <div class="modal-overlay" v-if="detailEntry">
        <div class="modal detail-modal">
          <header class="modal-header">
            <h3>{{ detailEntry.title }}</h3>
            <button class="close" @click="detailEntry = null">✕</button>
          </header>
          <section class="modal-body">
            <div class="detail-content">{{ detailEntry.content }}</div>
          </section>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted, onUnmounted } from "vue";
import { ElMessage } from "element-plus";
import {
  getWikiList,
  createWiki,
  updateWiki,
  deleteWiki,
  likeWiki,
} from "@/api/modules/wiki";

// 本地存储自己创建的词条 ID
const LS_MY_WIKI_IDS = "yuzuriha:wiki:my_ids";
const myWikiIds: string[] = JSON.parse(
  localStorage.getItem(LS_MY_WIKI_IDS) || "[]"
);
const markAsMine = (id: string | number) => {
  if (!myWikiIds.includes(String(id))) {
    myWikiIds.push(String(id));
    localStorage.setItem(LS_MY_WIKI_IDS, JSON.stringify(myWikiIds));
  }
};
const canEdit = (id: string | number) => myWikiIds.includes(String(id));

// 数据状态
const entries = ref<any[]>([]);

// 本地存储键
const LS_LIKED_IDS = "yuzuriha:wiki:liked_ids";
// 从 localStorage 读取已点赞 id 列表（字符串形式）
const likedIds = ref<string[]>(
  JSON.parse(localStorage.getItem(LS_LIKED_IDS) || "[]")
);

const showModal = ref(false);
const editing = ref(false);
const editingId = ref<string | number | null>(null);
const detailEntry = ref<any>(null);
const form = reactive({ title: "", slug: "", author: "", content: "" });
const search = ref("");

// 时间格式化
function formatTime(ts: string | number | null | undefined) {
  if (!ts) return "未知时间";
  const date = new Date(ts);
  if (isNaN(date.getTime())) return "未知时间";
  return `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(
    2,
    "0"
  )}-${String(date.getDate()).padStart(2, "0")}`;
}

// 加载词条列表
async function loadEntries() {
  try {
    const res: any = await getWikiList();
    entries.value = res.data.map((e: any) => ({
      ...e,
      createdAt: e.createdAt || e.created_at,
      updatedAt: e.updatedAt || e.updated_at,
    }));
  } catch (err) {
    console.error(err);
    ElMessage.error("加载词条失败");
  }
}

// 打开/关闭弹窗
function openCreate() {
  editing.value = false;
  editingId.value = null;
  form.title = "";
  form.slug = "";

  form.content = "";
  showModal.value = true;
}
function openEdit(entry: any) {
  if (!canEdit(entry.id)) {
    ElMessage.warning("只有创建者可以编辑");
    return;
  }
  editing.value = true;
  editingId.value = entry.id;
  form.title = entry.title;
  form.slug = entry.slug;
  form.author = entry.author;
  form.content = entry.content;
  showModal.value = true;
}
function closeModal() {
  showModal.value = false;
}

const canSubmit = computed(() => form.title.trim() && form.content.trim());

// 提交
async function submit() {
  if (!canSubmit.value) {
    ElMessage.warning("请填写标题和内容");
    return;
  }
  const payload = {
    title: form.title.trim(),
    author: form.author.trim() || "匿名",
    content: form.content.trim(),
    slug: null,
  };
  if (form.slug.trim()) payload.slug = form.slug.trim();
  try {
    if (editing.value && editingId.value) {
      await updateWiki(editingId.value, payload);
      ElMessage.success("编辑成功");
    } else {
      const res: any = await createWiki(payload);
      markAsMine(res.data.id);
      editingId.value = res.data.id;
      ElMessage.success("创建成功");
    }
    showModal.value = false;
    loadEntries();
  } catch (err) {
    console.error(err);
    ElMessage.error("提交失败");
  }
}

// 删除
async function remove(id: string | number) {
  if (!canEdit(id)) {
    ElMessage.warning("只有创建者可以删除");
    return;
  }
  if (!confirm("确认删除该词条？此操作不可撤销")) return;
  try {
    await deleteWiki(id);
    const index = myWikiIds.indexOf(String(id));
    if (index !== -1) myWikiIds.splice(index, 1);
    localStorage.setItem(LS_MY_WIKI_IDS, JSON.stringify(myWikiIds));
    ElMessage.success("删除成功");
    loadEntries();
  } catch (err) {
    console.error(err);
    ElMessage.error("删除失败");
  }
}

// 点赞
function persistLikedIds() {
  try {
    localStorage.setItem(LS_LIKED_IDS, JSON.stringify(likedIds.value));
  } catch (e) {
    console.warn("保存 likedIds 失败", e);
  }
}

// 判断是否已点赞（供模板绑定 class/aria-pressed）
function isLiked(id: string | number) {
  return likedIds.value.includes(String(id));
}

// 点赞 / 取消点赞（乐观更新，本地仅存 id，点赞数使用 entry.likes）
async function toggleLike(id: string | number) {
  const entry = entries.value.find((e) => e.id === id);
  if (!entry) return;

  const idStr = String(id);
  const wasLiked = likedIds.value.includes(idStr);

  // 乐观更新 UI（立即反映）
  if (wasLiked) {
    // 取消点赞：保证不低于 0
    entry.likes = Math.max(0, (entry.likes || 0) - 1);
    likedIds.value = likedIds.value.filter((x) => x !== idStr);
  } else {
    // 点赞
    entry.likes = (entry.likes || 0) + 1;
    likedIds.value.push(idStr);
  }
  persistLikedIds();

  try {
    // 调用后端（action: 'like' | 'unlike' | 'toggle'）
    // 我们明确传 'like' 或 'unlike'
    const action = wasLiked ? "unlike" : "like";
    await likeWiki(id, action);

    // 可选：如果后端在响应中返回了最新的 likes 数（res.data.likes），
    // 你可以在这里用后端值覆盖本地（示例注释）
    // const res = await likeWiki(id, action)
    // if (res?.data?.likes !== undefined) entry.likes = res.data.likes
  } catch (err) {
    // 出错则回滚乐观更新
    console.error("toggleLike error", err);
    if (wasLiked) {
      // 取消点赞失败 -> 重新标记为已点赞
      entry.likes = (entry.likes || 0) + 1;
      if (!likedIds.value.includes(idStr)) likedIds.value.push(idStr);
    } else {
      // 点赞失败 -> 取消之前增加的 count
      entry.likes = Math.max(0, (entry.likes || 0) - 1);
      likedIds.value = likedIds.value.filter((x) => x !== idStr);
    }
    persistLikedIds();
    ElMessage.error("点赞失败，请稍后重试");
  }
}

// 详情弹窗
async function openDetail(entry: any) {
  detailEntry.value = entry;
}

// 搜索过滤
const filteredEntries = computed(() => {
  const q = String(search.value || "")
    .trim()
    .toLowerCase();
  let list = entries.value;

  // 搜索过滤
  if (q) {
    list = list.filter(
      (e) =>
        (e.title || "").toLowerCase().includes(q) ||
        (e.slug || "").toLowerCase().includes(q)
    );
  }

  // 按点赞数排序（默认降序：点赞多的在前）
  return [...list].sort((a, b) => (b.likes || 0) - (a.likes || 0));
});

// 1. 分别导入两套图
const pcModules = import.meta.glob("@/assets/images1/*.{jpg,png,jpeg,webp}", {
  eager: true,
});
const mobileModules = import.meta.glob(
  "@/assets/images2/*.{jpg,png,jpeg,webp}",
  { eager: true }
);

const pcSrcs: string[] = Object.values(pcModules).map((m: any) => m.default);
const mobileSrcs: string[] = Object.values(mobileModules).map(
  (m: any) => m.default
);

// 洗牌函数
function shuffle<T>(arr: T[]): T[] {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}

const randomFive = ref<string[]>([]);
const currentIndex = ref(0);
let timer: number;

function pickImages() {
  const isMobile = window.innerWidth < 768;
  const all = isMobile ? mobileSrcs : pcSrcs;
  randomFive.value = shuffle(all).slice(0, 5);
}

onMounted(() => {
  loadEntries();
  pickImages(); // 首次判断
  // 监听窗口大小变化
  window.addEventListener("resize", pickImages);

  // 轮播
  timer = window.setInterval(() => {
    if (randomFive.value.length > 0) {
      currentIndex.value = (currentIndex.value + 1) % randomFive.value.length;
    }
  }, 5000);
});

onUnmounted(() => {
  clearInterval(timer);
  window.removeEventListener("resize", pickImages);
});
</script>
<style scoped lang="scss">

/* ==== 主页面样式 ==== */
.wiki-page {
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

  min-height: 100vh;
  color: var(--text-primary);
  position: relative;
  overflow-x: hidden;
  padding-top: 80px;
  background: radial-gradient(
      ellipse at 20% 10%,
      var(--glow-magenta) 0%,
      transparent 40%
    ),
    radial-gradient(ellipse at 80% 90%, var(--glow-crimson) 0%, transparent 40%),
    linear-gradient(180deg, var(--deep-space) 0%, var(--void-purple) 100%);
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
      var(--glow-magenta) 1px,
      var(--glow-magenta) 2px
    );
    opacity: 0.02;
    pointer-events: none;
    z-index: 0;
    animation: scanlines 20s linear infinite;
  }

  /* ==== 轮播图背景 ==== */
  .carousel {
    position: fixed;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    opacity: 0.15;
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
      filter: blur(4px) brightness(0.6) contrast(1.1) saturate(1.2);
      opacity: 0;
      transition: opacity 2s cubic-bezier(0.4, 0, 0.2, 1);
      transform: scale(1.02);

      &.active {
        opacity: 1;
        animation: floatImage 25s ease-in-out infinite;
      }
    }
  }

  /* ==== 页面头部 ==== */
  .wiki-header {
    position: relative;
    z-index: 10;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 20px;
    padding: 1.5rem 2rem;
    margin: 0 auto 2rem;
    max-width: 1200px;
    background: var(--glass-dark);
    backdrop-filter: blur(20px) saturate(180%);
    border-radius: 24px;
    border: 1px solid var(--border-faint);
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4), 0 8px 32px rgba(0, 0, 0, 0.3),
      inset 0 1px 0 rgba(255, 255, 255, 0.05);
    transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);

    &:hover {
      transform: translateY(-4px);
      box-shadow: 0 30px 80px rgba(184, 107, 224, 0.2),
        0 20px 60px rgba(0, 0, 0, 0.5);
    }

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

    .title {
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

      .subtitle {
        margin-top: 0.5rem;
        color: var(--text-secondary);
        font-size: clamp(0.9rem, 2vw, 1.1rem);
        font-weight: 300;
        letter-spacing: 0.1em;
        text-transform: uppercase;
        opacity: 0.8;
      }
    }

    .actions {
      display: flex;
      gap: 1rem;
      align-items: center;
      flex-wrap: wrap;

      .search {
        padding: 0.75rem 1.25rem;
        border-radius: 12px;
        border: 1px solid var(--border-faint);
        background: rgba(10, 8, 16, 0.4);
        color: var(--text-primary);
        font-size: 0.95rem;
        min-width: 240px;
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

      .btn-new {
        background: linear-gradient(
          135deg,
          var(--accent-magenta) 0%,
          var(--accent-crimson) 100%
        );
        color: var(--text-primary);
        border: none;
        border-radius: 12px;
        padding: 0.875rem 1.75rem;
        font-size: 1rem;
        font-weight: 600;
        letter-spacing: 0.05em;
        cursor: pointer;
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        position: relative;
        overflow: hidden;

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

  /* ==== 主体内容 ==== */
  .wiki-body {
    position: relative;
    z-index: 10;
    max-width: 1200px;
    margin: 0 auto 3rem;
    padding: 0 1rem;

    .empty {
      text-align: center;
      padding: 4rem 2rem;
      color: var(--text-secondary);
      font-size: 1.1rem;
      background: var(--glass-light);
      backdrop-filter: blur(10px);
      border-radius: 20px;
      border: 1px solid var(--border-faint);
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);

      &::before {
        content: "📚";
        display: block;
        font-size: 3rem;
        margin-bottom: 1rem;
        opacity: 0.6;
        animation: float 4s ease-in-out infinite;
      }
    }

    .entry-list {
      display: grid;
      gap: 1.25rem;
      list-style: none;
      padding: 0;
      margin: 0;

      .entry-card {
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
          transform: translateY(-8px) translateX(4px);
          border-color: var(--accent-magenta);
          box-shadow: 0 30px 80px var(--glow-magenta),
            0 20px 60px rgba(0, 0, 0, 0.4);

          &::before {
            left: 100%;
          }

          .entry-title {
            color: var(--accent-crimson);
          }
        }

        .entry-head {
          display: flex;
          justify-content: space-between;
          align-items: flex-start;
          gap: 1.5rem;

          .entry-meta {
            flex: 1;
            cursor: pointer;

            .entry-title-wrap {
              display: flex;
              align-items: center;
              gap: 1rem;
              margin-bottom: 0.75rem;

              .entry-title {
                margin: 0;
                font-size: 1.4rem;
                font-weight: 700;
                color: var(--text-primary);
                transition: color 0.3s ease;
                line-height: 1.3;
              }

              .entry-badge {
                padding: 0.375rem 0.875rem;
                border-radius: 100px;
                font-size: 0.8rem;
                font-weight: 600;
                background: linear-gradient(
                  135deg,
                  rgba(184, 107, 224, 0.15) 0%,
                  rgba(255, 107, 133, 0.15) 100%
                );
                border: 1px solid var(--border-faint);
                color: var(--text-secondary);
                text-transform: uppercase;
                letter-spacing: 0.05em;
              }
            }

            .entry-info {
              display: flex;
              flex-wrap: wrap;
              gap: 1rem;

              .meta-item {
                font-size: 0.9rem;
                color: var(--text-secondary);
                display: flex;
                align-items: center;
                gap: 0.25rem;

                &::before {
                  content: "";
                  display: inline-block;
                  width: 4px;
                  height: 4px;
                  border-radius: 50%;
                  background: var(--accent-magenta);
                  margin-right: 0.5rem;
                  opacity: 0.6;
                }
              }
            }
          }

          .entry-actions {
            display: flex;
            align-items: center;
            gap: 1rem;

            .like {
              display: flex;
              align-items: center;
              gap: 0.5rem;
              background: none;
              border: none;
              color: var(--text-secondary);
              cursor: pointer;
              padding: 0.5rem 1rem;
              border-radius: 10px;
              transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
              position: relative;

              &::before {
                content: "";
                position: absolute;
                inset: 0;
                border-radius: 10px;
                background: var(--glow-magenta);
                opacity: 0;
                transition: opacity 0.3s ease;
              }

              &:hover {
                color: var(--accent-crimson);

                &::before {
                  opacity: 0.1;
                }

                img {
                  transform: scale(1.3);
                  animation: pulseHeart 1s ease-in-out;
                }
              }

              &.active {
                color: var(--accent-crimson);

                img {
                  filter: drop-shadow(0 0 8px var(--glow-crimson));
                  animation: heartbeat 1.5s ease-in-out infinite;
                }

                .like-count {
                  color: var(--accent-crimson);
                }
              }

              img {
                width: 22px;
                height: 22px;
                transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
                position: relative;
                z-index: 1;
              }

              .like-count {
                font-weight: 700;
                position: relative;
                z-index: 1;
                transition: color 0.3s ease;
                min-width: 20px;
                text-align: center;
              }
            }

            .edit-delete {
              display: flex;
              gap: 0.5rem;

              .small {
                padding: 0.5rem 1rem;
                border-radius: 10px;
                border: 1px solid var(--border-faint);
                background: rgba(184, 107, 224, 0.08);
                color: var(--text-secondary);
                font-size: 0.85rem;
                font-weight: 500;
                cursor: pointer;
                transition: all 0.3s ease;

                &:hover {
                  background: rgba(184, 107, 224, 0.15);
                  border-color: var(--accent-magenta);
                  color: var(--text-primary);
                  transform: translateY(-2px);
                }

                &.danger {
                  background: rgba(255, 107, 133, 0.08);
                  color: var(--accent-crimson);

                  &:hover {
                    background: rgba(255, 107, 133, 0.15);
                    border-color: var(--accent-crimson);
                  }
                }
              }
            }
          }
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

    .modal {
      width: min(800px, 100%);
      max-height: 90vh;
      overflow-y: auto;
      background: var(--glass-dark);
      backdrop-filter: blur(30px);
      border-radius: 24px;
      border: 1px solid var(--border-faint);
      box-shadow: 0 40px 100px rgba(0, 0, 0, 0.5),
        0 20px 60px rgba(184, 107, 224, 0.2);

      .modal-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 1.5rem 2rem;
        border-bottom: 1px solid var(--border-faint);

        h3 {
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

        .close {
          background: none;
          border: none;
          color: var(--text-secondary);
          font-size: 1.5rem;
          cursor: pointer;
          width: 40px;
          height: 40px;
          display: flex;
          align-items: center;
          justify-content: center;
          border-radius: 50%;
          transition: all 0.3s ease;

          &:hover {
            background: rgba(184, 107, 224, 0.1);
            color: var(--accent-crimson);
            transform: rotate(90deg);
          }
        }
      }

      .modal-body {
        padding: 2rem;
        color: var(--text-primary);

        label {
          display: block;
          margin-bottom: 1.5rem;
          font-size: 0.95rem;
          color: var(--text-secondary);
          font-weight: 500;

          input,
          textarea {
            width: 100%;
            margin-top: 0.5rem;
            padding: 0.875rem 1rem;
            border-radius: 12px;
            border: 1px solid var(--border-faint);
            background: rgba(10, 8, 16, 0.4);
            color: var(--text-primary);
            font-size: 1rem;
            transition: all 0.3s ease;

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
          }

          textarea {
            min-height: 200px;
            resize: vertical;
            line-height: 1.6;
          }
        }

        .detail-content {
          white-space: pre-wrap;
          line-height: 1.8;
          font-size: 1.1rem;
          color: var(--text-primary);
          background: rgba(10, 8, 16, 0.2);
          padding: 1.5rem;
          border-radius: 12px;
          border: 1px solid var(--border-faint);
        }
      }

      .modal-footer {
        padding: 1.5rem 2rem;
        border-top: 1px solid var(--border-faint);
        display: flex;
        justify-content: flex-end;
        gap: 1rem;

        .btn {
          padding: 0.75rem 1.75rem;
          border-radius: 12px;
          border: none;
          font-size: 1rem;
          font-weight: 600;
          cursor: pointer;
          transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

          &.ghost {
            background: transparent;
            border: 1px solid var(--border-faint);
            color: var(--text-secondary);

            &:hover {
              background: rgba(184, 107, 224, 0.1);
              border-color: var(--accent-magenta);
              color: var(--text-primary);
            }
          }

          &:not(.ghost) {
            background: linear-gradient(
              135deg,
              var(--accent-magenta) 0%,
              var(--accent-crimson) 100%
            );
            color: var(--text-primary);

            &:hover {
              transform: translateY(-2px);
              box-shadow: 0 15px 40px var(--glow-magenta),
                0 10px 30px var(--glow-crimson);
            }
          }
        }
      }

      &.detail-modal {
        .modal-body {
          padding: 2rem;
        }
      }
    }
  }

  /* ==== 过渡动画 ==== */
  .fade-zoom-enter-active,
  .fade-zoom-leave-active {
    transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  }

  .fade-zoom-enter-from,
  .fade-zoom-leave-to {
    opacity: 0;
    transform: scale(0.95);
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

@keyframes heartbeat {
  0%,
  100% {
    transform: scale(1);
  }
  25% {
    transform: scale(1.2);
  }
  50% {
    transform: scale(1.1);
  }
  75% {
    transform: scale(1.15);
  }
}

@keyframes pulseHeart {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.4);
  }
  100% {
    transform: scale(1);
  }
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
}

/* ==== 响应式设计 ==== */
@media (max-width: 768px) {
  .wiki-page {
    padding-top: 60px;
    background: var(--mobile-bg);

    .wiki-header {
      flex-direction: column;
      align-items: stretch;
      gap: 1rem;
      padding: 1.25rem;
      margin: 0 0.5rem 1.5rem;
      border-radius: 16px;

      .title {
        text-align: center;

        h1 {
          font-size: 1.5rem;
        }

        .subtitle {
          font-size: 0.85rem;
        }
      }

      .actions {
        flex-direction: column;

        .search {
          min-width: 100%;
          order: 2;
        }

        .btn-new {
          order: 1;
          width: 100%;
        }
      }
    }

    .wiki-body {
      padding: 0 0.5rem;

      .entry-list {
        .entry-card {
          padding: 1.25rem;

          .entry-head {
            flex-direction: column;
            gap: 1rem;

            .entry-actions {
              width: 100%;
              justify-content: space-between;
              margin-top: 1rem;

              .like {
                flex: 1;
              }

              .edit-delete {
                flex: 1;
                justify-content: flex-end;
              }
            }
          }
        }
      }
    }

    .modal-overlay {
      .modal {
        width: 100%;
        max-height: 95vh;
        border-radius: 16px;

        .modal-header {
          padding: 1.25rem;
        }

        .modal-body {
          padding: 1.25rem;
        }

        .modal-footer {
          padding: 1.25rem;
          flex-direction: column;

          .btn {
            width: 100%;
          }
        }
      }
    }
  }
}

@media (max-width: 480px) {
  .wiki-page {
    .wiki-body {
      .entry-list {
        .entry-card {
          .entry-head {
            .entry-title-wrap {
              flex-direction: column;
              align-items: flex-start;
              gap: 0.5rem;

              .entry-badge {
                align-self: flex-start;
              }
            }
          }
        }
      }
    }
  }
}
</style>