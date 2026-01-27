<template>
  <div class="florlo-gallery-container">
    <!-- 背景效果 -->
    <div class="gallery-background">
      <div class="particle-field"></div>
      <div class="musical-staff"></div>
    </div>

    <!-- 上传按钮 -->
    <button class="florlo-upload-btn" @click="openUploadModal">
      <div class="btn-icon">♫</div>
      <span>上传图片</span>
      <div class="btn-glow"></div>
    </button>

    <section class="gallery section">
      <div class="sort-controls">
        <!-- 排序按钮 -->
        <div>
          <button @click="toggleSort" class="florlo-sort-btn">
            <span class="sort-icon">↕</span>
            <span
              >按
              {{ sortBy === "like_count" ? "点赞量" : "最新上传" }} 排序</span
            >
            <div class="sort-wave"></div>
          </button>
        </div>

        <!-- 点赞筛选按钮 -->
        <div class="filter-controls">
          <button
            class="florlo-filter-btn liked-filter"
            @click="toggleLikedFilter"
            :class="{ active: showLikedOnly }"
            :disabled="
              isLoadingLikedImages || (likedIds.length === 0 && !showLikedOnly)
            "
            :title="showLikedOnly ? '显示所有图片' : '只看点赞过的图片'"
          >
            <div class="filter-heart">
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path
                  d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                />
              </svg>
            </div>
            <span class="filter-text">
              {{ showLikedOnly ? "所有图片" : "只看点赞" }}
            </span>
            <span
              class="filter-count"
              v-if="showLikedOnly && likedImages.length > 0"
            >
              {{ likedImages.length }}
            </span>
            <span class="loading-spinner" v-if="isLoadingLikedImages"></span>
            <div class="filter-glow"></div>
          </button>

          <!-- 筛选状态提示 -->
          <transition name="fade">
            <div v-if="showLikedOnly" class="filter-info">
              <div class="info-icon">♬</div>
              <span class="info-text">
                {{
                  likedImages.length > 0
                    ? `已筛选：${likedImages.length} 张点赞图片`
                    : "暂无点赞图片"
                }}
                <span v-if="isLoadingLikedImages" class="loading-text"
                  >（加载中...）</span
                >
              </span>
              <button
                class="clear-filter"
                @click="clearLikedFilter"
                title="清除筛选"
              >
                <svg
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                >
                  <path d="M6 18L18 6M6 6l12 12" />
                </svg>
              </button>
            </div>
          </transition>
        </div>
      </div>

      <!-- 画廊网格 -->
      <div class="florlo-gallery-grid">
        <!-- 筛选模式下的空状态 -->
        <div
          v-if="
            showLikedOnly && likedImages.length === 0 && !isLoadingLikedImages
          "
          class="filter-empty"
        >
          <div class="empty-icon">♪</div>
          <h3>暂无点赞图片</h3>
          <p>快去为你喜欢的图片点赞吧！</p>
        </div>

        <!-- 图片卡片 -->
        <div
          v-for="(img, index) in displayedImages"
          :key="img.id"
          class="florlo-card"
          @click="openLightbox(getDisplayedIndex(img.id, index))"
          ref="cards"
          :class="{ 'liked-card': img.liked, loaded: img.liked }"
        >
          <div class="card-inner">
            <div class="card-frame">
              <!-- 图片加载前的骨架屏 -->
              <div class="image-skeleton"></div>
              <img
                :src="img.src"
                :alt="img.alt"
                loading="lazy"
                @load="onImageLoad($event)"
                @error="handleImageError"
              />
              <!-- 卡片装饰 -->
              <div class="card-decoration">
                <div class="decoration-note">♫</div>
                <div class="decoration-line"></div>
              </div>
            </div>
            <div class="card-overlay">
              <div class="overlay-content">
                <span class="view-text">查看大图</span>
                <div class="overlay-note">♬</div>
              </div>
            </div>
            <button class="florlo-like-btn" @click.stop="handleLike(img)">
              <div class="heart-container">
                <svg
                  v-if="!img.liked"
                  class="heart-icon"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="#ff6b85"
                  stroke-width="2"
                >
                  <path
                    d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                  />
                </svg>
                <svg
                  v-else
                  class="heart-icon liked"
                  viewBox="0 0 24 24"
                  fill="#ff4757"
                  stroke="#ff4757"
                  stroke-width="2"
                >
                  <path
                    d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                  />
                </svg>
                <div class="heart-glow" v-if="img.liked"></div>
              </div>
              <span class="like-count">{{ img.likeCount }}</span>
            </button>
          </div>
        </div>
      </div>

      <!-- sentinel：用于触发无限滚动 -->
      <div ref="sentinel" class="sentinel"></div>

      <!-- 可选：加载中/结束提示 -->
      <div class="loading-state" v-if="loading && !showLikedOnly">
        <div class="loading-spinner large"></div>
        <span>加载中...</span>
      </div>
      <div class="finished-state" v-if="finished && !showLikedOnly">
        <div class="finished-icon">♩</div>
        <span>已全部加载</span>
      </div>
    </section>

    <!-- 排行榜面板 -->
    <aside class="florlo-ranking-panel">
      <div class="panel-header" @click="expanded = !expanded">
        <div class="panel-icon">🏆</div>
        <h3 class="ranking-title">上传排行榜</h3>
        <span class="ranking-count">共{{ imageTotal }}张</span>
        <span class="toggle-icon">{{ expanded ? "▾" : "▸" }}</span>
        <div class="panel-glow"></div>
      </div>
      <transition name="panel-expand">
        <ul v-if="expanded" class="ranking-list">
          <li
            v-for="(item, idx) in rankingList"
            :key="idx"
            class="ranking-item"
            :class="`rank-${idx + 1}`"
          >
            <div class="rank-decoration">
              <span class="rank-number">{{ idx + 1 }}</span>
              <div class="rank-glow"></div>
            </div>
            <span class="rank-name">{{ item.nickname }}</span>
            <span class="rank-count">{{ item.count }} 张</span>
          </li>
        </ul>
      </transition>
    </aside>

    <!-- Lightbox Modal -->
    <div
      v-if="lightboxOpen"
      class="florlo-lightbox"
      @click.self="closeLightbox"
      @wheel.prevent="handleLightboxWheel"
    >
      <!-- 控制按钮 -->
      <div class="lightbox-controls">
        <button class="lightbox-close" @click="closeLightbox" title="关闭">
          <svg
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
          >
            <path d="M6 18L18 6M6 6l12 12" />
          </svg>
          <div class="close-glow"></div>
        </button>

        <div class="lightbox-toolbar">
          <!-- 缩放控制 -->
          <div class="zoom-controls">
            <button
              @click.stop="zoomOut"
              :disabled="zoomLevel <= 0.5"
              title="缩小"
              class="zoom-btn"
            >
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path d="M5 12h14" />
              </svg>
            </button>
            <span class="zoom-level">{{ Math.round(zoomLevel * 100) }}%</span>
            <button
              @click.stop="zoomIn"
              :disabled="zoomLevel >= 3"
              title="放大"
              class="zoom-btn"
            >
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path d="M12 5v14M5 12h14" />
              </svg>
            </button>
            <button @click.stop="resetZoom" title="重置缩放" class="zoom-reset">
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path d="M3 12a9 9 0 1018 0 9 9 0 00-18 0z" />
                <path d="M9 12h6M12 9v6" />
              </svg>
            </button>
          </div>

          <!-- 点赞按钮 -->
          <button
            class="lightbox-like-btn"
            @click.stop="handleLike(currentImage)"
            :class="{ liked: currentImage?.liked }"
            :title="currentImage?.liked ? '已点赞' : '点赞'"
          >
            <div class="like-container">
              <svg
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
              >
                <path
                  d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                />
              </svg>
              <span class="like-count">{{ currentImage?.likeCount || 0 }}</span>
            </div>
            <div class="like-pulse" v-if="currentImage?.liked"></div>
          </button>

          <!-- 下载按钮 -->
          <button
            class="lightbox-download-btn"
            @click.stop="downloadImage"
            title="下载图片"
          >
            <svg
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
            >
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4" />
              <polyline points="7 10 12 15 17 10" />
              <line x1="12" y1="15" x2="12" y2="3" />
            </svg>
            <div class="download-glow"></div>
          </button>

          <!-- 导航信息 -->
          <div class="nav-info">
            <span class="current-index">{{ currentIndex + 1 }}</span>
            <span class="separator">/</span>
            <span class="total-count">{{ images.length }}</span>
          </div>
        </div>
      </div>

      <!-- 图片容器 -->
      <div
        class="lightbox-image-container"
        :style="{
          transform: `scale(${zoomLevel})`,
          transformOrigin: `${panOrigin.x}% ${panOrigin.y}%`,
        }"
        @mousedown="startPan"
        @touchstart="startPan"
        @mousemove="doPan"
        @touchmove="doPan"
        @mouseup="endPan"
        @touchend="endPan"
        @mouseleave="endPan"
      >
        <img
          :src="currentImage?.src"
          :alt="currentImage?.alt"
          :style="{
            cursor: isPanning ? 'grabbing' : zoomLevel > 1 ? 'grab' : 'default',
          }"
          @load="onLightboxImageLoad"
          @error="handleLightboxImageError"
        />
        <div class="image-frame"></div>
      </div>

      <!-- 导航按钮 -->
      <button class="lightbox-nav prev" @click.stop="prevImage" title="上一张">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <polyline points="15 18 9 12 15 6" />
        </svg>
        <div class="nav-glow"></div>
      </button>
      <button class="lightbox-nav next" @click.stop="nextImage" title="下一张">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
        >
          <polyline points="9 18 15 12 9 6" />
        </svg>
        <div class="nav-glow"></div>
      </button>

      <!-- 拖拽提示 -->
      <div v-if="zoomLevel > 1 && !isPanning" class="pan-hint">
        <div class="hint-icon">⇲</div>
        <span>拖拽查看大图</span>
      </div>
    </div>

    <!-- 上传弹窗 -->
    <div
      v-if="uploadModalOpen"
      class="florlo-upload-modal-overlay"
      @click.self="closeUploadModal"
    >
      <div class="upload-modal">
        <div class="modal-header">
          <div class="header-icon">📤</div>
          <h3>批量上传图片</h3>
          <div class="header-note">♫</div>
        </div>

        <div class="tip-container">
          <div class="tip-header">
            <div class="tip-icon">⚠</div>
            <h4>上传须知</h4>
          </div>
          <ul class="tips-list">
            <li>
              <span class="list-bullet">•</span
              >审核规则：1.不要色情倾向（我怕被封） 2.要我能认出是弗洛洛。
            </li>
            <li>
              <span class="list-bullet">•</span
              >由于没有用户系统，我这边不好做审核反馈，但只要显示上传成功，我这边肯定能收到。
            </li>
            <li>
              <span class="list-bullet">•</span
              >如果图片数量较多请在b站私信联系我给我网盘链接，因为我云服务器比较小一次性上传太多图片可能会导致上传不上，感谢理解。
            </li>
            <li>
              <span class="list-bullet">•</span
              >因为审核上传一次比较麻烦，所以审核时间不定，最晚一周，感谢谅解。
            </li>
          </ul>
        </div>

        <div class="upload-stats">
          <div class="stat-item">
            <span class="stat-label">今日已上传：</span>
            <span class="stat-value highlight">{{ uploadedToday }}</span>
            <span class="stat-unit">张</span>
          </div>
          <div class="stat-item">
            <span class="stat-label">剩余可上传：</span>
            <span class="stat-value highlight">{{ remaining }}</span>
            <span class="stat-unit">张</span>
          </div>
        </div>

        <div class="modal-form">
          <label class="form-label">
            <div class="label-icon">👤</div>
            <span>昵称：</span>
            <input
              v-model="nickname"
              type="text"
              placeholder="请输入昵称"
              class="form-input"
            />
            <div class="input-glow"></div>
          </label>

          <label class="form-label">
            <div class="label-icon">🖼️</div>
            <span>选择图片（最多 {{ remaining }} 张）：</span>
            <div class="file-input-wrapper">
              <input
                ref="fileInput"
                type="file"
                multiple
                accept="image/*"
                @change="handleFileSelect"
                class="file-input"
              />
              <div class="file-input-overlay">
                <div class="overlay-icon">+</div>
                <span>选择文件</span>
              </div>
            </div>
          </label>

          <div class="selected-files" v-if="selectedFiles.length">
            <div class="files-icon">📎</div>
            <span class="files-count">已选 {{ selectedFiles.length }} 张</span>
          </div>
        </div>

        <div class="modal-actions">
          <button
            :disabled="!canSubmit || isUploading"
            @click="submitUpload"
            class="submit-btn"
          >
            <div class="btn-spinner" v-if="isUploading"></div>
            <span v-if="!isUploading">立即上传</span>
            <span v-else>上传中...</span>
            <div class="btn-glow"></div>
          </button>
          <button class="cancel-btn" @click="closeUploadModal">取消</button>
        </div>
      </div>
    </div>

    <!-- 浮动角色 -->
    <div class="floating-chibis">
      <img
        v-for="(pet, i) in chibiList"
        :key="i"
        :src="pet.src"
        :style="{ top: pet.top + 'px', left: pet.left + 'px' }"
        class="chibi-img"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
// 保持原有的脚本逻辑完全不变
import {
  ref,
  onMounted,
  computed,
  nextTick,
  onBeforeUnmount,
  watch,
} from "vue";
import { uploadImages } from "@/api/modules/images";
import { getRankingList } from "@/api/modules/ranking";
import { gsap } from "gsap";
import {
  getImagesLikesList,
  likeImage,
  getImagesByIds,
} from "@/api/modules/imagesLikes";
import { debounce } from "lodash";

const sortBy = ref<"uploaded_at" | "like_count">("like_count");
const order = ref<"asc" | "desc">("desc");
function toggleSort() {
  if (sortBy.value === "uploaded_at") {
    sortBy.value = "like_count";
    order.value = "desc";
  } else {
    sortBy.value = "uploaded_at";
    order.value = "desc";
  }
  pageImage.value = 1;
  images.value = [];
  finished.value = false;
  window.scrollTo(0, 0);
  loadNextPage();
}

function getLikedIds(): number[] {
  const data = localStorage.getItem("likedImageIds");
  return data ? JSON.parse(data) : [];
}

function setLikedIds(ids: number[]) {
  localStorage.setItem("likedImageIds", JSON.stringify(ids));
}

const showLikedOnly = ref(false);
const likedImages = ref<ImageItem[]>([]);
const isLoadingLikedImages = ref(false);

const likedIds = computed(() => {
  return getLikedIds();
});

const displayedImages = computed(() => {
  if (showLikedOnly.value) {
    return likedImages.value;
  }
  return images.value;
});

const fetchLikedImages = async () => {
  if (isLoadingLikedImages.value) return;

  isLoadingLikedImages.value = true;
  try {
    const likedIdsArray = getLikedIds();

    if (likedIdsArray.length === 0) {
      likedImages.value = [];
      return;
    }

    const res = await getImagesByIds({
      ids: likedIdsArray,
      character_key: "fll",
    });

    if (res.success) {
      likedImages.value = res.images.map((item) => ({
        src: item.url,
        alt: "",
        likeCount: item.like_count || 0,
        id: item.id,
        liked: true,
      }));
    } else {
      console.error("获取点赞图片失败:", res.message);
      likedImages.value = images.value.filter((img) => img.liked);
    }
  } catch (error) {
    console.error("获取点赞图片错误:", error);
    likedImages.value = images.value.filter((img) => img.liked);
  } finally {
    isLoadingLikedImages.value = false;
  }
};

const toggleLikedFilter = async () => {
  if (showLikedOnly.value) {
    showLikedOnly.value = false;
    pageImage.value = 1;
    images.value = [];
    finished.value = false;
    loading.value = false;

    await nextTick();
    if (sentinel.value && sentinelObserver) {
      sentinelObserver.unobserve(sentinel.value);
      sentinelObserver.observe(sentinel.value);
    }

    loadNextPage();
  } else {
    showLikedOnly.value = true;
    likedImages.value = [];

    likedImages.value = images.value.filter((img) => img.liked);

    fetchLikedImages();

    window.scrollTo({ top: 0, behavior: "smooth" });

    showFilterAnimation();
  }
};

const clearLikedFilter = () => {
  showLikedOnly.value = false;
  pageImage.value = 1;
  images.value = [];
  finished.value = false;
  loading.value = false;

  nextTick(() => {
    if (sentinel.value && sentinelObserver) {
      sentinelObserver.unobserve(sentinel.value);
      sentinelObserver.observe(sentinel.value);
    }
    loadNextPage();
  });
};

const showFilterAnimation = () => {
  setTimeout(() => {
    const likedCards = document.querySelectorAll(".florlo-card");
    likedCards.forEach((card, index) => {
      const img = card.querySelector("img");
      if (
        img?.getAttribute("src") &&
        likedImages.value.some(
          (likedImg) => likedImg.src === img.getAttribute("src")
        )
      ) {
        card.classList.add("liked-highlight");
        setTimeout(() => {
          card.classList.remove("liked-highlight");
        }, 1000 + index * 100);
      }
    });
  }, 100);
};

const getDisplayedIndex = (imgId: number, currentIndex: number) => {
  if (showLikedOnly.value) {
    return currentIndex;
  }
  return images.value.findIndex((img) => img.id === imgId);
};

const handleLike = async (img: ImageItem) => {
  if (img.liked) return;

  try {
    await likeImage(img.id);
    img.likeCount += 1;
    img.liked = true;

    const likedIds = getLikedIds();
    likedIds.push(img.id);
    setLikedIds(likedIds);

    if (showLikedOnly.value) {
      if (!likedImages.value.some((item) => item.id === img.id)) {
        likedImages.value.push({ ...img });
      }

      const card = Array.from(document.querySelectorAll(".florlo-card")).find(
        (card) => {
          const cardImg = card.querySelector("img");

          return cardImg?.getAttribute("src") === img.src;
        }
      );
      if (card) {
        card.classList.add("new-liked");

        setTimeout(() => {
          card.classList.remove("new-liked");
        }, 1500);
      }
    }
  } catch (error) {
    console.error("点赞失败", error);
    alert("点赞失败，请稍后重试");
  }
};

interface ImageItem {
  src: string;
  alt: string;
  likeCount: number;
  id: number;
  liked: Boolean;
}

interface RankingItem {
  id?: number;
  nickname: string;
  count: number;
}
const rankingList = ref<RankingItem[]>([]);
const expanded = ref(true);

const page = 1;
const pageSize = 99;

const fetchRanking = async () => {
  const res = await getRankingList({ page, pageSize, character_key: "fll" });
  if (res.success) {
    rankingList.value = res.data;
  } else {
    console.error("获取排行榜失败", res.message);
  }
};

const images = ref<ImageItem[]>([]);
const imageTotal = ref(0);
const pageImage = ref(1);
const limit = ref(10);
const loading = ref(false);
const finished = ref(false);

const sentinel = ref<HTMLElement | null>(null);

async function loadNextPage() {
  if (loading.value || finished.value) return;
  loading.value = true;
  try {
    const res = await getImagesLikesList({
      page: pageImage.value,
      limit: limit.value,
      sortBy: sortBy.value,
      character_key: "fll",
      order: order.value,
    });
    const likedIds = getLikedIds();
    const list = (
      res.images as Array<{ url: string; like_count: number; id: number }>
    ).map((item) => ({
      src: item.url,
      alt: "",
      likeCount: item.like_count,
      id: item.id,
      liked: likedIds.includes(item.id),
    }));
    if (list.length === 0) {
      finished.value = true;
      return;
    }

    const existingIds = new Set(images.value.map((i) => i.id));
    const filtered = list.filter((item) => !existingIds.has(item.id));
    images.value.push(...filtered);
    pageImage.value++;
    imageTotal.value = res.total;
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
}

const debouncedLoad = debounce(
  () => {
    loadNextPage();
  },
  200,
  { leading: true, trailing: false }
);

const lightboxOpen = ref(false);
const currentIndex = ref(0);
const zoomLevel = ref(1);
const isPanning = ref(false);
const panStart = ref({ x: 0, y: 0 });
const panOrigin = ref({ x: 50, y: 50 });

const currentImage = computed(() => {
  return images.value[currentIndex.value];
});

const onLightboxImageLoad = () => {
  resetZoom();
};

const zoomIn = () => {
  if (zoomLevel.value < 3) {
    zoomLevel.value = Math.min(zoomLevel.value + 0.25, 3);
    panOrigin.value = { x: 50, y: 50 };
  }
};

const zoomOut = () => {
  if (zoomLevel.value > 0.5) {
    zoomLevel.value = Math.max(zoomLevel.value - 0.25, 0.5);
    if (zoomLevel.value <= 1) {
      panOrigin.value = { x: 50, y: 50 };
    }
  }
};

const resetZoom = () => {
  zoomLevel.value = 1;
  panOrigin.value = { x: 50, y: 50 };
};

const handleLightboxWheel = (e: WheelEvent) => {
  e.preventDefault();
  if (e.deltaY < 0) {
    zoomIn();
  } else {
    zoomOut();
  }
};

const startPan = (e: MouseEvent | TouchEvent) => {
  if (zoomLevel.value <= 1) return;

  isPanning.value = true;
  const clientX = "touches" in e ? e.touches[0].clientX : e.clientX;
  const clientY = "touches" in e ? e.touches[0].clientY : e.clientY;

  panStart.value = { x: clientX, y: clientY };
};

const doPan = (e: MouseEvent | TouchEvent) => {
  if (!isPanning.value || zoomLevel.value <= 1) return;

  const clientX = "touches" in e ? e.touches[0].clientX : e.clientX;
  const clientY = "touches" in e ? e.touches[0].clientY : e.clientY;

  const deltaX = clientX - panStart.value.x;
  const deltaY = clientY - panStart.value.y;

  const sensitivity = 0.5;
  panOrigin.value = {
    x: Math.max(
      0,
      Math.min(
        100,
        panOrigin.value.x - (deltaX * sensitivity) / zoomLevel.value
      )
    ),
    y: Math.max(
      0,
      Math.min(
        100,
        panOrigin.value.y - (deltaY * sensitivity) / zoomLevel.value
      )
    ),
  };

  panStart.value = { x: clientX, y: clientY };
};

const endPan = () => {
  isPanning.value = false;
};

const downloadImage = async () => {
  if (!currentImage.value) return;

  try {
    const response = await fetch(currentImage.value.src);
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);

    const link = document.createElement("a");
    link.href = url;

    const fileName =
      currentImage.value.src.split("/").pop() || `弗洛洛图集_${Date.now()}.jpg`;
    link.download = fileName;

    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);

    window.URL.revokeObjectURL(url);

    showDownloadSuccess();
  } catch (error) {
    console.error("下载失败:", error);
    alert("下载失败，请稍后重试");
  }
};

const showDownloadSuccess = () => {
  const successEl = document.createElement("div");
  successEl.className = "download-success-toast";
  successEl.innerHTML = `
    <div class="toast-icon">✓</div>
    <span>图片已开始下载</span>
  `;

  document.body.appendChild(successEl);

  setTimeout(() => {
    successEl.classList.add("show");
  }, 10);

  setTimeout(() => {
    successEl.classList.remove("show");
    setTimeout(() => {
      if (successEl.parentNode) {
        document.body.removeChild(successEl);
      }
    }, 300);
  }, 3000);
};

function openLightbox(index: number) {
  if (showLikedOnly.value) {
    const displayedImg = likedImages.value[index];
    const originalIndex = images.value.findIndex(
      (img) => img.id === displayedImg.id
    );
    currentIndex.value = originalIndex;
  } else {
    currentIndex.value = index;
  }

  lightboxOpen.value = true;
  resetZoom();
  document.body.style.overflow = "hidden";
}

function closeLightbox() {
  lightboxOpen.value = false;
  resetZoom();
  document.body.style.overflow = "";
}

function prevImage() {
  currentIndex.value =
    (currentIndex.value + images.value.length - 1) % images.value.length;
}

function nextImage() {
  currentIndex.value = (currentIndex.value + 1) % images.value.length;
}

function onImageLoad(e: Event) {
  const img = e.target as HTMLImageElement;
  const card = img.closest(".florlo-card");
  card?.classList.add("loaded");
}

function handleImageError(e: Event) {
  const img = e.target as HTMLImageElement;
  img.style.opacity = "0";
  const card = img.closest(".florlo-card");
  if (card) {
    const skeleton = card.querySelector(".image-skeleton");
    if (skeleton) {
      skeleton.classList.add("error");
    }
  }
}

function handleLightboxImageError(e: Event) {
  const img = e.target as HTMLImageElement;
  img.style.opacity = "0.3";
}

const uploadModalOpen = ref(false);
const nickname = ref("");
const fileInput = ref<HTMLInputElement>();
const selectedFiles = ref<File[]>([]);

function getTodayKey() {
  return `uploaded_${new Date().toISOString().slice(0, 10)}`;
}
const uploadedToday = ref<number>(
  Number(localStorage.getItem(getTodayKey()) || 0)
);
const remaining = computed(() => Math.max(24 - uploadedToday.value, 0));

const canSubmit = computed(() => {
  return (
    nickname.value.trim().length > 0 &&
    selectedFiles.value.length > 0 &&
    selectedFiles.value.length <= remaining.value
  );
});

function clearOldUploadRecords() {
  const today = new Date();
  const storage = window.localStorage;
  for (const key of Object.keys(storage)) {
    if (!key.startsWith("uploaded_")) continue;

    const dateStr = key.slice("uploaded_".length);
    const recordDate = new Date(dateStr);
    if (isNaN(recordDate.getTime())) continue;

    const diffMs = today.getTime() - recordDate.getTime();
    const diffDays = diffMs / (1000 * 60 * 60 * 24);

    if (diffDays > 2) {
      storage.removeItem(key);
    }
  }
}

function openUploadModal() {
  clearOldUploadRecords();
  nickname.value = "";
  selectedFiles.value = [];
  if (fileInput.value) fileInput.value.value = "";
  uploadedToday.value = Number(localStorage.getItem(getTodayKey()) || 0);
  uploadModalOpen.value = true;
}

function closeUploadModal() {
  uploadModalOpen.value = false;
}

function handleFileSelect(e: Event) {
  const files = Array.from((e.target as HTMLInputElement).files || []);

  if (!files) return;

  const validFiles: File[] = [];
  for (const file of files) {
    if (file.size > 20 * 1024 * 1024) {
      alert(`文件太大：${file.name}，请控制在 20MB 内`);
      continue;
    }
    validFiles.push(file);
  }

  if (validFiles.length === 0) return;

  if (validFiles.length > remaining.value) {
    alert(
      `今天最多还能上传 ${remaining.value} 张，已为你截取前 ${remaining.value} 张`
    );
    selectedFiles.value = files.slice(0, remaining.value);
  } else {
    selectedFiles.value = files;
  }
}

const isUploading = ref(false);
async function submitUpload() {
  if (!canSubmit.value) return;
  isUploading.value = true;
  try {
    const res = await uploadImages(
      selectedFiles.value,
      nickname.value.trim(),
      "fll"
    );
    const uploadedCount = res.data.length;
    uploadedToday.value += uploadedCount;
    localStorage.setItem(getTodayKey(), String(uploadedToday.value));

    alert(`成功上传 ${uploadedCount} 张图片`);
    closeUploadModal();
  } catch (err: any) {
    console.error(err);
    alert(err.message || "上传失败");
  } finally {
    isUploading.value = false;
  }
}

interface Chibi {
  src: string;
  top: number;
  left: number;
}

const chibiList = ref<Chibi[]>([]);
let sentinelObserver: IntersectionObserver;

watch(showLikedOnly, (newVal) => {
  if (!newVal && !loading.value && !finished.value) {
    nextTick(() => {
      if (sentinel.value && sentinelObserver) {
        sentinelObserver.unobserve(sentinel.value);
        sentinelObserver.observe(sentinel.value);
      }
      if (images.value.length === 0) {
        loadNextPage();
      }
    });
  }
});

onMounted(async () => {
  await fetchRanking();
  await loadNextPage();

  sentinelObserver = new IntersectionObserver(
    (entries) => {
      if (entries[0].isIntersecting) debouncedLoad();
    },
    { rootMargin: "0px", threshold: 0.1 }
  );
  if (sentinel.value) {
    sentinelObserver.observe(sentinel.value);
  }

  const likedIdsArray = getLikedIds();
  if (likedIdsArray.length > 0) {
    setTimeout(() => {
      fetchLikedImages();
    }, 1000);
  }

  const total = 9;
  let pickCount = 3;
  const vw = window.innerWidth;
  const vh = window.innerHeight;
  const isMobile = window.innerWidth <= 768;
  const imgWidth = 100;
  const imgHeight = 100;

  function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  const nums = shuffle(Array.from({ length: total }, (_, k) => k + 1));
  if (isMobile) {
    pickCount = 1;
  }
  const picks = nums.slice(0, pickCount);

  chibiList.value = [];
  picks.forEach((i) => {
    chibiList.value.push({
      src: `/QImages/1 (${i}).png`,
      left: Math.random() * (vw - imgWidth),
      top: Math.random() * (vh - imgHeight),
    });
  });

  await nextTick();

  const imgs = document.querySelectorAll<HTMLImageElement>(".chibi-img");
  imgs.forEach((img, index) => {
    const padding = 200;

    gsap.fromTo(
      img,
      { opacity: 0, scale: 0.5 },
      {
        opacity: 1,
        scale: 1,
        duration: 0.8,
        ease: "back.out(2)",
        delay: 0.2 * index,
      }
    );

    img.addEventListener("mouseenter", () => {
      gsap.killTweensOf(img);

      gsap.to(img, {
        x: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
        y: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
        duration: 1.2,
        ease: "back.out(2)",
        onComplete: () => {
          animate(img);
        },
      });
    });

    const animate = (img: HTMLImageElement) => {
      let { x, y } = img.getBoundingClientRect();
      let deltaX = (Math.random() - 0.5) * 200;
      let deltaY = (Math.random() - 0.5) * 200;

      let nextX = x + deltaX;
      let nextY = y + deltaY;

      if (nextX < padding) deltaX = padding - x;
      if (nextX + img.width > window.innerWidth - padding)
        deltaX = window.innerWidth - padding - (x + img.width);
      if (nextY < padding) deltaY = padding - y;
      if (nextY + img.height > window.innerHeight - padding)
        deltaY = window.innerHeight - padding - (y + img.height);

      gsap.to(img, {
        x: `+=${deltaX.toFixed(0)}`,
        y: `+=${deltaY.toFixed(0)}`,
        rotation: `+=${((Math.random() - 0.5) * 60).toFixed(0)}`,
        duration: 2 + Math.random() * 2,
        ease: "power1.inOut",
        onComplete: () => animate(img),
      });
    };
    animate(img);
  });
});

onBeforeUnmount(() => {
  sentinelObserver.disconnect();
});
</script>

<style lang="scss" scoped>
/* 弗洛洛风格配色 */
$bg-deep: #06050a;
$deep-2: #120718;
$accent-1: #b86be0;
$accent-2: #ff6b85;
$accent-3: #9d4edd;
$text-main: #efe9ec;
$text-muted: rgba(239, 233, 236, 0.68);
$glass-bg: rgba(8, 6, 10, 0.68);
$glass-border: rgba(184, 107, 224, 0.12);
$shadow-deep: rgba(0, 0, 0, 0.75);
$inner-glow: rgba(184, 107, 224, 0.08);
$blood-halo: rgba(255, 107, 133, 0.1);

/* 全局容器 */
.florlo-gallery-container {
  min-height: 100vh;
  background: linear-gradient(180deg, $bg-deep 0%, $deep-2 100%);
  color: $text-main;
  font-family: "Noto Sans SC", system-ui, -apple-system, sans-serif;
  position: relative;
  padding: 20px;
  padding-top: 80px;
  overflow-x: hidden;
}

/* 背景效果 */
.gallery-background {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;

  .particle-field {
    position: absolute;
    inset: 0;
    background-image: radial-gradient(
        circle at 20% 30%,
        rgba($accent-1, 0.05) 0 2px,
        transparent 4px
      ),
      radial-gradient(
        circle at 80% 70%,
        rgba($accent-2, 0.04) 0 1px,
        transparent 3px
      ),
      radial-gradient(
        circle at 40% 80%,
        rgba($accent-1, 0.03) 0 1.5px,
        transparent 4px
      );
    background-size: 300px 300px, 200px 200px, 250px 250px;
    animation: particle-drift 60s linear infinite;
  }

  .musical-staff {
    position: absolute;
    inset: 0;
    background-image: repeating-linear-gradient(
      to bottom,
      rgba(255, 255, 255, 0.02) 0px,
      rgba(255, 255, 255, 0.02) 1px,
      transparent 1px,
      transparent 22px
    );
    opacity: 0.1;
    mix-blend-mode: overlay;
    animation: staff-scroll 40s linear infinite;
  }
}

/* 上传按钮 */
.florlo-upload-btn {
  position: fixed;
  bottom: 30px;
  left: 30px;
  z-index: 100;
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.9),
    rgba($accent-2, 0.7)
  );
  color: #0a0710;
  border: none;
  border-radius: 50px;
  padding: 12px 24px;
  font-size: 16px;
  font-weight: 700;
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  box-shadow: 0 8px 32px rgba($accent-2, 0.3);
  transition: all 0.3s cubic-bezier(0.2, 0.9, 0.3, 1);
  overflow: hidden;

  .btn-icon {
    font-size: 20px;
    animation: note-bounce 2s ease-in-out infinite;
  }

  .btn-glow {
    position: absolute;
    inset: 0;
    border-radius: 50px;
    background: linear-gradient(
      45deg,
      transparent,
      rgba(255, 255, 255, 0.2),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  &:hover {
    transform: translateY(-4px) scale(1.05);
    box-shadow: 0 12px 40px rgba($accent-2, 0.4);

    .btn-glow {
      opacity: 1;
    }
  }

  &:active {
    transform: translateY(-2px);
  }
}

/* 排序控制 */
.sort-controls {
  display: flex;

  align-items: center;
  margin-bottom: 30px;
  padding: 0 20px;
  position: relative;
  z-index: 2;
}

.florlo-sort-btn {
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  border-radius: 25px;
  padding: 12px 24px;
  font-size: 14px;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;

  .sort-icon {
    font-size: 16px;
  }

  .sort-wave {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 2px;
    background: linear-gradient(90deg, $accent-1, $accent-2);
    transform: translateX(-100%);
    transition: transform 0.3s ease;
  }

  &:hover {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.25),
      rgba($accent-2, 0.2)
    );
    border-color: $accent-1;
    transform: translateY(-2px);

    .sort-wave {
      transform: translateX(0);
    }
  }
}

/* 筛选控制 */
.filter-controls {
  display: flex;
  align-items: center;
  gap: 15px;
}

.florlo-filter-btn {
  position: relative;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  border-radius: 25px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  overflow: hidden;

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;

    &:hover {
      transform: none;
      background: linear-gradient(
        135deg,
        rgba($accent-1, 0.15),
        rgba($accent-2, 0.1)
      );
    }
  }

  .filter-heart {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 20px;
    height: 20px;

    svg {
      width: 16px;
      height: 16px;
      transition: all 0.3s ease;
    }
  }

  .filter-count {
    background: rgba($accent-2, 0.2);
    color: $accent-2;
    font-size: 12px;
    font-weight: 700;
    padding: 2px 6px;
    border-radius: 10px;
    margin-left: 4px;
  }

  .filter-glow {
    position: absolute;
    inset: 0;
    border-radius: 25px;
    background: linear-gradient(
      45deg,
      transparent,
      rgba($accent-2, 0.1),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  &:hover:not(:disabled) {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.25),
      rgba($accent-2, 0.2)
    );
    border-color: $accent-1;
    transform: translateY(-2px);

    .filter-glow {
      opacity: 1;
    }

    .filter-heart svg {
      animation: heart-beat 0.6s ease;
    }
  }

  &.active {
    background: linear-gradient(
      135deg,
      rgba(255, 107, 133, 0.2),
      rgba(184, 107, 224, 0.15)
    );
    border-color: rgba(255, 107, 133, 0.4);
    color: #ff6b85;

    .filter-heart svg {
      fill: #ff6b85;
      stroke: #ff6b85;
      filter: drop-shadow(0 0 8px rgba(255, 107, 133, 0.3));
    }
  }
}

.loading-spinner {
  width: 16px;
  height: 16px;
  border: 2px solid rgba($text-main, 0.3);
  border-top-color: $accent-2;
  border-radius: 50%;
  animation: spinner-rotate 0.6s linear infinite;

  &.large {
    width: 24px;
    height: 24px;
    border-width: 3px;
  }
}

.filter-info {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: rgba($accent-2, 0.1);
  border: 1px solid rgba($accent-2, 0.2);
  border-radius: 12px;
  animation: slide-in 0.3s ease;

  .info-icon {
    color: $accent-2;
    font-size: 14px;
  }

  .info-text {
    color: $text-main;
    font-size: 12px;
    font-weight: 500;
  }

  .loading-text {
    opacity: 0.7;
  }

  .clear-filter {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba($accent-2, 0.3);
    color: $accent-2;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.2s ease;

    svg {
      width: 12px;
      height: 12px;
    }

    &:hover {
      background: rgba($accent-2, 0.2);
      transform: scale(1.1);
    }
  }
}

/* 画廊网格 */
.florlo-gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 20px;
  padding: 0 20px;
  position: relative;
  z-index: 2;

  @media (max-width: 768px) {
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 15px;
    padding: 0 10px;
  }
}

/* 卡片样式 */
.florlo-card {
  position: relative;
  border-radius: 16px;
  overflow: hidden;
  aspect-ratio: 3/4;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);

  transform: translateY(20px);

  &:hover {
    transform: translateY(-8px) scale(1.02);

    .card-inner {
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4), 0 0 0 1px rgba($accent-1, 0.3);
    }

    .card-overlay {
      opacity: 1;
      transform: translateY(0);
    }

    .card-decoration .decoration-note {
      transform: translateY(-5px) rotate(15deg);
    }
  }

  &.liked-card {
    .card-frame::before {
      content: "";
      position: absolute;
      inset: -2px;
      border-radius: 16px;
      background: linear-gradient(45deg, $accent-2, $accent-1, $accent-2);
      z-index: -1;
      animation: border-glow 2s linear infinite;
    }
  }

  &.liked-highlight {
    animation: liked-pulse 1s ease;
  }

  &.new-liked {
    animation: new-liked 1.5s ease;
  }
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: inherit;
  overflow: hidden;
  background: linear-gradient(
    135deg,
    rgba(10, 8, 12, 0.8),
    rgba(8, 6, 10, 0.9)
  );
  border: 1px solid rgba($accent-1, 0.15);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
  transition: box-shadow 0.4s ease;
}

.card-frame {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: inherit;
  overflow: hidden;

  .image-skeleton {
    position: absolute;
    inset: 0;
    background: linear-gradient(
      90deg,
      rgba($accent-1, 0.1),
      rgba($accent-2, 0.05),
      rgba($accent-1, 0.1)
    );
    background-size: 200% 100%;
    animation: skeleton-loading 1.5s ease-in-out infinite;

    &.error {
      background: linear-gradient(
        135deg,
        rgba($accent-2, 0.1),
        rgba($accent-1, 0.05)
      );
    }
  }

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: all 0.6s ease;
    opacity: 0;

    .loaded & {
      opacity: 1;
    }
  }
}

.card-decoration {
  position: absolute;
  top: 15px;
  left: 15px;
  z-index: 2;

  .decoration-note {
    font-size: 18px;
    color: rgba($accent-2, 0.5);
    transition: all 0.4s ease;
  }

  .decoration-line {
    width: 30px;
    height: 1px;
    background: linear-gradient(90deg, rgba($accent-2, 0.5), transparent);
    margin-top: 5px;
  }
}

.card-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    to top,
    rgba($bg-deep, 0.9) 0%,
    rgba($bg-deep, 0.6) 50%,
    transparent 100%
  );
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding: 20px;
  opacity: 0;
  transform: translateY(10px);
  transition: all 0.4s ease;

  .overlay-content {
    display: flex;
    align-items: center;
    gap: 8px;

    .view-text {
      color: $accent-2;
      font-size: 14px;
      font-weight: 600;
      padding: 6px 12px;
      background: rgba($bg-deep, 0.7);
      border-radius: 20px;
      border: 1px solid rgba($accent-2, 0.3);
      backdrop-filter: blur(10px);
    }

    .overlay-note {
      color: rgba($accent-1, 0.5);
      font-size: 16px;
      animation: note-spin 4s linear infinite;
    }
  }
}

.florlo-like-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: rgba($bg-deep, 0.7);
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-2, 0.2);
  border-radius: 50%;
  width: 44px;
  height: 44px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 2;

  &:hover {
    background: rgba($bg-deep, 0.9);
    border-color: $accent-2;
    transform: scale(1.1);

    .heart {
      transform: scale(1.2);
    }
  }

  .heart-container {
    position: relative;
    width: 24px;
    height: 24px;
    display: flex;
    align-items: center;
    justify-content: center;

    .heart-icon {
      width: 20px;
      height: 20px;
      transition: all 0.3s ease;

      &.liked {
        animation: heart-beat 0.6s ease;
      }
    }

    .heart-glow {
      position: absolute;
      inset: -5px;
      border-radius: 50%;
      background: radial-gradient(
        circle,
        rgba(255, 107, 133, 0.4),
        transparent 70%
      );
      opacity: 0;
      transition: opacity 0.3s ease;

      .liked + & {
        opacity: 1;
      }
    }
  }

  .like-count {
    font-size: 12px;
    color: $text-main;
    font-weight: 600;
    margin-top: 2px;
    text-shadow: 0 1px 3px rgba(0, 0, 0, 0.5);
  }
}

/* 筛选空状态 */
.filter-empty {
  grid-column: 1 / -1;
  text-align: center;
  padding: 60px 20px;
  color: $accent-1;

  .empty-icon {
    font-size: 48px;
    margin-bottom: 20px;
    opacity: 0.5;
    animation: note-float 3s ease-in-out infinite;
  }

  h3 {
    margin: 0 0 10px 0;
    font-size: 20px;
    font-weight: 700;
  }

  p {
    margin: 0;
    opacity: 0.7;
    font-size: 14px;
  }
}

/* 加载状态 */
.loading-state,
.finished-state {
  grid-column: 1 / -1;
  text-align: center;
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
  color: $accent-1;

  span {
    font-size: 14px;
    font-weight: 600;
  }
}

.finished-state {
  .finished-icon {
    font-size: 32px;
    opacity: 0.7;
    animation: note-spin 4s linear infinite;
  }
}

.sentinel {
  height: 100px;
  width: 100%;
  position: relative;
  z-index: 1;
}

/* 排行榜面板 */
.florlo-ranking-panel {
  position: fixed;
  top: 100px;
  right: 30px;
  width: 280px;
  z-index: 90;
}

.panel-header {
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  border-radius: 16px;
  padding: 16px 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;

  &:hover {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.25),
      rgba($accent-2, 0.2)
    );
    border-color: $accent-1;
    transform: translateY(-2px);

    .panel-glow {
      opacity: 1;
    }
  }

  .panel-icon {
    font-size: 20px;
    color: $accent-2;
  }

  .ranking-title {
    margin: 0;
    font-size: 16px;
    font-weight: 700;
    color: $accent-2;
    flex: 1;
    text-align: center;
  }

  .ranking-count {
    font-size: 12px;
    color: rgba($text-main, 0.7);
    background: rgba(0, 0, 0, 0.3);
    padding: 4px 8px;
    border-radius: 10px;
    margin-right: 10px;
  }

  .toggle-icon {
    color: $accent-1;
    font-size: 14px;
    transition: transform 0.3s ease;
  }

  .panel-glow {
    position: absolute;
    inset: 0;
    border-radius: 16px;
    background: linear-gradient(
      45deg,
      transparent,
      rgba($accent-1, 0.1),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }
}

.ranking-list {
  margin-top: 10px;
  background: linear-gradient(
    135deg,
    rgba(10, 8, 12, 0.9),
    rgba(8, 6, 10, 0.95)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.2);
  border-radius: 16px;
  padding: 20px;
  max-height: 400px;
  overflow-y: auto;

  &::-webkit-scrollbar {
    width: 6px;
  }

  &::-webkit-scrollbar-track {
    background: rgba($text-main, 0.05);
    border-radius: 3px;
  }

  &::-webkit-scrollbar-thumb {
    background: rgba($accent-1, 0.3);
    border-radius: 3px;

    &:hover {
      background: rgba($accent-1, 0.5);
    }
  }
}

.ranking-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 16px;
  margin-bottom: 8px;
  background: rgba($text-main, 0.05);
  border-radius: 12px;
  border: 1px solid transparent;
  transition: all 0.3s ease;

  &:hover {
    background: rgba($text-main, 0.1);
    border-color: rgba($accent-1, 0.2);
    transform: translateX(5px);
  }

  &:last-child {
    margin-bottom: 0;
  }

  .rank-decoration {
    position: relative;

    .rank-number {
      width: 28px;
      height: 28px;
      display: flex;
      align-items: center;
      justify-content: center;
      background: rgba($text-main, 0.1);
      border-radius: 50%;
      font-size: 12px;
      font-weight: 700;
      color: $accent-1;
    }

    .rank-glow {
      position: absolute;
      inset: -3px;
      border-radius: 50%;
      background: radial-gradient(
        circle,
        rgba($accent-1, 0.3),
        transparent 70%
      );
      opacity: 0;
      transition: opacity 0.3s ease;

      .ranking-item:hover & {
        opacity: 1;
      }
    }
  }

  .rank-name {
    flex: 1;
    padding: 0 15px;
    font-size: 14px;
    color: $text-main;
    font-weight: 500;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .rank-count {
    font-size: 13px;
    color: $accent-2;
    font-weight: 600;
    white-space: nowrap;
  }

  &.rank-1 {
    background: linear-gradient(
      135deg,
      rgba($accent-2, 0.15),
      rgba($accent-1, 0.1)
    );
    border-color: rgba($accent-2, 0.3);

    .rank-number {
      background: $accent-2;
      color: #0a0710;
    }
  }

  &.rank-2 {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.15),
      rgba($accent-3, 0.1)
    );
    border-color: rgba($accent-1, 0.3);

    .rank-number {
      background: $accent-1;
      color: #0a0710;
    }
  }

  &.rank-3 {
    background: linear-gradient(
      135deg,
      rgba($accent-3, 0.15),
      rgba(157, 78, 221, 0.1)
    );
    border-color: rgba($accent-3, 0.3);

    .rank-number {
      background: $accent-3;
      color: $text-main;
    }
  }
}

/* Lightbox 样式 */
.florlo-lightbox {
  position: fixed;
  inset: 0;
  background: rgba($bg-deep, 0.98);
  backdrop-filter: blur(20px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  opacity: 0;
  animation: fade-in 0.3s ease forwards;
  overflow: hidden;
  user-select: none;
  -webkit-user-select: none;
}

.lightbox-controls {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 10;
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  pointer-events: none;

  > * {
    pointer-events: auto;
  }
}

.lightbox-close {
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.2),
    rgba($accent-2, 0.15)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;

  svg {
    width: 20px;
    height: 20px;
  }

  .close-glow {
    position: absolute;
    inset: 0;
    border-radius: 50%;
    background: linear-gradient(
      45deg,
      transparent,
      rgba($accent-2, 0.2),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  &:hover {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.3),
      rgba($accent-2, 0.25)
    );
    border-color: $accent-2;
    transform: scale(1.1) rotate(90deg);

    .close-glow {
      opacity: 1;
    }
  }
}

.lightbox-toolbar {
  display: flex;
  align-items: center;
  gap: 15px;
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  border-radius: 25px;
  padding: 12px 20px;

  .zoom-controls {
    display: flex;
    align-items: center;
    gap: 10px;

    .zoom-btn,
    .zoom-reset {
      background: rgba($text-main, 0.1);
      border: 1px solid rgba($accent-1, 0.2);
      color: $text-main;
      width: 36px;
      height: 36px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      transition: all 0.2s ease;

      svg {
        width: 18px;
        height: 18px;
      }

      &:hover:not(:disabled) {
        background: rgba($accent-1, 0.2);
        border-color: $accent-1;
        transform: scale(1.1);
      }

      &:disabled {
        opacity: 0.3;
        cursor: not-allowed;
      }
    }

    .zoom-reset {
      margin-left: 5px;
    }

    .zoom-level {
      color: $accent-2;
      font-size: 14px;
      font-weight: 600;
      min-width: 50px;
      text-align: center;
    }
  }

  .lightbox-like-btn {
    position: relative;
    background: rgba($text-main, 0.1);
    border: 1px solid rgba($accent-1, 0.2);
    color: $text-main;
    width: 44px;
    height: 44px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;

    .like-container {
      display: flex;
      flex-direction: column;
      align-items: center;

      svg {
        width: 20px;
        height: 20px;
        transition: all 0.3s ease;
      }

      .like-count {
        font-size: 10px;
        color: $text-main;
        font-weight: 600;
        margin-top: 2px;
      }
    }

    .like-pulse {
      position: absolute;
      inset: -5px;
      border-radius: 50%;
      border: 2px solid rgba(255, 71, 87, 0.3);
      animation: heart-pulse 1.5s ease-out infinite;
      opacity: 0;

      .liked & {
        opacity: 1;
      }
    }

    &:hover {
      background: rgba($accent-2, 0.2);
      border-color: $accent-2;
      transform: scale(1.1);

      svg {
        transform: scale(1.1);
      }
    }

    &.liked {
      color: #ff4757;
      border-color: rgba(255, 71, 87, 0.5);

      svg {
        fill: #ff4757;
        stroke: #ff4757;
        filter: drop-shadow(0 0 8px rgba(255, 71, 87, 0.5));
      }
    }
  }

  .lightbox-download-btn {
    position: relative;
    background: rgba($accent-1, 0.2);
    border: 1px solid rgba($accent-1, 0.3);
    color: $accent-1;
    width: 44px;
    height: 44px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;

    svg {
      width: 20px;
      height: 20px;
    }

    .download-glow {
      position: absolute;
      inset: 0;
      border-radius: 50%;
      background: linear-gradient(
        45deg,
        transparent,
        rgba($accent-1, 0.2),
        transparent
      );
      opacity: 0;
      transition: opacity 0.3s ease;
    }

    &:hover {
      background: rgba($accent-1, 0.3);
      border-color: $accent-1;
      transform: scale(1.1);
      color: $text-main;

      .download-glow {
        opacity: 1;
      }
    }
  }

  .nav-info {
    color: $text-main;
    font-size: 14px;
    font-weight: 500;
    padding-left: 15px;
    border-left: 1px solid rgba($text-main, 0.2);

    .current-index {
      color: $accent-2;
      font-weight: 700;
    }

    .separator {
      margin: 0 5px;
      opacity: 0.6;
    }

    .total-count {
      opacity: 0.8;
    }
  }
}

.lightbox-image-container {
  position: relative;
  max-width: 85vw;
  max-height: 85vh;
  transition: transform 0.2s ease-out;
  will-change: transform;

  img {
    max-width: 100%;
    max-height: 85vh;
    border-radius: 8px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5), 0 0 0 1px rgba($accent-1, 0.2);
    object-fit: contain;
    display: block;
  }

  .image-frame {
    position: absolute;
    inset: -10px;
    border: 2px solid rgba($accent-1, 0.2);
    border-radius: 12px;
    pointer-events: none;
  }
}

.lightbox-nav {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.2),
    rgba($accent-2, 0.15)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 10;
  position: relative;
  overflow: hidden;

  svg {
    width: 24px;
    height: 24px;
  }

  .nav-glow {
    position: absolute;
    inset: 0;
    border-radius: 50%;
    background: linear-gradient(
      45deg,
      transparent,
      rgba($accent-2, 0.2),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  &:hover {
    background: linear-gradient(
      135deg,
      rgba($accent-1, 0.3),
      rgba($accent-2, 0.25)
    );
    border-color: $accent-2;
    transform: translateY(-50%) scale(1.1);

    .nav-glow {
      opacity: 1;
    }
  }

  &.prev {
    left: 30px;
  }

  &.next {
    right: 30px;
  }
}

.pan-hint {
  position: absolute;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(10px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  padding: 12px 20px;
  border-radius: 25px;
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  animation: float-hint 3s ease-in-out infinite;

  .hint-icon {
    font-size: 18px;
    animation: arrow-move 2s ease-in-out infinite;
  }
}

.download-success-toast {
  position: fixed;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%) translateY(100px);
  background: linear-gradient(
    135deg,
    rgba($accent-1, 0.15),
    rgba($accent-2, 0.1)
  );
  backdrop-filter: blur(20px);
  border: 1px solid rgba($accent-1, 0.3);
  color: $accent-2;
  padding: 15px 25px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 14px;
  font-weight: 600;
  z-index: 3000;
  opacity: 0;
  transition: all 0.3s cubic-bezier(0.2, 0.8, 0.2, 1);
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);

  .toast-icon {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: $accent-2;
    color: #0a0710;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    font-weight: bold;
  }

  &.show {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

/* 上传模态框 */
.florlo-upload-modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba($bg-deep, 0.95);
  backdrop-filter: blur(20px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  opacity: 0;
  animation: fade-in 0.3s ease forwards;
}

.upload-modal {
  background: linear-gradient(
    135deg,
    rgba(10, 8, 12, 0.95),
    rgba(8, 6, 10, 0.98)
  );
  backdrop-filter: blur(20px);
  border: 1px solid rgba($accent-1, 0.3);
  border-radius: 20px;
  width: 90%;
  max-width: 500px;
  padding: 30px;
  color: $text-main;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.4);
}

.modal-header {
  text-align: center;
  margin-bottom: 25px;

  .header-icon {
    font-size: 32px;
    margin-bottom: 10px;
  }

  h3 {
    color: $accent-2;
    margin: 0;
    font-size: 24px;
    font-weight: 700;
  }

  .header-note {
    color: rgba($accent-1, 0.5);
    font-size: 20px;
    margin-top: 10px;
    animation: note-spin 4s linear infinite;
  }
}

.tip-container {
  background: rgba($text-main, 0.05);
  border: 1px solid rgba($accent-1, 0.1);
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 25px;
}

.tip-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 15px;

  .tip-icon {
    color: $accent-2;
    font-size: 20px;
  }

  h4 {
    color: $accent-2;
    margin: 0;
    font-size: 16px;
    font-weight: 600;
  }
}

.tips-list {
  list-style: none;
  padding: 0;
  margin: 0;

  li {
    position: relative;
    padding-left: 25px;
    margin-bottom: 12px;
    font-size: 14px;
    color: rgba($text-main, 0.9);
    line-height: 1.5;

    &:last-child {
      margin-bottom: 0;
    }

    .list-bullet {
      position: absolute;
      left: 0;
      color: $accent-2;
      font-size: 16px;
    }
  }
}

.upload-stats {
  display: flex;
  justify-content: center;
  gap: 30px;
  margin: 25px 0;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 5px;

  .stat-label {
    color: $text-main;
    font-size: 14px;
  }

  .stat-value {
    color: $accent-2;
    font-size: 18px;
    font-weight: 700;

    &.highlight {
      animation: stat-pulse 2s ease-in-out infinite;
    }
  }

  .stat-unit {
    color: rgba($text-main, 0.7);
    font-size: 12px;
  }
}

.modal-form {
  margin: 25px 0;
}

.form-label {
  display: block;
  margin-bottom: 20px;
  font-size: 14px;
  color: $text-main;

  .label-icon {
    display: inline-block;
    margin-right: 8px;
    color: $accent-1;
  }
}

.form-input {
  width: 100%;
  margin-top: 8px;
  padding: 12px 16px;
  background: rgba($text-main, 0.05);
  border: 1px solid rgba($accent-1, 0.2);
  border-radius: 8px;
  color: $text-main;
  font-size: 14px;
  transition: all 0.3s ease;
  position: relative;

  &:focus {
    outline: none;
    border-color: $accent-1;
    background: rgba($text-main, 0.1);

    + .input-glow {
      opacity: 1;
    }
  }

  &::placeholder {
    color: rgba($text-main, 0.5);
  }
}

.input-glow {
  position: absolute;
  inset: -2px;
  border-radius: 10px;
  background: linear-gradient(
    45deg,
    transparent,
    rgba($accent-1, 0.1),
    transparent
  );
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.file-input-wrapper {
  position: relative;
  margin-top: 8px;
}

.file-input {
  position: absolute;
  inset: 0;
  opacity: 0;
  cursor: pointer;
  z-index: 2;
}

.file-input-overlay {
  width: 100%;
  padding: 20px;
  background: rgba($text-main, 0.05);
  border: 2px dashed rgba($accent-1, 0.3);
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  transition: all 0.3s ease;

  .overlay-icon {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: rgba($accent-1, 0.1);
    color: $accent-1;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    font-weight: bold;
    transition: all 0.3s ease;
  }

  &:hover {
    background: rgba($text-main, 0.1);
    border-color: $accent-1;

    .overlay-icon {
      background: rgba($accent-1, 0.2);
      transform: scale(1.1);
    }
  }
}

.selected-files {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-top: 15px;
  padding: 10px;
  background: rgba($accent-1, 0.1);
  border-radius: 8px;

  .files-icon {
    color: $accent-1;
  }

  .files-count {
    color: $accent-2;
    font-size: 14px;
    font-weight: 600;
  }
}

.modal-actions {
  display: flex;
  gap: 15px;
  justify-content: center;
  margin-top: 30px;
}

.submit-btn {
  flex: 1;
  padding: 15px 25px;
  background: linear-gradient(135deg, $accent-1, $accent-2);
  border: none;
  border-radius: 12px;
  color: #0a0710;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;

    &:hover {
      transform: none;
      background: linear-gradient(135deg, $accent-1, $accent-2);
    }
  }

  .btn-spinner {
    width: 20px;
    height: 20px;
    border: 2px solid rgba(10, 7, 16, 0.3);
    border-top-color: #0a0710;
    border-radius: 50%;
    animation: spinner-rotate 0.6s linear infinite;
  }

  .btn-glow {
    position: absolute;
    inset: 0;
    border-radius: 12px;
    background: linear-gradient(
      45deg,
      transparent,
      rgba(255, 255, 255, 0.2),
      transparent
    );
    opacity: 0;
    transition: opacity 0.3s ease;
  }

  &:hover:not(:disabled) {
    transform: translateY(-3px);
    box-shadow: 0 8px 25px rgba($accent-2, 0.3);

    .btn-glow {
      opacity: 1;
    }
  }

  &:active:not(:disabled) {
    transform: translateY(-1px);
  }
}

.cancel-btn {
  padding: 15px 25px;
  background: transparent;
  border: 1px solid rgba($text-main, 0.3);
  border-radius: 12px;
  color: $text-main;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;

  &:hover {
    background: rgba($text-main, 0.1);
    border-color: $text-main;
    transform: translateY(-2px);
  }
}

/* 浮动角色 */
.floating-chibis {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 99;
}

.chibi-img {
  position: absolute;
  width: 80px;
  user-select: none;
  transform-origin: center center;
  pointer-events: auto;
  z-index: 10;
  filter: drop-shadow(0 4px 12px rgba(0, 0, 0, 0.3));
}

/* 动画关键帧 */
@keyframes particle-drift {
  0% {
    background-position: 0 0, 0 0, 0 0;
  }
  100% {
    background-position: 300px 300px, 200px 200px, 250px 250px;
  }
}

@keyframes staff-scroll {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(22px);
  }
}

@keyframes note-bounce {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
  }
  50% {
    transform: translateY(-5px) rotate(10deg);
  }
}

@keyframes spinner-rotate {
  to {
    transform: rotate(360deg);
  }
}

@keyframes heart-beat {
  0% {
    transform: scale(1);
  }
  25% {
    transform: scale(1.3);
  }
  50% {
    transform: scale(1.1);
  }
  75% {
    transform: scale(1.2);
  }
  100% {
    transform: scale(1);
  }
}

@keyframes pulse {
  0% {
    transform: scale(0.8);
    opacity: 0.6;
  }
  70% {
    transform: scale(1.2);
    opacity: 0;
  }
  100% {
    transform: scale(1.2);
    opacity: 0;
  }
}

@keyframes slide-in {
  from {
    opacity: 0;
    transform: translateX(-10px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes liked-pulse {
  0% {
    transform: translateY(0) scale(1);
  }
  50% {
    transform: translateY(-10px) scale(1.05);
  }
  100% {
    transform: translateY(0) scale(1);
  }
}

@keyframes border-glow {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
}

@keyframes new-liked {
  0% {
    transform: scale(1);
  }
  25% {
    transform: scale(1.1) rotate(5deg);
  }
  50% {
    transform: scale(1.05) rotate(-5deg);
  }
  75% {
    transform: scale(1.08) rotate(3deg);
  }
  100% {
    transform: scale(1) rotate(0deg);
  }
}

@keyframes note-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

@keyframes skeleton-loading {
  0% {
    background-position: 200% 0;
  }
  100% {
    background-position: -200% 0;
  }
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes heart-pulse {
  0% {
    transform: scale(0.95);
    opacity: 1;
  }
  70% {
    transform: scale(1.3);
    opacity: 0;
  }
  100% {
    transform: scale(1.3);
    opacity: 0;
  }
}

@keyframes float-hint {
  0%,
  100% {
    transform: translateX(-50%) translateY(0);
  }
  50% {
    transform: translateX(-50%) translateY(-10px);
  }
}

@keyframes arrow-move {
  0%,
  100% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(-5px);
  }
}

@keyframes stat-pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.7;
  }
}

@keyframes note-float {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.5;
  }
  50% {
    transform: translateY(-10px) rotate(180deg);
    opacity: 1;
  }
}

/* 过渡动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.panel-expand-enter-active,
.panel-expand-leave-active {
  transition: all 0.3s ease;
  max-height: 400px;
  overflow: hidden;
}

.panel-expand-enter-from,
.panel-expand-leave-to {
  opacity: 0;
  max-height: 0;
  transform: translateY(-10px);
}

/* 移动端适配 */
@media (max-width: 768px) {
  .florlo-gallery-container {
    padding: 10px;
    padding-top: 70px;
  }

  .florlo-upload-btn {
    bottom: 20px;
    left: 20px;
    padding: 10px 20px;
    font-size: 14px;
  }

  .sort-controls {
    flex-direction: column;
    gap: 15px;
    align-items: stretch;
    padding: 0 10px;
  }

  .florlo-gallery-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    padding: 0 10px;
  }

  .florlo-card {
    aspect-ratio: 2/3;
  }

  .florlo-ranking-panel {
    width: 240px;
  }

  .lightbox-controls {
    padding: 15px;
  }

  .lightbox-toolbar {
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
    max-width: 280px;
    margin: 0 auto;

    .nav-info {
      border-left: none;
      border-top: 1px solid rgba($text-main, 0.2);
      padding-left: 0;
      padding-top: 10px;
      width: 100%;
      text-align: center;
    }
  }

  .lightbox-nav {
    width: 48px;
    height: 48px;

    svg {
      width: 20px;
      height: 20px;
    }

    &.prev {
      left: 15px;
    }

    &.next {
      right: 15px;
    }
  }

  .upload-modal {
    width: 95%;
    padding: 20px;
    margin: 15px;
  }

  .upload-stats {
    flex-direction: column;
    gap: 15px;
  }

  .modal-actions {
    flex-direction: column;
  }

  .chibi-img {
    width: 60px;
  }
}

@media (max-width: 480px) {
  .florlo-gallery-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .lightbox-image-container {
    max-width: 95vw;
    max-height: 75vh;

    img {
      max-height: 75vh;
    }
  }
}
</style>