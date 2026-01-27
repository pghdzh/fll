<template>
  <div class="florlo-page" aria-live="polite">
    <main class="container" ref="mainContainer">
      <!-- 浮动音符装饰 -->
      <div class="floating-note" style="top: 10%; left: 5%">♪</div>
      <div
        class="floating-note"
        style="top: 30%; right: 8%; animation-delay: 0.5s"
      >
        ♫
      </div>
      <div
        class="floating-note"
        style="top: 70%; left: 7%; animation-delay: 1.2s"
      >
        ♬
      </div>
      <div
        class="floating-note"
        style="top: 85%; right: 5%; animation-delay: 0.8s"
      >
        ♩
      </div>

      <!-- HERO 区域 -->
      <header class="hero" role="banner">
        <div class="left-col">
          <button class="avatar-card">
            <picture class="avatar-wrap">
              <img
                src="@/assets/images1/slty (5).webp"
                class="avatar"
                loading="lazy"
                decoding="async"
                alt="弗洛洛角色立绘"
              />
            </picture>
            <div class="avatar-meta">
              <span class="avatar-badge">共鸣能力：重世的交响乐</span>
              <span class="avatar-badge">我或曾梦见，与你亲密无间。</span>
            </div>
          </button>
        </div>
        <div class="right-col" ref="metaCard">
          <div class="hero-panel">
            <h1 class="name">弗洛洛 <span class="jp">（残星会会监）</span></h1>
            <p class="subtitle">于失亡彼岸奏响命运乐章的音律咏叹者</p>
            <p class="lead">
              神秘而危险的指挥家，在生死之间谱写灵魂乐章。
              她沉默地挥舞指挥棒，以音律构筑理想世界，却深陷绝望与等待的循环。
            </p>
            <div class="hero-tags">
              <span class="tag">音感仪</span>
              <span class="tag">湮灭属性</span>
              <span class="tag">残星会</span>
            </div>
          </div>
        </div>
      </header>

      <!-- 角色背景与形象 -->
      <section id="overview" class="card overview">
        <div class="card-head">
          <h2><i class="icon icon-profile"></i> 角色背景与形象</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <h3>背景故事</h3>
            <p>
              残星会会监弗洛洛，安静、忧郁，似乎靠近她就会坠入无尽的忧伤之中。
              为复活逝去的村民，她创造了"失亡彼岸"，一个充满彼岸花与薰衣草的永恒空间。
            </p>
            <p>
              在生死之间，她谱写了一篇又一篇曲谱，不断构筑着她心中完美的世界，
              却始终被困在绝望与希望、等待与放弃的矛盾中。
            </p>
          </div>
          <div class="text-block">
            <h3>外貌／着装</h3>
            <p>
              弗洛洛以白发红瞳的造型登场，手持音感仪作为武器，身着指挥家服饰。
              形象设计偏向悲情美少女风格，带有疏离感与危险气质。
            </p>
            <p>
              她的服装以暗紫色为主色调，饰有彼岸花纹样，
              象征着生死别离与她对逝去之人的永恒思念。
            </p>
          </div>
        </div>
      </section>

      <!-- 共鸣状况 -->
      <section id="resonance-status" class="card overview">
        <div class="card-head">
          <h2><i class="icon icon-resonance"></i> 共鸣状况</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <h3>频谱检验报告</h3>
            <p>
              鉴定依据：【频谱检验报告RA-F2005-G】<br />
              对象在死亡时引发共鸣觉醒，拥有在理解事物本质频率结构的基础上可以调律万物频率、改变其“旋律”的能力。调律能力强弱与身体状态正相关。最新测试数据显示，对象在极度超频并保持清醒的状态下调律可控范围可最大扩大至30倍，最长持续时间46秒。对象共鸣时间距今过于久远，已无法追溯明确时间与共鸣源。<br />
              对象声痕位于左臂，共鸣后半残象化的身体不再衰老，除左眼瞳色发生改变外，无其他特殊体征变化。测试结果显示，对象身体处于健康状态时，皮肤毁坏后进行频率更新与重组的时间约为6秒，器官毁坏后恢复时间为15-18分钟，至今未找到致死弱点。<br />
              检测发现，对象体内深处隐藏着大量极度混乱的频率，数量逾千，具体数目至今无法确定，已经过实验确认，这些频率对其身体、共鸣能力发挥没有任何影响，无任何利用价值，仅与对象的精神波动相关。<br />
              受试样本拉贝尔曲线前段平直无波动，后段拉贝尔曲线陡升，此后拉贝尔曲线波动趋于稳定，检测结果判断为突变型共鸣者。已经过多次实验确认，其觉醒方式无法通过人工激化复现在他人身上。<br />
            </p>
            <h3>超频诊断报告</h3>
            <p>
              受试样本波形检测呈现锯齿态与针尖态的交替出现，时域表示剧烈且无序，频率极高，检测中时域表示有失真情况，峰值暂无可推定范围。<br />
              诊断结果：超频临界值极低，稳定性极低，超频状态频繁出现。<br />
              其超频一直处于可控状态，可利用这种精神稳定性，探寻稳定延长普通人超频状态的方法。
            </p>
          </div>
        </div>
      </section>

      <!-- 四大主题区域 -->
      <section id="themes" class="card themes-section">
        <div class="card-head">
          <h2><i class="icon icon-themes"></i> 弗洛洛的珍贵之物</h2>
          <p class="section-subtitle">
            这些物品承载着她的记忆、执念与未完成的愿望
          </p>
        </div>

        <div class="themes-grid">
          <div class="theme-card">
            <div class="theme-img">
              <img
                src="./img/item1.png"
                loading="lazy"
                decoding="async"
                alt="自撰的乐谱"
              />
            </div>
            <div class="theme-content">
              <h3>自撰的乐谱</h3>
              <p>
                她面色平静，双手之间却不停对一首首乐曲进行激越的变奏与夸张。
              </p>
              <p>
                摇摇欲坠的理性已被汹涌的情感彻底吞噬，一段段音符在她手中被撕得粉碎，又重新揉捏成凄惨诡异的模样。
              </p>
            </div>
          </div>

          <div class="theme-card">
            <div class="theme-img">
              <img
                src="./img/item2.png"
                loading="lazy"
                decoding="async"
                alt="遗留的提琴"
              />
            </div>
            <div class="theme-content">
              <h3>遗留的提琴</h3>
              <p>她亲手接过了大家的礼物。她亲手埋葬了大家的骸骨。</p>
              <p>
                琴弦振动发出的是一声又一声的欢唱。她持弓走近的是一次又一次的死亡。
              </p>
            </div>
          </div>

          <div class="theme-card">
            <div class="theme-img">
              <img
                src="./img/item3.png"
                loading="lazy"
                decoding="async"
                alt="未送出的门票"
              />
            </div>
            <div class="theme-content">
              <h3>未送出的门票</h3>
              <p>
                当她再一次拿着门票在音乐厅门口等候，她开始不知道自己等待的到底是什么。
              </p>
              <p>
                直到等到听众全部进场，门外已无他人，这张票仍然在她手中。她不会再等了。
              </p>
            </div>
          </div>

          <div class="theme-card">
            <div class="theme-img">
              <img
                src="./img/food.png"
                loading="lazy"
                decoding="async"
                alt="红醋栗果饼"
              />
            </div>
            <div class="theme-content">
              <h3>红醋栗果饼</h3>
              <p>
                极致的酸涩与殷红浓稠的果酱一同淌下，缓缓浸入蜜饼间的每一处缝隙。
              </p>
              <p>
                除了弗洛洛，没人能接受这种古怪的味蕾刺激，也没人知道这种小巧酸硬的果子到底从何而来。
              </p>
            </div>
          </div>
        </div>
      </section>

      <!-- 剧情时间线 -->
      <section id="story-timeline" class="card timeline-section">
        <div class="card-head">
          <h2><i class="icon icon-timeline"></i> 剧情时间线</h2>
          <p class="section-subtitle">弗洛洛在黎那汐塔篇章中的完整轨迹</p>
        </div>

        <div class="story-timeline">
          <div class="timeline-track"></div>

          <div
            class="timeline-item"
            v-for="(event, index) in storyTimeline"
            :key="index"
          >
            <div class="timeline-marker">
              <div class="marker-dot"></div>
            </div>
            <div class="timeline-content">
              <h3>{{ event.title }}</h3>
              <p>{{ event.description }}</p>
              <div class="timeline-tags">
                <span
                  class="timeline-tag"
                  v-for="tag in event.tags"
                  :key="tag"
                  >{{ tag }}</span
                >
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 人物彩蛋 -->
      <section id="easter-eggs" class="card easter-eggs-section">
        <div class="card-head">
          <h2><i class="icon icon-easter-egg"></i> 人物彩蛋与细节</h2>
          <p class="section-subtitle">隐藏在失亡彼岸中的秘密</p>
        </div>

        <div class="eggs-grid">
          <div class="egg-card flower-egg">
            <div class="egg-icon">🌸</div>
            <h3>彼岸花与薰衣草</h3>
            <p>彼岸花的花语是<strong>"生死别离与绝望的爱"</strong> 。</p>
            <p>
              失亡彼岸布满了薰衣草，而薰衣草的花语是：<strong>等你爱我</strong>
            </p>
          </div>

          <div class="egg-card voice-egg">
            <div class="egg-icon">🎵</div>
            <h3>力竭语音解析</h3>
            <ul>
              <li>"在靠近些，也好……</li>
              <li>"我回来了……"</li>
              <li>"这里并非终章……"</li>
            </ul>
            <p class="egg-note">
              剧情弗洛洛最后跟漂子说的话“这就是你我的终章。”（那么弗洛洛落水前心里的期待是？）
            </p>
          </div>

          <div class="egg-card achievement-egg">
            <div class="egg-icon">🏆</div>
            <h3>隐藏成就</h3>
            <div class="achievement-item">
              <h4>II aurait suffi (本该知足)</h4>
              <p>首次进入失亡彼岸时获得</p>
            </div>
            <div class="achievement-item">
              <h4>‌此岸彼岸的回声‌</h4>
              <p>进入失亡彼岸后，在小屋门口与弗洛洛对话</p>
            </div>
            <div class="achievement-item">
              <h4>‌来自过去的乐曲</h4>
              <p>与弗洛洛对话后，进入屋内与唱片交互</p>
            </div>
          </div>
          <div class="egg-card achievement-egg">
            <div class="egg-icon">🏆</div>
            <h3>隐藏成就</h3>
            <div class="achievement-item">
              <h4>‌寥远的歌声</h4>
              <p>前往山上找到特莉丝并与其交互即可</p>
            </div>
            <div class="achievement-item">
              <h4>水中捞月</h4>
              <p>从小屋出来后，向前找到一口井，对水中的小提琴进行三次交互</p>
              <div class="achievement-img-placeholder">
                <div class="img-placeholder">
                  <img
                    src="/img/QQ20260127-125649.webp"
                    alt="水中捞月成就截图"
                    @click="
                      openImageModal(
                        '/img/QQ20260127-125649.webp',
                        '水中捞月',
                        '在失亡彼岸的水井中尝试捞取弗洛洛的小提琴'
                      )
                    "
                  />
                </div>
              </div>
            </div>
          </div>
          <div class="egg-card location-egg">
            <div class="egg-icon">📍</div>
            <h3>弗洛洛常驻点位</h3>
            <p>点击查看大图</p>
            <div class="time-grid">
              <div class="time-slot">
                <span class="time">清晨6点</span>
                <span class="time-desc">向邻居问好</span>
              </div>
              <div class="time-slot">
                <span class="time">早上8点</span>
                <span class="time-desc">拜访亲朋好友</span>
              </div>
              <div class="time-slot">
                <span class="time">中午12点</span>
                <span class="time-desc">在家听歌</span>
              </div>
              <div class="time-slot">
                <span class="time">下午2点</span>
                <span class="time-desc">练习指挥</span>
              </div>

              <div class="access-img-placeholder">
                <div class="img-placeholder">
                  <img
                    src="/img/6点.webp"
                    alt="清晨6点弗洛洛位置"
                    @click="
                      openImageModal(
                        '/img/6点.webp',
                        '清晨6点 - 向邻居问好',
                        '弗洛洛在清晨6点的常驻点位'
                      )
                    "
                  />
                </div>
              </div>
              <div class="access-img-placeholder">
                <div class="img-placeholder">
                  <img
                    src="/img/8点.webp"
                    alt="早上8点弗洛洛位置"
                    @click="
                      openImageModal(
                        '/img/8点.webp',
                        '早上8点 - 拜访亲朋好友',
                        '弗洛洛在早上8点的常驻点位'
                      )
                    "
                  />
                </div>
              </div>
              <div class="access-img-placeholder">
                <div class="img-placeholder">
                  <img
                    src="/img/12点.webp"
                    alt="中午12点弗洛洛位置"
                    @click="
                      openImageModal(
                        '/img/12点.webp',
                        '中午12点 - 在家听歌',
                        '弗洛洛在中午12点的常驻点位'
                      )
                    "
                  />
                </div>
              </div>
              <div class="access-img-placeholder">
                <div class="img-placeholder">
                  <img
                    src="/img/14点.webp"
                    alt="下午2点弗洛洛位置"
                    @click="
                      openImageModal(
                        '/img/14点.webp',
                        '下午2点 - 练习指挥',
                        '弗洛洛在下午2点的常驻点位'
                      )
                    "
                  />
                </div>
              </div>
            </div>
          </div>

          <div class="egg-card access-egg">
            <div class="egg-icon">🚪</div>
            <h3>失亡彼岸进入方法</h3>
            <div class="access-method">
              <h4>1. 材料追踪</h4>
              <p>使用特殊材料"来生追踪"进入</p>
            </div>
            <div class="access-img-placeholder">
              <div class="img-placeholder">
                <img
                  src="/img/egg1.webp"
                  alt="来生追踪材料位置"
                  @click="
                    openImageModal(
                      '/img/egg1.webp',
                      '来生追踪材料',
                      '使用特殊材料进入失亡彼岸的方法'
                    )
                  "
                />
              </div>
            </div>
            <div class="access-method">
              <h4>2. 地图位置</h4>
              <p>Boss芬莱克右下角</p>
            </div>
            <div class="access-img-placeholder">
              <div class="img-placeholder">
                <img
                  src="/img/egg2.webp"
                  alt="Boss芬莱克右下角位置"
                  @click="
                    openImageModal(
                      '/img/egg2.webp',
                      'Boss芬莱克右下角',
                      '在地图中找到失亡彼岸入口的位置'
                    )
                  "
                />
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 共鸣链详解 -->
      <section id="resonance-chain" class="card resonance-chain-section">
        <div class="card-head">
          <h2><i class="icon icon-chain"></i> 共鸣链详解</h2>
          <p class="section-subtitle">弗洛洛的六层共鸣能力解析</p>
        </div>

        <div class="chain-container">
          <div class="chain-track"></div>

          <div
            class="chain-item"
            v-for="(chain, index) in resonanceChains"
            :key="index"
          >
            <div class="chain-level">{{ index + 1 }}</div>
            <div class="chain-content">
              <h3>{{ chain.title }}</h3>
              <div class="chain-desc">{{ chain.description }}</div>
              <div class="chain-effects">
                <div
                  class="effect"
                  v-for="(effect, i) in chain.effects"
                  :key="i"
                >
                  <span class="effect-icon">✦</span>
                  <span>{{ effect }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- 弗洛洛纪事 -->
      <section class="ll-stories" ref="storyRef">
        <div class="card" role="region" aria-labelledby="stories-title">
          <header class="card-head">
            <h2 id="stories-title" class="card-title">
              <i class="icon icon-stories"></i> 弗洛洛纪事
            </h2>
            <p class="section-subtitle">深入探索她的内心世界与过往</p>
          </header>

          <main class="card-body">
            <transition name="fade-slide" mode="out-in">
              <article
                :key="stories[currentStory].id"
                :id="`panel-${stories[currentStory].id}`"
                class="story"
                role="tabpanel"
                :aria-labelledby="`tab-${stories[currentStory].id}`"
              >
                <h3 class="story-head">{{ stories[currentStory].title }}</h3>
                <div class="story-content">
                  {{ stories[currentStory].content }}
                </div>
              </article>
            </transition>
          </main>

          <footer class="card-foot">
            <button class="nav-btn" @click="prevStory" aria-label="上一则故事">
              ‹
            </button>
            <div class="story-pager">
              <span class="story-title">{{ stories[currentStory].title }}</span>
              <span class="story-counter"
                >{{ currentStory + 1 }} / {{ stories.length }}</span
              >
            </div>
            <button class="nav-btn" @click="nextStory" aria-label="下一则故事">
              ›
            </button>
          </footer>
        </div>
      </section>
      <!-- 页脚部分 -->
      <footer class="page-footer">
        <!-- 装饰性分隔线 -->
        <div class="footer-divider">
          <div class="music-note">♪</div>
          <div class="divider-line"></div>
          <div class="music-note">♫</div>
        </div>

        <!-- 页脚主要内容 -->
        <div class="footer-content">
          <!-- 主题标志 -->
          <div class="footer-theme">
            <div class="theme-icon">🎵</div>
            <p class="theme-text">「于失亡彼岸奏响终章之乐」</p>
          </div>
        </div>

        <!-- 底部装饰 -->
        <div class="footer-bottom">
          <div class="bottom-flower">🌸</div>
          <div class="bottom-text">彼岸之花永不凋零</div>
          <div class="bottom-flower">🌸</div>
        </div>
      </footer>
      <!-- 图片弹窗 -->
      <transition name="fade">
        <div
          v-if="showImageModal"
          class="image-modal"
          @click="handleModalBackdropClick"
        >
          <div class="modal-content">
            <!-- 关闭按钮 -->
            <button
              class="modal-close"
              @click="closeImageModal"
              aria-label="关闭弹窗"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
              </svg>
            </button>

            <!-- 图片信息 -->
            <div class="image-info" v-if="currentImage.title">
              <h3 class="image-title">{{ currentImage.title }}</h3>
              <p class="image-desc" v-if="currentImage.description">
                {{ currentImage.description }}
              </p>
            </div>

            <!-- 图片容器 -->
            <div class="image-container">
              <img
                :src="currentImage.url"
                :alt="currentImage.title || '查看大图'"
                loading="lazy"
              />
            </div>
          </div>
        </div>
      </transition>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

// 剧情时间线数据
const storyTimeline = ref([
  {
    title: "早年与变故",
    description:
      "弗洛洛的出生和早期经历未知。她曾与一群村民（可能被视为‘家人’）共同生活，但一场灾难导致他们全部丧生，她是唯一的幸存者。这段经历是她内心创伤与执念的根源。",
    tags: ["过去", "悲剧起源"],
  },
  {
    title: "共鸣觉醒",
    description:
      "在死亡边缘，弗洛洛引发了独特的“突变型”共鸣觉醒，获得了调律万物频率、改变其“旋律”的能力。觉醒后身体停止衰老，并留下了永久的声痕。",
    tags: ["觉醒", "能力获得"],
  },
  {
    title: "孤独的漫游与音乐生涯",
    description:
      "拥有漫长生命的她，将孤独与悲怆投入音乐，成为一位能操纵听众情绪的指挥家。她曾在音乐厅外长久等待一位“知音”，但最终彻底失望，不再等待。",
    tags: ["漫游", "音乐", "孤独"],
  },
  {
    title: "加入残星会",
    description:
      "为了寻找复活逝者的方法或达成某个深层目的，她加入了残星会并成为会监。但她对组织事务表现得异常疏离，仿佛只是一个过客，唯独关注与人类进化相关的研究。",
    tags: ["残星会", "会监", "疏离"],
  },
  {
    title: `创造「失亡彼岸」`,
    description:
      "这是她计划的核心。她利用自身能力，为了复活逝去的村民而创造了一个独立的幻想空间「失亡彼岸」，其中布满了象征“绝望的爱”的彼岸花和象征“等待”的薰衣草。",
    tags: ["核心计划", "失亡彼岸", "执念"],
  },
  {
    title: `与漂泊者相遇及「终章」`,
    description:
      "在2.5版本剧情中，她的计划因漂泊者的介入而达到高潮。最终，她可能选择与「失亡彼岸」融合，并对漂泊者说出“这就是你我的终章”，其内心的期待或许是希望有人能理解或终结她漫长的孤独与执念。",
    tags: ["漂泊者", "结局", "融合"],
  },
]);

// 共鸣链数据
const resonanceChains = ref([
  {
    title: "钥匙，通往冥界的奥秘",
    description: "开启通往彼岸的大门，掌握生死之间的音律奥秘。",
    effects: [
      "亡与死的乐章伤害倍率提升80%",
      "永不消逝的梦呓伤害倍率提升80%",
      "弗洛洛不处于指挥状态且不处于战斗状态4秒后，若【乐声】少于2枚，获得【乐声·彩乐】，直至【乐声】不少于2枚。",
    ],
  },
  {
    title: "绳索，重生更新的纽带",
    description: "连接过去与未来的音律纽带，让逝去的旋律得以重生。",
    effects: [
      "谱曲终末伤害倍率提升75%",
      "余响对谱曲终末的倍率增加效果提升75%",
      "施放谱曲终末时可获得14层余响",
    ],
  },
  {
    title: "匕首，消弭妄想的力量",
    description: "以音律为刃，斩断不切实际的妄想，直面残酷的现实。",
    effects: [
      "声骸技能伤害加深80%",
      "谱曲终末期间所有【乐声】转化为【乐声·彩乐】",
      "被强化攻击·彩乐·赫卡忒命中的目标，攻击降低20%，持续15秒。",
    ],
  },
  {
    title: "火炬，新径启行的引导",
    description: "在黑暗中点亮前行的道路，引导迷失的灵魂找到方向。",
    effects: [
      "施放声骸技能时，队伍全属性伤害加成提升20%",
      "增益效果持续30秒",
      "增强团队协同作战能力",
    ],
  },
  {
    title: "岔路，穿越生命的要地",
    description: "站在生死岔路口，以音律停滞时间，做出命运的选择。",
    effects: [
      "进入指挥状态时，生成领域停滞周围的目标，领域持续4秒。结束指挥状态或切换至其他角色时，将提前清除停滞效果。",
      "处于指挥状态时受到伤害降低30%",
      "提供强大的控场与生存能力",
    ],
  },
  {
    title: "深夜，走出安息与终结",
    description: "穿越漫长的黑夜，在终结之处寻找新的开始。",
    effects: [
      "强化攻击·赫卡忒伤害倍率提升24%",
      "施放亡与死的乐章、永不消逝的梦呓期间，将召唤赫卡忒施放1次重世幻象·赫卡忒，造成弗洛洛攻击216.42%的湮灭伤害，命中目标时获得8层余响。本次伤害为声骸技能伤害。",
      "处于指挥状态时，若弗洛洛不为队伍中登场角色，目标受到赫卡忒和弗洛洛的伤害提升40%。",
      "处于指挥状态时，若弗洛洛为队伍中登场角色，湮灭伤害加成提升60%。",
    ],
  },
]);

// 故事数据
const stories = ref([
  {
    id: "1",
    title: "红与黑的歌",
    content: `听众们总是异常投入，或是僵在原地，或是控制不住地颤栗摇摆，黑暗中的一双双眼睛死死盯着那纤弱又有力的背影，目光因随着双手起落而晕眩，如同纷飞的音符凌乱于大段大段即兴的激昂挽歌中。

沸腾的急板旋律点燃了心火，让人们压抑在心底的情绪肆意疯长，无法掌控、无法自拔，直至被推搡着和她一起窒息于乐曲高潮时极致狂想的曲调，在不知不觉间走到精神世界的边缘，站在悬崖边望着深不见底的深渊，迫不及待地在极端兴奋中，感受毁灭与新生的绽放。

在那一步凌空的脚步迈下前，聚光灯下的指挥家稳稳定住了自己的双臂，残忍地让乐曲戛然而止。

她站在那里，一动不动宛如雕像，胸膛却仍不停剧烈起伏，独自在情绪中徜徉，刚才将她拉回来的那一丝理智已经崩断，逸散在了浓郁的情感中。她只望着天顶，从不会望向台下的任何人。

这首曲子就这样终结？

人们沉默，惊讶，徘徊，最后失神离开。可还没走出演奏厅的大门，耳边就已重新听到了刚才的乐音，从过去传来的乐音。那些乐曲毫不费力的刻进了每一个人的脑海里，一遍又一遍的重复，比当初亲耳听到的还要鼓动人心，不断唤醒着、放大着每个人心中极端的疯狂。

眼前一片光明的时代，人们大笑着走出门去，鲜红的歌让他们迫切地拥住美好的明天；眼前一片黑暗的时代，人们承受不住这最后一根稻草，漆黑的歌让他们果断地走向最高处的窗前。

她似乎从不在乎自己的音乐会为他人染上何种颜色，自顾自的在时代轮替中不停舞动双手。坐在演奏厅中的人也不多想自己将在音乐中走向何方。无人细究过，创造出这些温暖与悲伤乐曲的人一次次站到台上，究竟只是无所求的创造或是发泄，还是为了埋于尘土中的爱与过往。`,
  },
  {
    id: "2",
    title: "隐没的花簇",
    content: `猩红的厅殿中常常空无一人，只有一面面象征着会监的旗帜在冷风中飘扬。那面彼岸花的旗帜颜色早已暗沉，但它永远都悬挂在边缘，不曾掉落。残星会人的眼中，会监们皆是来无影去无踪的存在，没有几个人会在这里多做停留，可在那些抓不住的身影中，她是隐没最深的人。除了那面旗帜，这里没有任何属于她的痕迹。她放弃了会监在这里可以拥有的一切，她的房间中空无一物，她不会在这里多说半句……她不归属于这里。疏离的沉默让人们忽视，当人们觉得她对于残星会、残星会对于她都不过可有可无时，沉寂的彼岸花会突然从黑暗的世界中出现，凌厉地绽放，将花瓣精准刺穿敌人的胸膛，完成属于她的任务，然后重新消失在众人视野中。她似乎对这里的一切都没兴趣，也很少有人知道她加入到底是为了什么。

残星会中只有一个地方，能够牵绊住她的脚步。她偶尔站在实验室上层，沉默俯视着人类与残象肢体耦合技术的发展、超频技术的不断进步，看着那些主动寻求进化的人们眼神中的渴望，看着他们艰难地走向全新的人生，成功的尖叫，失败的哀嚎，仿佛看到的是更多的人。

她有时会掌一盏灯，如同幽灵般在深夜中独自一人出现在这里，静静翻看最新的研究进展，却从来没有带过一页资料离开，似乎每一次翻阅都以失望告终。偶尔有人看到这一幕也不明白，她到底想寻找到什么。`,
  },
  {
    id: "3",
    title: "前行于困顿",
    content: `当生命被无限延长，到底应该在世界上走出一条什么样的道路？她不知道。

前所未有的空旷与悠远让她感到无所适从，于是她选择闭上双眼，将孤独、悲怆、不甘发泄在音乐中，在任何地方、任何时间不顾一切的挥洒音符。

癫狂的人变得声名显赫，却一如既往的独断专行。围绕着她的亲和的、厌恶的言论比她消失得更早，重复的历史、重复的言论不断上演，她不可避免的一次次重新变得孤单，直到后来她才明白，她想寻找到依靠，可真情实感不过徒劳。短暂的美好消逝时，只有无法离开的人继续承受痛苦。她渐渐恢复了理智，直到彻底封闭内心，最终偏离为麻木，脱离所有人踽踽独行，不再看向台下，不再看向世界。

她不更换名称去遮掩，不管人们对她的猜测与探查。任何人都看得出她的年龄，但没有几个人能猜透她心灵的年龄。

她本会如无意义的幽灵般永远游荡下去，可一场音乐会让她找到了自己新的人生目标。难熬的时间在她想通的那一瞬间飞速流逝，宽广的生命蓝图开始收缩。她不顾日夜的汲取不曾了解的一切，寻找时间长河中一闪而过的天才。如果人们认为她才是最大的希望，那她便会大方满足他们研究的愿望。她在不同时代中让自己一次又一次躺在不同的试验器械之下，目睹自己分解，看着自己重生。疼痛让她感受到自己仍困于现实，却也只能在疼痛的缝隙间靠近过去。无论什么样的尝试都没关系，因为疼痛终归会被遗忘，可为什么，她渐渐觉得自己身陷囹圄，曾经漫无边际的路越来越窄……这不是唯一的道路吗？她在狭窄的缝隙中仍不死心的向前探出手指，企图在困顿中找到出路。`,
  },
  {
    id: "4",
    title: "完美的裂隙",
    content: `古老的雕像因拥有独特的材质、精细的制作工艺而显得尤为珍贵和难以获取。

越被珍惜的宝物，越容易走向毁灭，看似意外，又总觉得是拥有者带有自毁倾向的命中注定。

这座雕像从高高的柜子上意外摔下，摔得粉碎，涣散破裂的零件洒了一地。它被创造出的时代早已远去，如今除了她，没有人记得它原本的样貌。她想要把它拼凑回原本无瑕的模样，于是想起来时，就会空出些时间，小心翼翼地粘合每一片碎片。她从内部开始重组，按照回忆中的形象将几万个碎片复归原位，专心拼合的时光让那颗空落落的心变得平静、充实，连带着身边的一切都陷入到美妙和谐的氛围中。当她慢下来时，一切却又残忍地变得更快起来，不知不觉间，她已将这座雕像恢复了大半，它已经可以同往常一样运作、活动，可这还不够，美丽的皮囊也是不可或缺的一部分。她的捏造，她行动的结果，必须遵从她的意愿，达到从内到外的完美。

胜利近在眼前，又仿佛遥不可及。每当她拼好一处，另一处就再一次因为年久脆弱而剥落下来。

裂隙永远存在。

她不停地拼着，一片掉下来就再次拿上去，心里越焦急，越想要靠近最终的结果，零落的碎片就变得更多。

她凝视雕像太久，久到雕像面容上的裂隙开始变成无数个嘲讽的笑容，笑她想要追求完美，实际理想与现实之间的裂隙永远存在，她面对的其实是没有尽头的徒劳。她凝视雕像太久，久到雕像面容上的裂隙开始成为一种提醒，提醒她需要做的还有许多。这种残缺已然成为了雕像的一部分，完美之上的裂隙不知何时，已经变为了完美的裂隙。`,
  },
  {
    id: "5",
    title: "彼世与彼岸",
    content: `她抬头望向天空，天很蓝，可她记得，天是黑红色的。

她翻过栅栏去寻找，旁边人的声音此起彼伏，拉住了她。他们说，她做了一个噩梦。

原来只是梦……白裙子如一片轻快的白云，雀跃的从草地上向小镇悠悠飘去。太阳照常升起，镇上的炊烟与笑语日复一日，日复一日……日复一日的平常让人沉沦，可她渐渐感受到了这种停滞，她的音乐不再进步，人们的思维不再焕新，日复一日……

所有人都已经很久没有走出这片土地了。食物从哪里来？生活用品为何源源不绝？她想着这些，走在离开的路上。

她走到小镇边缘，看到朦胧的迷雾前有一个红色的身影。它等在那里很久了，它似乎早就知道她终有一天会走到这里。

她想要后退，却不知为何将手放在了它的手心里，随即扑了个空，一把抓起了一手血污。

天确实是黑红色的。

只剩一只眼睛的残躯上覆满鲜红的花，她无法挣扎、无法行动，可为什么她还没有死去？她望着灰烬飘零的天，手上控制不住地想抓起泥泞的红土。局限的视野中，自己早已消失的手指慢慢重新出现。众人的死亡捧起了她的新生，众人的残响凝聚在了她的右眼中，直到她有勇气重新走出小镇、面对现实，包裹着她的“彼世”才退散到了她的内心深处。红花在匍匐中抖落，露出崭新的肌肤。耳边的笑语仍未消散，她确实做了一个很长的梦，却是“彼世”赐予她的一个美梦。

可谁说梦境不能成为现实？

她抬头望向天空，天真的很蓝。

这里不再有日复一日的停滞和戳破幻境的迷雾，因为这是她费尽心血重新创造的新的“彼岸”，与她心中的、他们遗赠给她的“彼世”如出一辙。

人们依旧呼喊着她的名字，这一次她一定能够拥抱真正的他们。`,
  },
]);

const currentStory = ref(0);

function prevStory() {
  currentStory.value =
    (currentStory.value - 1 + stories.value.length) % stories.value.length;
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}

function nextStory() {
  currentStory.value = (currentStory.value + 1) % stories.value.length;
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}

const storyRef = ref<HTMLElement | null>(null);
// 添加以下代码到现有的 script 部分
const showImageModal = ref(false);
const currentImage = ref({
  url: "",
  title: "",
  description: "",
});

// 打开图片弹窗
const openImageModal = (url, title = "", description = "") => {
  currentImage.value = { url, title, description };
  showImageModal.value = true;

  // 禁止背景滚动
  document.body.style.overflow = "hidden";
};

// 关闭图片弹窗
const closeImageModal = () => {
  showImageModal.value = false;

  // 恢复背景滚动
  document.body.style.overflow = "auto";
};

// 点击弹窗背景关闭
const handleModalBackdropClick = (event) => {
  if (event.target.classList.contains("image-modal")) {
    closeImageModal();
  }
};
</script>

<style scoped lang="scss">
/* 弗洛洛主题色系 */
$bg-deep: #06050a;
$deep-2: #120718;
$accent-1: #b86be0;
$accent-2: #ff6b85;
$accent-3: #9d4edd;
$text-main: #efe9ec;
$text-muted: rgba(239, 233, 236, 0.78);
$card-bg: rgba(8, 6, 10, 0.56);
$card-border: rgba(184, 107, 224, 0.12);
$card-border-2: rgba(255, 107, 133, 0.08);
$soft-shadow: rgba(0, 0, 0, 0.7);
$inner-glow: rgba(184, 107, 224, 0.05);
$blood-halo: rgba(255, 107, 133, 0.06);

/* 基础样式 */
.florlo-page {
  position: relative;
  min-height: 100vh;
  background: linear-gradient(180deg, $bg-deep 0%, $deep-2 72%);
  font-family: "Noto Sans SC", "Segoe UI", system-ui, -apple-system, sans-serif;
  color: $text-main;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: clamp(16px, 3vw, 60px) clamp(16px, 4vw, 80px);
  box-sizing: border-box;
  padding-top: 60px;
  overflow-x: hidden;

  &.reduced-motion * {
    transition: none !important;
    animation: none !important;
  }

  .container {
    position: relative;
    z-index: 10;
    max-width: 1200px;
    margin: 0 auto;
    padding: clamp(12px, 2vw, 32px);
  }
}

/* 浮动音符 */
.floating-note {
  position: fixed;
  font-size: 20px;
  color: $accent-1;
  opacity: 0.3;
  z-index: 1;
  pointer-events: none;
  animation: note-float 8s ease-in-out infinite;
  filter: drop-shadow(0 4px 12px rgba($accent-1, 0.2));

  &:nth-child(odd) {
    color: $accent-2;
    animation-duration: 10s;
  }
}

/* HERO区域 */
.hero {
  position: relative;
  display: grid;
  grid-template-columns: 280px 1fr;
  gap: 32px;
  align-items: start;
  padding: 16px;
  margin-bottom: 24px;
  border-radius: 20px;
  background: rgba(6, 5, 10, 0.4);
  backdrop-filter: blur(10px);
  border: 1px solid $card-border;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);

  /* 乐谱背景 */
  &::before {
    content: "";
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    background-image: repeating-linear-gradient(
        to bottom,
        rgba($accent-1, 0.03) 0px,
        rgba($accent-1, 0.03) 1px,
        transparent 1px,
        transparent 20px
      ),
      repeating-linear-gradient(
        to right,
        rgba($accent-2, 0.02) 0px,
        rgba($accent-2, 0.02) 1px,
        transparent 1px,
        transparent 60px
      );
    opacity: 0.4;
    mix-blend-mode: overlay;
    animation: staff-drift 25s linear infinite;
  }

  .left-col {
    display: flex;
    flex-direction: column;
    align-items: center;
    z-index: 6;
  }

  .avatar-card {
    background: linear-gradient(
      145deg,
      rgba(255, 255, 255, 0.03),
      rgba(184, 107, 224, 0.05)
    );
    border-radius: 18px;
    padding: 16px;
    display: grid;
    justify-items: center;
    gap: 12px;
    border: 1px solid $card-border;
    box-shadow: 0 20px 50px $soft-shadow,
      inset 0 1px 0 rgba(255, 255, 255, 0.05);
    cursor: pointer;
    transition: all 0.4s cubic-bezier(0.2, 0.9, 0.3, 1.1);
    width: 100%;
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
        rgba($accent-2, 0.1),
        transparent
      );
      transition: left 0.8s ease;
    }

    &:hover {
      transform: translateY(-8px) scale(1.02);
      box-shadow: 0 30px 80px rgba(0, 0, 0, 0.7), 0 0 40px rgba($accent-2, 0.1),
        inset 0 1px 0 rgba($accent-1, 0.1);

      &::before {
        left: 100%;
      }
    }

    /* 血泪装饰 */
    &::after {
      content: "";
      position: absolute;
      right: 20px;
      top: 20px;
      width: 6px;
      height: 24px;
      border-radius: 40% 40% 60% 60%;
      background: linear-gradient(
        180deg,
        rgba($accent-2, 0.9),
        rgba($accent-1, 0.7)
      );
      filter: blur(1px);
      opacity: 0.8;
      transform: rotate(8deg);
      box-shadow: 0 6px 20px rgba($accent-2, 0.15);
    }
  }

  .avatar-wrap {
    width: 100%;
    display: block;
    border-radius: 14px;
    overflow: hidden;
    position: relative;

    &::after {
      content: "";
      position: absolute;
      inset: 0;
      background: linear-gradient(45deg, transparent 60%, rgba($accent-1, 0.1));
      pointer-events: none;
    }
  }

  .avatar {
    width: 100%;
    height: auto;
    display: block;
    transition: all 0.6s ease;
    filter: saturate(0.85) contrast(0.95) brightness(0.9);
    transform: scale(1);
  }

  .avatar-card:hover .avatar {
    transform: scale(1.05);
    filter: saturate(0.9) contrast(1) brightness(0.95);
  }

  .avatar-meta {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 100%;
  }

  .avatar-badge {
    font-size: 0.85rem;
    color: $text-main;
    background: rgba(0, 0, 0, 0.25);
    padding: 6px 12px;
    border-radius: 999px;
    border: 1px solid rgba($accent-2, 0.1);
    font-weight: 600;
    text-align: center;
    backdrop-filter: blur(4px);
    transition: all 0.3s ease;

    &:hover {
      background: rgba($accent-1, 0.15);
      border-color: rgba($accent-1, 0.3);
    }
  }

  .right-col {
    display: flex;
    flex-direction: column;
    gap: 16px;
    z-index: 6;
  }

  .hero-panel {
    position: relative;
    z-index: 6;
    padding: 24px;
    border-radius: 18px;
    background: $card-bg;
    backdrop-filter: blur(12px) saturate(1.8);
    border: 1px solid $card-border;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.6), inset 0 1px 0 $inner-glow;
  }

  .name {
    font-size: clamp(2rem, 4vw, 3rem);
    margin: 0;
    font-weight: 900;
    letter-spacing: 1.5px;
    line-height: 1.1;
    display: inline-block;
    background: linear-gradient(
      135deg,
      $accent-1 0%,
      $accent-2 70%,
      #ff8fa3 100%
    );
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    color: transparent;
    text-shadow: 0 10px 40px rgba($accent-1, 0.3);
    position: relative;

    &::after {
      content: "";
      position: absolute;
      bottom: -8px;
      left: 0;
      width: 100px;
      height: 3px;
      background: linear-gradient(90deg, $accent-1, transparent);
      border-radius: 2px;
    }
  }

  .jp {
    font-size: 1.1rem;
    color: rgba($accent-1, 0.9);
    margin-left: 12px;
    font-weight: 400;
    vertical-align: middle;
  }

  .subtitle {
    margin: 12px 0 0;
    font-size: 1.2rem;
    color: $accent-1;
    font-weight: 600;
    line-height: 1.4;
    opacity: 0.95;
  }

  .lead {
    margin: 16px 0 0;
    color: $text-main;
    opacity: 0.9;
    line-height: 1.7;
    max-width: 72ch;
    font-size: 1.05rem;
  }

  .hero-tags {
    display: flex;
    gap: 10px;
    margin-top: 20px;
    flex-wrap: wrap;
  }

  .tag {
    font-size: 0.85rem;
    color: $accent-2;
    background: rgba($accent-2, 0.1);
    padding: 6px 14px;
    border-radius: 999px;
    border: 1px solid rgba($accent-2, 0.2);
    font-weight: 600;
    transition: all 0.3s ease;

    &:hover {
      background: rgba($accent-2, 0.2);
      transform: translateY(-2px);
    }
  }
}

/* 通用卡片样式 */
.card {
  margin-top: 32px;
  background: $card-bg;
  border-radius: 18px;
  padding: 28px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(10px);
  color: $text-main;
  border: 1px solid $card-border;
  transition: all 0.4s cubic-bezier(0.2, 0.9, 0.3, 1);
  position: relative;
  overflow: hidden;

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
      $accent-1,
      $accent-2,
      transparent
    );
    opacity: 0.3;
  }

  &:hover {
    transform: translateY(-6px);
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.6), 0 0 40px rgba($accent-1, 0.1);
    border-color: rgba($accent-1, 0.2);
  }

  .card-head {
    margin-bottom: 24px;
    display: flex;
    flex-direction: column;
    gap: 8px;

    h2 {
      margin: 0;
      font-size: 1.6rem;
      color: $accent-2;
      display: flex;
      align-items: center;
      gap: 12px;

      .icon {
        font-size: 1.4rem;
        opacity: 0.8;
      }
    }
  }

  .section-subtitle {
    color: rgba($text-main, 0.7);
    font-size: 1rem;
    margin: 0;
    font-weight: 400;
  }
}

/* 图标样式 */
.icon {
  display: inline-block;
  width: 24px;
  height: 24px;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  vertical-align: middle;

  &.icon-profile {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%23b86be0'%3E%3Cpath d='M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z'/%3E%3C/svg%3E");
  }

  &.icon-resonance {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%23ff6b85'%3E%3Cpath d='M12 3v10.55c-.59-.34-1.27-.55-2-.55-2.21 0-4 1.79-4 4s1.79 4 4 4 4-1.79 4-4V7h4V3h-6z'/%3E%3C/svg%3E");
  }

  &.icon-themes {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%239d4edd'%3E%3Cpath d='M12 2L4.5 20.29l.71.71L12 18l6.79 3 .71-.71z'/%3E%3C/svg%3E");
  }

  &.icon-timeline {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%23b86be0'%3E%3Cpath d='M11.99 2C6.47 2 2 6.48 2 12s4.47 10 9.99 10C17.52 22 22 17.52 22 12S17.52 2 11.99 2zM12 20c-4.42 0-8-3.58-8-8s3.58-8 8-8 8 3.58 8 8-3.58 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z'/%3E%3C/svg%3E");
  }

  &.icon-easter-egg {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%23ff6b85'%3E%3Cpath d='M12 3c-4.97 0-9 4.03-9 9s4.03 9 9 9 9-4.03 9-9-4.03-9-9-9zm0 16c-3.86 0-7-3.14-7-7s3.14-7 7-7 7 3.14 7 7-3.14 7-7 7zm3-7c0 1.66-1.34 3-3 3s-3-1.34-3-3 1.34-3 3-3 3 1.34 3 3z'/%3E%3C/svg%3E");
  }

  &.icon-chain {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%239d4edd'%3E%3Cpath d='M17 7h-4v2h4c1.65 0 3 1.35 3 3s-1.35 3-3 3h-4v2h4c2.76 0 5-2.24 5-5s-2.24-5-5-5zm-6 8H7c-1.65 0-3-1.35-3-3s1.35-3 3-3h4V7H7c-2.76 0-5 2.24-5 5s2.24 5 5 5h4v-2zm-3-4h8v2H8z'/%3E%3C/svg%3E");
  }

  &.icon-stories {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='%23b86be0'%3E%3Cpath d='M4 6H2v14c0 1.1.9 2 2 2h14v-2H4V6zm16-4H8c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm0 14H8V4h12v12zM10 9h8v2h-8zm0 3h4v2h-4zm0-6h8v2h-8z'/%3E%3C/svg%3E");
  }
}

/* 概述区域 */
.overview {
  .two-col {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 28px;
    margin-top: 16px;
  }

  .text-block {
    h3 {
      color: $accent-1;
      font-size: 1.3rem;
      margin-bottom: 12px;
      padding-bottom: 8px;
      border-bottom: 2px solid rgba($accent-1, 0.2);
    }

    p {
      line-height: 1.7;
      color: rgba($text-main, 0.9);
      margin-bottom: 12px;

      &:last-child {
        margin-bottom: 0;
      }
    }
  }
}

/* 主题遗物区域 */
.themes-section {
  .themes-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 24px;
    margin-top: 20px;
  }

  .theme-card {
    background: rgba(10, 8, 15, 0.5);
    border-radius: 16px;
    overflow: hidden;
    border: 1px solid rgba($accent-1, 0.1);
    transition: all 0.4s ease;
    display: flex;
    flex-direction: column;

    &:hover {
      transform: translateY(-8px);
      border-color: rgba($accent-1, 0.3);
      box-shadow: 0 15px 40px rgba(0, 0, 0, 0.4);
    }
  }

  .theme-img {
    height: 180px;
    overflow: hidden;

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 0.6s ease;
    }

    .theme-card:hover img {
      transform: scale(1.05);
    }
  }

  .theme-content {
    padding: 20px;
    flex-grow: 1;
    display: flex;
    flex-direction: column;

    h3 {
      color: $accent-2;
      margin: 0 0 12px 0;
      font-size: 1.3rem;
    }

    p {
      color: rgba($text-main, 0.85);
      line-height: 1.6;
      margin-bottom: 10px;
      flex-grow: 1;

      &:last-child {
        margin-bottom: 0;
      }
    }
  }
}

/* 剧情时间线 */
.timeline-section {
  .story-timeline {
    position: relative;
    padding: 40px 0;

    .timeline-track {
      position: absolute;
      left: 50px;
      top: 0;
      bottom: 0;
      width: 3px;
      background: linear-gradient(
        180deg,
        rgba($accent-1, 0.3),
        rgba($accent-2, 0.6),
        rgba($accent-1, 0.3)
      );
      border-radius: 2px;

      &::before {
        content: "";
        position: absolute;
        top: 0;
        left: -4px;
        right: -4px;
        height: 12px;
        background: rgba($accent-2, 0.2);
        border-radius: 6px;
      }

      &::after {
        content: "";
        position: absolute;
        bottom: 0;
        left: -4px;
        right: -4px;
        height: 12px;
        background: rgba($accent-1, 0.2);
        border-radius: 6px;
      }
    }
  }

  .timeline-item {
    position: relative;
    margin-bottom: 40px;
    padding-left: 100px;

    &:last-child {
      margin-bottom: 0;
    }
  }

  .timeline-marker {
    position: absolute;
    left: 0;
    top: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100px;
  }

  .marker-dot {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: linear-gradient(135deg, $accent-1, $accent-2);
    box-shadow: 0 0 20px rgba($accent-2, 0.5);
    position: relative;
    z-index: 2;

    &::before {
      content: "";
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 32px;
      height: 32px;
      border-radius: 50%;
      background: rgba($accent-2, 0.2);
      z-index: 1;
    }
  }

  .marker-version {
    margin-top: 8px;
    font-size: 0.9rem;
    font-weight: 700;
    color: $accent-1;
    background: rgba($accent-1, 0.1);
    padding: 4px 12px;
    border-radius: 12px;
    border: 1px solid rgba($accent-1, 0.2);
  }

  .timeline-content {
    background: rgba(12, 10, 18, 0.6);
    border-radius: 16px;
    padding: 20px;
    border-left: 4px solid $accent-1;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);

    h3 {
      color: $accent-2;
      margin: 0 0 10px 0;
      font-size: 1.3rem;
    }

    p {
      color: rgba($text-main, 0.85);
      line-height: 1.6;
      margin-bottom: 15px;
    }
  }

  .timeline-tags {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }

  .timeline-tag {
    font-size: 0.8rem;
    color: rgba($accent-2, 0.9);
    background: rgba($accent-2, 0.1);
    padding: 4px 10px;
    border-radius: 12px;
    border: 1px solid rgba($accent-2, 0.2);
  }
}

/* 人物彩蛋 */
.easter-eggs-section {
  .eggs-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
    gap: 24px;
    margin-top: 20px;
  }

  .egg-card {
    background: rgba(12, 10, 18, 0.6);
    border-radius: 16px;
    padding: 24px;
    border: 1px solid rgba($accent-1, 0.1);
    transition: all 0.4s ease;
    position: relative;
    overflow: hidden;

    &::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      height: 4px;
      background: linear-gradient(90deg, $accent-1, $accent-2);
      opacity: 0.5;
    }

    &:hover {
      transform: translateY(-6px);
      border-color: rgba($accent-1, 0.3);
      box-shadow: 0 15px 40px rgba(0, 0, 0, 0.4);
    }

    &.flower-egg::before {
      background: linear-gradient(90deg, #ff6b85, #ff8fa3);
    }

    &.voice-egg::before {
      background: linear-gradient(90deg, #b86be0, #9d4edd);
    }

    &.achievement-egg::before {
      background: linear-gradient(90deg, #ffb347, #ffcc33);
    }

    &.location-egg::before {
      background: linear-gradient(90deg, #4ecdc4, #44a08d);
    }

    &.access-egg::before {
      background: linear-gradient(90deg, #8a2be2, #9370db);
    }
  }

  .egg-icon {
    font-size: 2.5rem;
    margin-bottom: 16px;
    opacity: 0.8;
  }

  h3 {
    color: $accent-2;
    margin: 0 0 16px 0;
    font-size: 1.3rem;
  }

  p,
  li {
    color: rgba($text-main, 0.85);
    line-height: 1.6;
    margin-bottom: 10px;

    strong {
      color: $accent-1;
    }
  }

  ul {
    padding-left: 20px;
    margin-bottom: 16px;

    li {
      margin-bottom: 8px;

      strong {
        color: $accent-1;
      }
    }
  }

  .egg-note {
    font-style: italic;
    color: rgba($accent-1, 0.9);
    margin-top: 12px;
    padding-top: 12px;
    border-top: 1px dashed rgba($accent-1, 0.2);
  }

  .achievement-item,
  .access-method {
    margin-bottom: 20px;

    &:last-child {
      margin-bottom: 0;
    }

    h4 {
      color: $accent-1;
      margin: 0 0 8px 0;
      font-size: 1.1rem;
    }
  }

  .time-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
    margin: 16px 0;
  }

  .time-slot {
    display: flex;
    flex-direction: column;
    background: rgba($accent-1, 0.05);
    padding: 10px;
    border-radius: 10px;
    border: 1px solid rgba($accent-1, 0.1);

    .time {
      color: $accent-2;
      font-weight: 600;
      font-size: 0.95rem;
    }

    .time-desc {
      color: rgba($text-main, 0.7);
      font-size: 0.85rem;
      margin-top: 4px;
    }
  }

  .achievement-img-placeholder,
  .access-img-placeholder {
    margin-top: 12px;
    background: rgba(0, 0, 0, 0.2);
    border-radius: 10px;
    padding: 30px;
    text-align: center;
    border: 2px dashed rgba($accent-1, 0.2);

    .img-placeholder {
      color: rgba($text-main, 0.5);
      font-style: italic;
      img {
        width: 100%;
        cursor: pointer;
      }
    }
  }
}

/* 共鸣链 */
.resonance-chain-section {
  .chain-container {
    position: relative;
    padding: 40px 0;

    .chain-track {
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      top: 0;
      bottom: 0;
      width: 3px;
      background: linear-gradient(
        180deg,
        rgba($accent-1, 0.2),
        rgba($accent-2, 0.4),
        rgba($accent-1, 0.2)
      );
      border-radius: 2px;

      @media (max-width: 768px) {
        left: 30px;
      }
    }
  }

  .chain-item {
    position: relative;
    margin-bottom: 40px;
    display: flex;
    align-items: center;

    &:nth-child(odd) {
      flex-direction: row-reverse;

      .chain-content {
        margin-right: 60px;
        margin-left: 0;
        text-align: right;

        @media (max-width: 768px) {
          margin-right: 0;
          margin-left: 80px;
          text-align: left;
        }
      }
    }

    &:last-child {
      margin-bottom: 0;
    }

    @media (max-width: 768px) {
      flex-direction: row !important;

      .chain-content {
        margin-right: 0 !important;
        margin-left: 80px !important;
        text-align: left !important;
      }
    }
  }

  .chain-level {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: linear-gradient(135deg, $accent-1, $accent-2);
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 900;
    color: white;
    box-shadow: 0 0 25px rgba($accent-2, 0.5);
    z-index: 2;
    font-size: 1.2rem;

    @media (max-width: 768px) {
      left: 30px;
      transform: translateX(0);
    }
  }

  .chain-content {
    width: 45%;
    background: rgba(12, 10, 18, 0.7);
    border-radius: 16px;
    padding: 20px;
    border: 1px solid rgba($accent-1, 0.15);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    margin-left: 60px;

    @media (max-width: 768px) {
      width: calc(100% - 100px);
    }

    h3 {
      color: $accent-2;
      margin: 0 0 10px 0;
      font-size: 1.3rem;
    }

    .chain-desc {
      color: rgba($text-main, 0.8);
      line-height: 1.6;
      margin-bottom: 16px;
      font-style: italic;
    }
  }

  .chain-effects {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .effect {
    display: flex;
    align-items: flex-start;
    gap: 10px;

    .effect-icon {
      color: $accent-1;
      flex-shrink: 0;
      margin-top: 2px;
    }

    span {
      color: rgba($text-main, 0.85);
      line-height: 1.5;
    }
  }
}

/* 弗洛洛纪事 */
.ll-stories {
  .card {
    border-radius: 18px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
    color: $text-main;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    background: $card-bg;
    border: 1px solid $card-border;
  }

  .card-head {
    padding: 24px;
    border-bottom: 1px solid rgba($accent-2, 0.1);
    display: flex;
    flex-direction: column;
    gap: 8px;

    .card-title {
      font-size: 1.6rem;
      font-weight: 700;
      color: $accent-1;
      letter-spacing: 0.6px;
      display: flex;
      align-items: center;
      gap: 12px;
    }
  }

  .card-body {
    padding: 28px;
    min-height: 280px;
    position: relative;
    background: linear-gradient(180deg, rgba(0, 0, 0, 0), rgba(8, 8, 10, 0.05));
  }

  .story {
    color: rgba($text-main, 0.96);

    .story-head {
      font-size: 1.5rem;
      color: $accent-2;
      font-weight: 700;
      margin-bottom: 20px;
      padding-bottom: 12px;
      border-bottom: 2px solid rgba($accent-2, 0.2);
    }

    .story-content {
      line-height: 1.8;
      color: rgba($text-main, 0.9);
      white-space: pre-wrap;
      font-size: 1.05rem;
      white-space: pre-wrap;
    }
  }

  .card-foot {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
    padding: 20px 24px;
    border-top: 1px solid rgba($accent-1, 0.1);
    background: linear-gradient(
      180deg,
      rgba(0, 0, 0, 0.05),
      rgba(0, 0, 0, 0.1)
    );
  }

  .story-pager {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    flex-grow: 1;
  }

  .story-title {
    color: rgba($accent-1, 0.9);
    font-weight: 600;
    font-size: 1.05rem;
  }

  .story-counter {
    color: rgba($text-main, 0.7);
    font-size: 0.95rem;
    font-weight: 500;
  }

  .nav-btn {
    background: rgba($accent-1, 0.1);
    border: 1px solid rgba($accent-1, 0.2);
    color: $accent-2;
    padding: 10px 16px;
    border-radius: 10px;
    cursor: pointer;
    font-size: 1.2rem;
    transition: all 0.3s ease;
    min-width: 50px;
    display: flex;
    align-items: center;
    justify-content: center;

    &:hover {
      background: rgba($accent-2, 0.15);
      border-color: rgba($accent-2, 0.3);
      transform: translateY(-2px);
    }

    &:active {
      transform: translateY(0);
    }
  }

  /* 切换动画 */
  .fade-slide-enter-active,
  .fade-slide-leave-active {
    transition: all 400ms cubic-bezier(0.2, 0.9, 0.3, 1);
  }

  .fade-slide-enter-from {
    opacity: 0;
    transform: translateY(10px);
  }

  .fade-slide-enter-to {
    opacity: 1;
    transform: translateY(0);
  }

  .fade-slide-leave-from {
    opacity: 1;
    transform: translateY(0);
  }

  .fade-slide-leave-to {
    opacity: 0;
    transform: translateY(-10px);
  }
}

/* 动画关键帧 */
@keyframes staff-drift {
  0% {
    transform: translateY(-4%);
  }
  50% {
    transform: translateY(4%);
  }
  100% {
    transform: translateY(-4%);
  }
}

@keyframes note-float {
  0%,
  100% {
    transform: translateY(0) rotate(0deg) scale(1);
    opacity: 0.3;
  }
  50% {
    transform: translateY(-20px) rotate(180deg) scale(1.1);
    opacity: 0.6;
  }
}

/* 响应式设计 */
@media (max-width: 1024px) {
  .florlo-page {
    padding: clamp(12px, 2vw, 40px) clamp(12px, 3vw, 40px);
  }

  .hero {
    grid-template-columns: 1fr;
    gap: 24px;

    .avatar-card {
      max-width: 280px;
      margin: 0 auto;
    }
  }

  .eggs-grid {
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)) !important;
  }
}

@media (max-width: 768px) {
  .florlo-page {
    padding: 12px;
    padding-top: 40px;
  }

  .container {
    padding: 8px !important;
  }

  .hero {
    padding: 12px;
    margin-bottom: 20px;

    .hero-panel {
      padding: 16px;
    }

    .name {
      font-size: 1.8rem;
    }

    .subtitle {
      font-size: 1rem;
    }

    .lead {
      font-size: 0.95rem;
    }
  }

  .card {
    padding: 20px;
    margin-top: 24px;

    .card-head h2 {
      font-size: 1.4rem;
    }
  }

  .themes-grid {
    grid-template-columns: 1fr !important;
  }

  .timeline-item {
    padding-left: 80px !important;
  }

  .marker-version {
    font-size: 0.8rem;
    padding: 3px 8px;
  }

  .chain-content {
    padding: 16px !important;
  }

  .ll-stories {
    .card-body {
      padding: 20px;
      min-height: 220px;
    }

    .story-head {
      font-size: 1.3rem;
    }

    .story-content {
      font-size: 1rem;
    }
  }
}

@media (max-width: 480px) {
  .hero .avatar-card {
    width: 100%;
    max-width: none;
  }

  .egg-card {
    padding: 20px !important;
  }

  .timeline-content {
    padding: 16px !important;
  }

  .ll-stories .card-foot {
    padding: 16px;
  }

  .floating-note {
    display: none;
  }
}
/* 图片弹窗样式 */
.image-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(6, 5, 10, 0.95);
  backdrop-filter: blur(10px);
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  animation: modal-fade-in 0.3s ease;

  &.fade-enter-active,
  &.fade-leave-active {
    transition: opacity 0.3s ease;
  }

  &.fade-enter-from,
  &.fade-leave-to {
    opacity: 0;
  }
}

.modal-content {
  position: relative;
  background: rgba(12, 10, 18, 0.95);
  border-radius: 16px;
  max-width: 90vw;
  max-height: 90vh;
  width: auto;
  height: auto;
  border: 1px solid rgba($accent-1, 0.3);
  box-shadow: 0 25px 80px rgba(0, 0, 0, 0.8), 0 0 60px rgba($accent-1, 0.2);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  animation: modal-slide-up 0.4s cubic-bezier(0.2, 0.9, 0.3, 1.1);
}

.modal-close {
  position: absolute;
  top: 16px;
  right: 16px;
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: rgba($accent-2, 0.2);
  border: 1px solid rgba($accent-2, 0.3);
  color: $text-main;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
  transition: all 0.3s ease;

  &:hover {
    background: rgba($accent-2, 0.3);
    transform: rotate(90deg);
    border-color: rgba($accent-2, 0.5);
  }

  svg {
    width: 24px;
    height: 24px;
  }
}

.image-info {
  padding: 24px 24px 16px;
  background: linear-gradient(
    180deg,
    rgba(12, 10, 18, 0.9),
    rgba(12, 10, 18, 0.7)
  );
  border-bottom: 1px solid rgba($accent-1, 0.2);

  .image-title {
    color: $accent-2;
    margin: 0 0 8px 0;
    font-size: 1.4rem;
    font-weight: 700;
  }

  .image-desc {
    color: rgba($text-main, 0.8);
    margin: 0;
    font-size: 0.95rem;
    line-height: 1.5;
  }
}

.image-container {
  position: relative;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: auto;
  padding: 20px;
  min-height: 300px;

  img {
    max-width: 100%;
    max-height: 70vh;
    object-fit: contain;
    border-radius: 8px;
    cursor: default;
  }
}

/* 动画关键帧 */
@keyframes modal-fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes modal-slide-up {
  from {
    opacity: 0;
    transform: translateY(30px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

/* 响应式调整 */
@media (max-width: 768px) {
  .image-modal {
    padding: 10px;
  }

  .modal-content {
    max-width: 95vw;
    max-height: 95vh;
  }

  .modal-close {
    top: 12px;
    right: 12px;
    width: 40px;
    height: 40px;
  }

  .image-info {
    padding: 20px 20px 12px;

    .image-title {
      font-size: 1.2rem;
    }
  }

  .image-container {
    padding: 15px;

    img {
      max-height: 60vh;
    }
  }
}

@media (max-width: 480px) {
  .modal-content {
    border-radius: 12px;
  }

  .image-info {
    padding: 16px 16px 10px;

    .image-title {
      font-size: 1.1rem;
    }

    .image-desc {
      font-size: 0.9rem;
    }
  }

  .image-container {
    padding: 10px;

    img {
      max-height: 50vh;
    }
  }
}

/* 页脚样式 */
.page-footer {
  margin-top: 60px;

  background: linear-gradient(
    180deg,
    transparent,
    rgba(8, 6, 12, 0.7) 20%,
    rgba(6, 5, 10, 0.9)
  );
  border-top: 1px solid rgba($accent-1, 0.1);
  position: relative;
  overflow: hidden;
}

.footer-divider {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin-bottom: 30px;
  opacity: 0.6;

  .music-note {
    color: $accent-1;
    font-size: 1.2rem;
    animation: note-float 3s ease-in-out infinite;

    &:nth-child(3) {
      animation-delay: 0.5s;
    }
  }

  .divider-line {
    width: 200px;
    height: 1px;
    background: linear-gradient(
      90deg,
      transparent,
      rgba($accent-1, 0.5),
      transparent
    );
  }
}

.footer-content {
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

.footer-theme {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin-bottom: 30px;

  .theme-icon {
    font-size: 2rem;
    color: $accent-2;
    opacity: 0.8;
  }

  .theme-text {
    color: rgba($accent-1, 0.9);
    font-style: italic;
    font-size: 1.1rem;
    letter-spacing: 1px;
  }
}

.footer-bottom {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
  padding-top: 20px;
  border-top: 1px solid rgba($accent-1, 0.1);

  .bottom-flower {
    color: rgba($accent-2, 0.6);
    font-size: 1.2rem;
    animation: flower-float 4s ease-in-out infinite;

    &:last-child {
      animation-delay: 0.5s;
    }
  }

  .bottom-text {
    color: rgba($text-main, 0.6);
    font-size: 0.9rem;
    letter-spacing: 2px;
    text-transform: uppercase;
  }
}

/* 动画 */
@keyframes flower-float {
  0%,
  100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.6;
  }
  50% {
    transform: translateY(-5px) rotate(10deg);
    opacity: 1;
  }
}

/* 响应式调整 */
@media (max-width: 768px) {
  .page-footer {
    padding: 30px 15px 20px;
    margin-top: 40px;
  }

  .footer-divider {
    .divider-line {
      width: 100px;
    }
  }

  .footer-theme {
    .theme-text {
      font-size: 1rem;
    }
  }

  .footer-bottom {
    .bottom-text {
      font-size: 0.8rem;
      letter-spacing: 1px;
    }
  }
}
</style>