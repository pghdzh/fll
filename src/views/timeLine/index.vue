<template>
  <div class="megumi-page" aria-live="polite">
    <main class="container" ref="mainContainer">
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
              />
            </picture>
            <div class="avatar-meta">
              <span class="avatar-badge">共鸣能力：重世的交响乐</span>
            </div>
          </button>
        </div>
        <div class="right-col" ref="metaCard">
          <div class="hero-panel">
            <h1 class="name">弗洛洛 <span class="jp">（残星会会监）</span></h1>
            <p class="subtitle">于失亡彼岸奏响命运乐章的音律咏叹者</p>
            <p class="lead">
              神秘而危险的指挥家……
              她沉默地挥舞指挥棒，手中调律频率演奏出的“灵魂”旋律，既可塑造美好的世界，也可唤来千军万马。
            </p>
          </div>
        </div>
      </header>

      <!-- 内容卡片区 -->
      <section id="personality" class="card personality">
        <div class="card-head">
          <h2>核心机制</h2>
        </div>
        <ul class="traits">
          <li>
            弗洛洛第3段普攻，共鸣技能可衔接施放强化普攻或强化共鸣技能，获得不同的【特殊能量】。
            获得6枚【特殊能量】后，可以施放强化重击，造成大量伤害并解锁共鸣解放。
          </li>
          <li>
            施放共鸣解放后，弗洛洛会依次读取【特殊能量】，改变赫卡忒的强力攻击形式。
            强化状态期间，队伍中角色施放声骸技能时，赫卡忒会施放强力攻击。
          </li>
          <li>· 亡与死的乐章：AAAA或EA</li>
          <li>· 永不消逝的梦呓：AAAE或EE</li>
          <li>· 谱曲终末：拥有6枚【乐声】且不处于【定音】状态时，长按。</li>
          <li>
            · 强化攻击·赫卡忒：处于指挥状态时，AAA或队伍中的角色施放声骸技能。
          </li>
          <li style="font-weight: bold">输出流程：</li>
          <li>基础流程：EEAAAE-闪-Q-AAAA-闪-AAA-闪-AE-闪-RAR</li>
          <li>
            技能缩写：普攻 = A，重击 = Z，共鸣技能 = E，共鸣解放 = R，声骸 = Q
          </li>
        </ul>
      </section>

      <section id="overview" class="card overview">
        <div class="card-head">
          <h2>角色背景与形象</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <h3>背景</h3>
            <p>
              残星会会监弗洛洛，安静、忧郁，似乎靠近她就会坠入无尽的忧伤之中。
              在生死之间，她谱写了一篇又一篇曲谱，不断构筑着她心中完美的世界。
            </p>
          </div>
          <div class="text-block">
            <h3>外貌／着装</h3>
            <p>
              弗洛洛以白发红瞳的造型登场，手持音感仪作为武器，身着指挥家服饰，形象设计偏向悲情美少女风格，带有疏离感与危险气质。
            </p>
          </div>
        </div>
      </section>

      <section id="resonance-status" class="card overview">
        <div class="card-head">
          <h2>共鸣状况</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <p>
              弗洛洛超频临界值极低，稳定性极低，超频状态频繁出现。
              其超频一直处于可控状态，可利用这种精神稳定性，探寻稳定延长普通人超频状态的方法。
            </p>
          </div>
        </div>
      </section>

      <!-- 新增三大主题区域 -->
      <section id="food" class="card special-section">
        <div class="card-head">
          <h2>自撰的乐谱</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item1.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>她面色平静，双手之间却不停对一首首乐曲进行激越的变奏与夸张。</p>
            <p>
              摇摇欲坠的理性已被汹涌的情感彻底吞噬，一段段音符在她手中被撕得粉碎，又重新揉捏成凄惨诡异的模样。
            </p>
          </div>
        </div>
      </section>
      <section id="shattered-crystal" class="card special-section">
        <div class="card-head">
          <h2>遗留的提琴</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item2.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>她亲手接过了大家的礼物。她亲手埋葬了大家的骸骨。</p>
            <p>
              琴弦振动发出的是一声又一声的欢唱。她持弓走近的是一次又一次的死亡。
            </p>
          </div>
        </div>
      </section>

      <section id="cyclic-clock" class="card special-section">
        <div class="card-head">
          <h2>未送出的门票</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item3.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              当她再一次拿着门票在音乐厅门口等候，她开始不知道自己等待的到底是什么。是那位能听懂乐曲的知音还是那条与自己目标相同的道路。
            </p>
            <p>直到等到听众全部进场，门外已无他人，这张票仍然在她手中。</p>
            <p>
              她想通了，都不是，她想等来的，是自己心中勾勒出的、实际上并不存在的，一根救命稻草。
            </p>
            <p>她不会再等了。</p>
          </div>
        </div>
      </section>

      <section id="collective-album" class="card special-section">
        <div class="card-head">
          <h2>红醋栗果饼</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/food.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              极致的酸涩与殷红浓稠的果酱一同淌下，缓缓浸入蜜饼间的每一处缝隙，直至完全覆盖它原本香甜可口的味道。
            </p>
            <p>
              除了弗洛洛，没人能接受这种古怪的味蕾刺激，也没人知道这种小巧酸硬的果子到底从何而来。
            </p>
          </div>
        </div>
      </section>

      <section
        id="timeline"
        class="card timeline"
        aria-labelledby="timeline-heading"
      >
        <div class="card-head">
          <h2 id="timeline-heading">共鸣链详解</h2>
        </div>
        <div class="tl-wrap" role="list">
          <div class="vl" aria-hidden="true"></div>
          <ol class="tl-list">
            <li
              v-for="(ev, i) in timeline"
              :key="i"
              class="tl-item"
              role="listitem"
              tabindex="0"
              :aria-label="``"
            >
              <div class="tl-card">
                <h3 class="tl-title">第 {{ i + 1 }} 链 · {{ ev.title }}</h3>
                <p class="tl-desc">{{ ev.desc }}</p>
              </div>
            </li>
          </ol>
        </div>
      </section>

      <section class="ll-stories" ref="storyRef">
        <div class="card" role="region" aria-labelledby="stories-title">
          <header class="card-head">
            <h2 id="stories-title" class="card-title">弗洛洛纪事</h2>
          </header>

          <main class="card-body">
            <transition name="fade-slide" mode="out-in">
              <article
                :key="items[current].id"
                :id="`panel-${items[current].id}`"
                class="story"
                role="tabpanel"
                :aria-labelledby="`tab-${items[current].id}`"
              >
                <h3 class="story-head">{{ items[current].title }}</h3>
                <div
                  class="story-content"
                  v-html="items[current].content"
                ></div>
              </article>
            </transition>
          </main>

          <footer class="card-foot">
            <button class="nav-btn" @click="prev" aria-label="上一则故事">
              ‹
            </button>
            <div class="pager">{{ current + 1 }} / {{ items.length }}</div>
            <button class="nav-btn" @click="next" aria-label="下一则故事">
              ›
            </button>
          </footer>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

const timeline = ref([
  {
    title: "钥匙，通往冥界的奥秘",
    desc: `亡与死的乐章伤害倍率提升80%。
永不消逝的梦呓伤害倍率提升80%。
弗洛洛不处于指挥状态且不处于战斗状态4秒后，若【乐声】少于2枚，获得【乐声·彩乐】，直至【乐声】不少于2枚。`,
  },
  {
    title: "绳索，重生更新的纽带",
    desc: `谱曲终末伤害倍率提升75%，余响对谱曲终末的倍率增加效果提升75%。
施放谱曲终末时可获得14层余响。`,
  },
  {
    title: "匕首，消弭妄想的力量",
    desc: `声骸技能伤害加深80%。
施放谱曲终末期间，所有【乐声】将依次转化为【乐声·彩乐】。
被强化攻击·彩乐·赫卡忒命中的目标，攻击降低20%，持续15秒。`,
  },
  {
    title: "火炬，新径启行的引导",
    desc: `施放声骸技能时，队伍中的角色全属性伤害加成提升20%，持续30秒。`,
  },
  {
    title: "岔路，穿越生命的要地",
    desc: `进入指挥状态时，生成领域停滞周围的目标，领域持续4秒。结束指挥状态或切换至其他角色时，将提前清除停滞效果。
处于指挥状态时，受到伤害降低30%。%。`,
  },
  {
    title: "深夜，走出安息与终结",
    desc: `强化攻击·赫卡忒伤害倍率提升24%。
施放亡与死的乐章、永不消逝的梦呓期间，将召唤赫卡忒施放1次重世幻象·赫卡忒，造成弗洛洛攻击216.42%的湮灭伤害，命中目标时获得8层余响。本次伤害为声骸技能伤害。
处于指挥状态时，若弗洛洛不为队伍中登场角色，目标受到赫卡忒和弗洛洛的伤害提升40%。
处于指挥状态时，若弗洛洛为队伍中登场角色，湮灭伤害加成提升60%。`,
  },
]);

const reducedMotion =
  window.matchMedia &&
  window.matchMedia("(prefers-reduced-motion: reduce)").matches;

const items = [
  {
    id: "1",
    title: "红与黑的歌",
    content: `
    听众们总是异常投入，或是僵在原地，或是控制不住地颤栗摇摆，黑暗中的一双双眼睛死死盯着那纤弱又有力的背影，目光因随着双手起落而晕眩，如同纷飞的音符凌乱于大段大段即兴的激昂挽歌中。

沸腾的急板旋律点燃了心火，让人们压抑在心底的情绪肆意疯长，无法掌控、无法自拔，直至被推搡着和她一起窒息于乐曲高潮时极致狂想的曲调，在不知不觉间走到精神世界的边缘，站在悬崖边望着深不见底的深渊，迫不及待地在极端兴奋中，感受毁灭与新生的绽放。

在那一步凌空的脚步迈下前，聚光灯下的指挥家稳稳定住了自己的双臂，残忍地让乐曲戛然而止。

她站在那里，一动不动宛如雕像，胸膛却仍不停剧烈起伏，独自在情绪中徜徉，刚才将她拉回来的那一丝理智已经崩断，逸散在了浓郁的情感中。她只望着天顶，从不会望向台下的任何人。

这首曲子就这样终结？

人们沉默，惊讶，徘徊，最后失神离开。可还没走出演奏厅的大门，耳边就已重新听到了刚才的乐音，从过去传来的乐音。那些乐曲毫不费力的刻进了每一个人的脑海里，一遍又一遍的重复，比当初亲耳听到的还要鼓动人心，不断唤醒着、放大着每个人心中极端的疯狂。

眼前一片光明的时代，人们大笑着走出门去，鲜红的歌让他们迫切地拥住美好的明天；眼前一片黑暗的时代，人们承受不住这最后一根稻草，漆黑的歌让他们果断地走向最高处的窗前。

她似乎从不在乎自己的音乐会为他人染上何种颜色，自顾自的在时代轮替中不停舞动双手。坐在演奏厅中的人也不多想自己将在音乐中走向何方。无人细究过，创造出这些温暖与悲伤乐曲的人一次次站到台上，究竟只是无所求的创造或是发泄，还是为了埋于尘土中的爱与过往。
    `,
  },
  {
    id: "2",
    title: "隐没的花簇",
    content: `
   猩红的厅殿中常常空无一人，只有一面面象征着会监的旗帜在冷风中飘扬。那面彼岸花的旗帜颜色早已暗沉，但它永远都悬挂在边缘，不曾掉落。残星会人的眼中，会监们皆是来无影去无踪的存在，没有几个人会在这里多做停留，可在那些抓不住的身影中，她是隐没最深的人。除了那面旗帜，这里没有任何属于她的痕迹。她放弃了会监在这里可以拥有的一切，她的房间中空无一物，她不会在这里多说半句……她不归属于这里。疏离的沉默让人们忽视，当人们觉得她对于残星会、残星会对于她都不过可有可无时，沉寂的彼岸花会突然从黑暗的世界中出现，凌厉地绽放，将花瓣精准刺穿敌人的胸膛，完成属于她的任务，然后重新消失在众人视野中。她似乎对这里的一切都没兴趣，也很少有人知道她加入到底是为了什么。

残星会中只有一个地方，能够牵绊住她的脚步。她偶尔站在实验室上层，沉默俯视着人类与残象肢体耦合技术的发展、超频技术的不断进步，看着那些主动寻求进化的人们眼神中的渴望，看着他们艰难地走向全新的人生，成功的尖叫，失败的哀嚎，仿佛看到的是更多的人。

她有时会掌一盏灯，如同幽灵般在深夜中独自一人出现在这里，静静翻看最新的研究进展，却从来没有带过一页资料离开，似乎每一次翻阅都以失望告终。偶尔有人看到这一幕也不明白，她到底想寻找到什么。
    `,
  },

  {
    id: "3",
    title: "前行于困顿",
    content: `
    当生命被无限延长，到底应该在世界上走出一条什么样的道路？她不知道。

前所未有的空旷与悠远让她感到无所适从，于是她选择闭上双眼，将孤独、悲怆、不甘发泄在音乐中，在任何地方、任何时间不顾一切的挥洒音符。

癫狂的人变得声名显赫，却一如既往的独断专行。围绕着她的亲和的、厌恶的言论比她消失得更早，重复的历史、重复的言论不断上演，她不可避免的一次次重新变得孤单，直到后来她才明白，她想寻找到依靠，可真情实感不过徒劳。短暂的美好消逝时，只有无法离开的人继续承受痛苦。她渐渐恢复了理智，直到彻底封闭内心，最终偏离为麻木，脱离所有人踽踽独行，不再看向台下，不再看向世界。

她不更换名称去遮掩，不管人们对她的猜测与探查。任何人都看得出她的年龄，但没有几个人能猜透她心灵的年龄。

她本会如无意义的幽灵般永远游荡下去，可一场音乐会让她找到了自己新的人生目标。难熬的时间在她想通的那一瞬间飞速流逝，宽广的生命蓝图开始收缩。她不顾日夜的汲取不曾了解的一切，寻找时间长河中一闪而过的天才。如果人们认为她才是最大的希望，那她便会大方满足他们研究的愿望。她在不同时代中让自己一次又一次躺在不同的试验器械之下，目睹自己分解，看着自己重生。疼痛让她感受到自己仍困于现实，却也只能在疼痛的缝隙间靠近过去。无论什么样的尝试都没关系，因为疼痛终归会被遗忘，可为什么，她渐渐觉得自己身陷囹圄，曾经漫无边际的路越来越窄……这不是唯一的道路吗？她在狭窄的缝隙中仍不死心的向前探出手指，企图在困顿中找到出路。
    `,
  },

  {
    id: "4",
    title: "完美的裂隙",
    content: `
    古老的雕像因拥有独特的材质、精细的制作工艺而显得尤为珍贵和难以获取。

越被珍惜的宝物，越容易走向毁灭，看似意外，又总觉得是拥有者带有自毁倾向的命中注定。

这座雕像从高高的柜子上意外摔下，摔得粉碎，涣散破裂的零件洒了一地。它被创造出的时代早已远去，如今除了她，没有人记得它原本的样貌。她想要把它拼凑回原本无瑕的模样，于是想起来时，就会空出些时间，小心翼翼地粘合每一片碎片。她从内部开始重组，按照回忆中的形象将几万个碎片复归原位，专心拼合的时光让那颗空落落的心变得平静、充实，连带着身边的一切都陷入到美妙和谐的氛围中。当她慢下来时，一切却又残忍地变得更快起来，不知不觉间，她已将这座雕像恢复了大半，它已经可以同往常一样运作、活动，可这还不够，美丽的皮囊也是不可或缺的一部分。她的捏造，她行动的结果，必须遵从她的意愿，达到从内到外的完美。

胜利近在眼前，又仿佛遥不可及。每当她拼好一处，另一处就再一次因为年久脆弱而剥落下来。

裂隙永远存在。

她不停地拼着，一片掉下来就再次拿上去，心里越焦急，越想要靠近最终的结果，零落的碎片就变得更多。

她凝视雕像太久，久到雕像面容上的裂隙开始变成无数个嘲讽的笑容，笑她想要追求完美，实际理想与现实之间的裂隙永远存在，她面对的其实是没有尽头的徒劳。她凝视雕像太久，久到雕像面容上的裂隙开始成为一种提醒，提醒她需要做的还有许多。这种残缺已然成为了雕像的一部分，完美之上的裂隙不知何时，已经变为了完美的裂隙。
    `,
  },

  {
    id: "5",
    title: "彼世与彼岸",
    content: `
     她抬头望向天空，天很蓝，可她记得，天是黑红色的。

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

人们依旧呼喊着她的名字，这一次她一定能够拥抱真正的他们。
    `,
  },
];
const current = ref(0);

function select(index) {
  current.value = index;
  // 将焦点移回当前 tab（便于无障碍与键盘导航）
  const id = `tab-${items[index].id}`;
  const el = document.getElementById(id);
  if (el) el.focus();
}
const storyRef = ref<HTMLElement | null>(null);

function prev() {
  current.value = (current.value - 1 + items.length) % items.length;
  select(current.value);
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}

function next() {
  current.value = (current.value + 1) % items.length;
  select(current.value);
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}

onMounted(() => {
  if (!reducedMotion) {
    const tl = gsap.timeline({ defaults: { ease: "power3.out" } });
    tl.from(".avatar-card", {
      duration: 0.8,
      y: 18,
      opacity: 0,
      scale: 0.99,
    }).from(
      ".right-col > .hero-panel .name",
      { duration: 0.9, x: 24, opacity: 0 },
      "-=0.45"
    );

    gsap.utils.toArray(".tl-item").forEach((it: any, idx: number) => {
      gsap.from(it.querySelector(".tl-card"), {
        scrollTrigger: {
          trigger: it,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
        opacity: 0,
        y: 22,
        duration: 0.7,
        delay: idx * 0.06,
      });
    });

    gsap.to(".avatar-card", {
      scale: 1.007,
      duration: 2.8,
      yoyo: true,
      repeat: -1,
      ease: "sine.inOut",
    });
  } else {
    document.documentElement.classList.add("reduced-motion");
  }
});
</script>

<style scoped lang="scss">
/* 弗洛洛风格 — 暗紫湮灭 + 血玫点光 + 乐谱/音符细节 */
$bg-deep: #06050a; // 极深底色（湮灭）
$deep-2: #120718; // 次深色渐变尾
$accent-1: #b86be0; // 暗紫主光
$accent-2: #ff6b85; // 血玫高光
$text-main: #efe9ec; // 苍白主色
$text-muted: rgba(239, 233, 236, 0.78);
$card-bg: rgba(8, 6, 10, 0.46); // 暗紫玻璃卡片
$card-border: rgba(184, 107, 224, 0.06);
$soft-shadow: rgba(0, 0, 0, 0.6);
$inner-glow: rgba(184, 107, 224, 0.03);
$blood-halo: rgba(255, 107, 133, 0.04);

.megumi-page {
  position: relative;
  min-height: 100vh;
  background: linear-gradient(180deg, $bg-deep 0%, $deep-2 72%);
  font-family: "Noto Sans SC", system-ui, -apple-system, "Segoe UI", Roboto,
    Arial;
  color: $text-main;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: clamp(20px, 4vw, 80px);
  box-sizing: border-box;
  padding-top: 60px;

  &.reduced-motion * {
    transition: none !important;
    animation: none !important;
  }

  .container {
    position: relative;
    z-index: 10;
    max-width: 1100px;
    margin: 0 auto;
    padding: clamp(12px, 2vw, 40px);
  }
}

/* ========== HERO 区域（整体暗化 + 血玫点光） ========== */
.hero {
  position: relative;
  display: grid;
  grid-template-columns: 280px 1fr;
  gap: 28px;
  align-items: start;
  padding: 12px;
  margin-bottom: 12px;

  /* 乐谱画布 —— 在 hero 背后增加五线谱移动感 */
  &::before {
    content: "";
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    background-image: repeating-linear-gradient(
      to bottom,
      rgba(255, 255, 255, 0.02) 0px,
      rgba(255, 255, 255, 0.02) 1px,
      transparent 1px,
      transparent 16px
    );
    opacity: 0.06;
    mix-blend-mode: overlay;
    transform: translateY(-4%);
    animation: staff-drift 16s linear infinite;
  }

  .left-col {
    display: flex;
    flex-direction: column;
    align-items: center;
    z-index: 6;
  }

  .avatar-card {
    background: linear-gradient(
      180deg,
      rgba(255, 255, 255, 0.01),
      rgba(184, 107, 224, 0.01)
    );
    border-radius: 16px;
    padding: 12px;
    display: grid;
    justify-items: center;
    gap: 8px;
    border: 1px solid $card-border;
    box-shadow: 0 14px 44px $soft-shadow, inset 0 1px 0 $inner-glow;
    cursor: pointer;
    transition: transform 0.28s ease, box-shadow 0.28s ease;
    width: 100%;

    /* 单侧血泪装饰（靠近头像一侧） */
    &::after {
      content: "";
      position: absolute;
      right: 18px;
      top: 18px;
      width: 6px;
      height: 18px;
      border-radius: 40% 40% 60% 60%;
      background: linear-gradient(
        180deg,
        rgba($accent-2, 0.98),
        rgba($accent-1, 0.6)
      );
      filter: blur(0.6px);
      opacity: 0.92;
      transform: rotate(8deg);
      box-shadow: 0 4px 14px rgba($accent-2, 0.06);
    }

    &:hover {
      transform: translateY(-6px);
      box-shadow: 0 28px 80px rgba(0, 0, 0, 0.6),
        inset 0 1px 0 rgba($accent-1, 0.045);
    }
  }

  .avatar-wrap {
    width: 100%;
    display: block;
  }

  .avatar {
    width: 100%;
    height: auto;
    border-radius: 12px;
    display: block;
    box-shadow: 0 12px 34px rgba(0, 0, 0, 0.5), 0 0 18px rgba($accent-1, 0.03);
    transition: transform 0.5s ease, filter 0.5s ease;
    background-color: rgba(0, 0, 0, 0.04);
    filter: saturate(0.8) contrast(0.95) brightness(0.92);
  }

  .avatar-card:hover .avatar {
    transform: translateY(-4px) scale(1.02);
    filter: saturate(0.7) contrast(0.9) brightness(0.9);
  }

  .avatar-meta {
    display: flex;
    gap: 8px;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
  }

  .avatar-badge {
    font-size: 0.9rem;
    color: $text-main;
    background: rgba(0, 0, 0, 0.18);
    padding: 6px 10px;
    border-radius: 999px;
    border: 1px solid rgba($accent-2, 0.05);
    font-weight: 600;
  }

  .right-col {
    display: flex;
    flex-direction: column;
    gap: 12px;
    z-index: 6;
  }

  .hero-panel {
    position: relative;
    z-index: 6;
    padding: 20px;
    border-radius: 14px;
    background: $card-bg;
    backdrop-filter: blur(8px) saturate(0.9);
    border: 1px solid $card-border;
    box-shadow: 0 12px 50px rgba(0, 0, 0, 0.6), inset 0 1px 0 $inner-glow;
  }

  .name {
    font-size: clamp(1.8rem, 3vw, 2.8rem);
    margin: 0;
    font-weight: 900;
    letter-spacing: 1px;
    line-height: 1;
    display: inline-block;
    /* 标题改为 暗紫 → 血玫 渐变 */
    background: linear-gradient(90deg, $accent-1 0%, $accent-2 70%);
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    color: transparent;
    -webkit-text-stroke: 0.2px rgba(0, 0, 0, 0.22);
    text-shadow: 0 10px 36px rgba(0, 0, 0, 0.65);
    position: relative;
  }

  .jp {
    font-size: 0.95rem;
    color: $accent-1;
    margin-left: 8px;
    font-weight: 400;
  }

  .subtitle {
    margin: 6px 0 0;
    font-size: 1.1rem;
    color: $accent-1;
    font-weight: 600;
    line-height: 1.4;
  }

  .lead {
    margin: 10px 0 0;
    color: $text-main;
    opacity: 0.96;
    line-height: 1.7;
    max-width: 72ch;
  }
}

/* ========== 通用卡片（更暗、更锐利的边与血色提示） ========== */
.card {
  margin-top: 24px;
  background: rgba(6, 6, 8, 0.5);
  border-radius: 14px;
  padding: 24px;
  box-shadow: 0 12px 48px rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(6px);
  color: $text-main;
  border: 1px solid $card-border;
  transition: transform 0.28s ease, box-shadow 0.28s ease;

  &:hover {
    transform: translateY(-8px) scale(1.01);
    box-shadow: 0 30px 90px rgba(0, 0, 0, 0.7);
  }

  h2 {
    margin: 0 0 12px 0;
    font-size: 1.3rem;
    color: $accent-2;
  }

  .card-head {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 12px;
  }
}

/* personality 列表样式（暗紫细线） */
.personality .traits li {
  padding: 12px 0;
  border-bottom: 1px dashed rgba($accent-1, 0.06);
  font-size: 1rem;
  color: rgba($text-main, 0.92);
}

/* overview 双栏布局（保持） */
.overview .two-col {
  display: flex;
  gap: 24px;
  flex-wrap: wrap;
  margin-top: 12px;
}

.overview .text-block {
  flex: 1 1 40%;
  min-width: 240px;
  line-height: 1.6;
  color: rgba($text-main, 0.96);
}

/* special-section：加入音符漂浮和暗紫边框 */
.special-section {
  position: relative;
  overflow: hidden;

  .content {
    display: flex;
    gap: 24px;
    align-items: center;
    margin-top: 16px;
  }

  .img-area {
    flex: 0 0 200px;

    img {
      width: 100%;
      height: auto;
      border-radius: 12px;
      box-shadow: 0 14px 44px rgba(0, 0, 0, 0.6), 0 0 12px rgba($accent-1, 0.02);
      border: 1px solid rgba($accent-1, 0.03);
    }
  }

  .text-block {
    flex: 1;
    line-height: 1.6;
    color: rgba($text-main, 0.95);
  }
}

/* ll-stories — 暗底 + 微血色分割线 */
.ll-stories {
  .card {
    width: min(1200px, 100%);
    border-radius: 12px;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.6),
      inset 0 1px 0 rgba($accent-1, 0.02);
    color: $text-main;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    background: linear-gradient(
      180deg,
      rgba(8, 10, 12, 0.12),
      rgba(8, 6, 10, 0.06)
    );
  }

  .card-head {
    padding: 14px 16px;
    border-bottom: 1px solid rgba($accent-2, 0.03);
    display: flex;
    flex-direction: column;
    gap: 12px;

    .card-title {
      font-size: 24px;
      font-weight: 700;
      color: $accent-1;
      letter-spacing: 0.6px;
    }
  }

  .card-body {
    padding: 18px 16px;
    min-height: 160px;
    position: relative;
    background: linear-gradient(180deg, rgba(0, 0, 0, 0), rgba(8, 8, 10, 0.02));
  }

  .story {
    color: rgba($text-main, 0.96);

    .story-head {
      font-size: 18px;
      color: $accent-2;
      font-weight: 700;
      margin-bottom: 8px;
    }

    .story-content {
      line-height: 1.7;
      color: rgba($text-main, 0.92);
      white-space: pre-wrap;
    }
  }

  .card-foot {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
    padding: 10px 14px;
    border-top: 1px solid rgba($accent-1, 0.02);
    background: linear-gradient(
      180deg,
      rgba(0, 0, 0, 0.02),
      rgba(0, 0, 0, 0.04)
    );

    .nav-btn {
      background: transparent;
      border: 1px solid rgba($accent-1, 0.06);
      color: $accent-2;
      padding: 6px 10px;
      border-radius: 6px;
      cursor: pointer;
      transition: background 180ms ease;
      &:hover {
        background: rgba($accent-2, 0.04);
      }
    }

    .pager {
      color: rgba($text-main, 0.72);
      font-size: 13px;
      font-weight: 600;
    }
  }

  /* 切换动画保留，但更暗 */
  .fade-slide-enter-active,
  .fade-slide-leave-active {
    transition: all 320ms cubic-bezier(0.2, 0.9, 0.2, 1);
  }

  .fade-slide-enter-from {
    opacity: 0;
    transform: translateY(6px);
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
    transform: translateY(-6px);
  }
}

/* timeline（暗紫竖线 + 微光） */
.timeline {
  position: relative;

  .tl-wrap {
    position: relative;
    padding: 24px 12px 12px 96px;
  }

  .vl {
    position: absolute;
    left: 80px;
    top: 32px;
    bottom: 20px;
    width: 4px;
    border-radius: 6px;
    background: linear-gradient(
      180deg,
      rgba($accent-2, 0.12),
      rgba($accent-1, 0.1)
    );
    box-shadow: 0 6px 18px rgba($accent-1, 0.06),
      inset 0 0 10px rgba($accent-1, 0.02);
    opacity: 0.95;
  }

  .tl-list {
    list-style: none;
    margin: 0;
    padding: 0;
  }

  .tl-item {
    position: relative;
    display: flex;
    gap: 18px;
    align-items: flex-start;
    margin: 20px 0;
    transition: transform 0.28s ease, box-shadow 0.28s ease;
  }

  .tl-card {
    background: rgba(8, 6, 10, 0.36);
    border-radius: 12px;
    padding: 16px 20px;
    box-shadow: 0 12px 36px rgba(0, 0, 0, 0.55);
    width: calc(100% - 130px);
    border-left: 4px solid transparent;
    transition: transform 0.28s ease, box-shadow 0.28s ease,
      border-color 0.28s ease;
    position: relative;
    z-index: 2;
  }

  .tl-item:hover .tl-card,
  .tl-item:focus-within .tl-card {
    transform: translateY(-8px);
    box-shadow: 0 24px 72px rgba(0, 0, 0, 0.6);
    border-left-color: rgba($accent-2, 0.18);
  }

  .tl-title {
    margin: 0 0 6px 0;
    font-size: 1.1rem;
    color: $accent-1;
    display: flex;
    align-items: baseline;
    gap: 8px;
  }

  .tl-desc {
    margin: 0;
    color: rgba($text-main, 0.92);
    white-space: pre-wrap;
  }
}

/* 浮动音符（可在模板中渲染若干 .floating-note） */
.floating-note {
  position: absolute;
  font-size: 14px;
  color: $accent-2;
  opacity: 0.95;
  transform-origin: center;
  animation: note-float 4.6s ease-in-out infinite;
  filter: drop-shadow(0 6px 18px rgba($accent-2, 0.06));
}

/* 动画关键帧 */
@keyframes staff-drift {
  0% {
    transform: translateY(-4%);
    opacity: 0.9;
  }
  50% {
    transform: translateY(6%);
    opacity: 0.95;
  }
  100% {
    transform: translateY(-4%);
    opacity: 0.9;
  }
}

@keyframes note-float {
  0% {
    transform: translateY(0) rotate(-6deg) scale(0.95);
    opacity: 0.85;
  }
  50% {
    transform: translateY(-12px) rotate(3deg) scale(1);
    opacity: 1;
  }
  100% {
    transform: translateY(0) rotate(-6deg) scale(0.95);
    opacity: 0.85;
  }
}

/* 响应式：小屏适配（保留原逻辑，仅小幅调整文字色调与阴影） */
@media (max-width: 980px) {
  .hero {
    grid-template-columns: 1fr;
    gap: 18px;
    padding: 8px;
  }

  .avatar-card {
    width: 140px;
    padding: 8px;
  }

  .hero-panel {
    padding: 14px;
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

  .card {
    padding: 18px;
    border-radius: 12px;
  }

  .overview .two-col {
    flex-direction: column;
  }

  .special-section .content {
    flex-direction: column;
  }

  .ll-stories {
    .card {
      border-radius: 10px;
    }

    .card-head {
      padding: 12px 12px;
    }

    .card-body {
      padding: 14px 12px;
      min-height: 180px;
    }

    .card-foot {
      padding: 8px 12px;
    }
  }

  .timeline {
    overflow: visible;
  }

  .timeline .tl-wrap {
    padding-left: 14px !important;
    padding-right: 14px;
    min-height: 0;
  }

  .timeline .vl {
    display: none !important;
  }

  .timeline .tl-list {
    overflow: visible;
  }

  .timeline .tl-item {
    display: block !important;
    margin: 14px 0 !important;
    width: 100%;
  }

  .timeline .tl-card {
    width: 100% !important;
    padding: 14px !important;
    border-left: 0 !important;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.45);
    transform: none !important;
  }
}
</style>
