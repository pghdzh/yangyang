<template>
  <div class="megumi-page" aria-live="polite">
    <main class="container" ref="mainContainer">
      <!-- HERO 区域 -->
      <header class="hero" role="banner">
        <div class="left-col">
          <button class="avatar-card">
            <picture class="avatar-wrap">
              <img
                src="@/assets/images1/yangyang-slty (16).webp"
                class="avatar"
                loading="lazy"
                decoding="async"
              />
            </picture>
            <div class="avatar-meta">
              <span class="avatar-badge">共鸣能力：风声流息</span>
            </div>
          </button>
        </div>
        <div class="right-col" ref="metaCard">
          <div class="hero-panel">
            <h1 class="name">
              秧秧
              <span class="jp">（夜归下设临时调查员）</span>
            </h1>
            <p class="subtitle">
              无论一切怎样变化，我都会陪在你身边。只要你需要，我就一直在。
            </p>
            <p class="lead">
              雀鸟迎风而行、飞向远方，受家境庇护顺遂成长的少女，如今正试图成为保护他人的力量。
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
            【流响】：血量上方上限3点的特殊能量条。当【流响】充满时，空中A消耗所有【流响】，发动连续攻击。
          </li>
          <li>【流响】获取方式：第4段A命中，Z命中，E命中，变奏命中。</li>

          <li style="font-weight: bold">输出流程：</li>
          <li>基础流程：AAA-EZ-A-R-Q-跳-A</li>
          <li>
            技能缩写：普攻 = A，重击 = Z，共鸣技能 = E，共鸣解放 = R，声骸 = Q
          </li>
        </ul>
      </section>

      <section id="resonance-status" class="card overview">
        <div class="card-head">
          <h2>共鸣状况</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <p>
              无明确共鸣时长和明确触发事件，主诉自幼对周遭环境表现出模糊的感应性。<br />
              对象声痕位置于额头中央，共鸣后原无明显体征变化，但随频率波动反噬，主诉的发梢和发尾出现局部的羽毛化表现。<br />
              对象能与周遭涌动的自然气流感应,具体表现为可感知暴露于气流中的信息、可汇聚气流形成有能量的场域。<br />
              共鸣频率共性较强，频谱检测图与已知气流、小型雀鸟的频率波动区间均有超过20%的相似率，测试有较强的谐振反应，但同样与多个相已知的频谱检测图存在5%左右的相似率，推测为非单一共鸣源或共鸣源本身具有强发散性，无法完全确认共鸣源。<br />
              受试样本拉贝尔曲线呈稳定上升态，最终趋向稳定波动，检测结果判断为自然型共鸣者。<br />
            </p>
          </div>
        </div>
      </section>

      <!-- 新增三大主题区域 -->
      <section id="food" class="card special-section">
        <div class="card-head">
          <h2>风纹发带</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item1.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              做工精致的发带，总是被秧秧戴在头上，连同那顶蓝色的帽子一起，成为了她的某种标志物。<br />
              离家远行前，母亲将这条发带作为礼物送给秧秧。对秧秧而言，它代表了母亲对自己的临别祝福，也象征着一段旅途的开始，她会一直记得自己第一次佩戴时，所下定的那份决心。
            </p>
          </div>
        </div>
      </section>
      <section id="shattered-crystal" class="card special-section">
        <div class="card-head">
          <h2>留声装置</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item2.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              流息无时不在，却又转瞬即逝，习惯了聆听这种讲述，秧秧偶尔也会有将“风声”存储下来的想法。<br />
              由白芷改良的这款留声装置，搭载了最新的能量转化技术，能够将秧秧感知到的流息，存储为可视可感的信息频谱。<br />
              某场雪落山间的轻响，某个不被人知晓的故事，某刻叫人落泪的瞬间……秧秧用这种方式，记录下自己曾感知到的真实。
            </p>
          </div>
        </div>
      </section>

      <section id="cyclic-clock" class="card special-section">
        <div class="card-head">
          <h2>万物弥声</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item3.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              秧秧自童年起制作至今的风铃，拨片是打磨成鸟雀形状的晶石玉料。<br />
              一开始只有寥寥几片，但随秧秧去过的地方越来越多，自己收集或是伙伴所赠，如今已有数十片之多，长度和复杂度也是最初的好几倍。<br />
              有限的基石不断延续，构成了可能性无限的演绎。随新的拨片不断加入，秧秧也不断调整着原有的位置和编排，最后风铃所奏究竟会是怎样一曲，秧秧不得而知，但砥砺初心、方得始终，家学所说的“一生一器”不外乎如此。
            </p>
          </div>
        </div>
      </section>

      <section id="collective-album" class="card special-section">
        <div class="card-head">
          <h2>蓬蓬烘蛋</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/food.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              经由秧秧改良的潮饼，搅打均匀的蛋白替换了部分蛋黄用量，搭配莓果果酱，口感蓬松绵软的同时，也更符合营养均衡的宗旨。
              这样一来，无论是上了年纪的老人还是尚未开蒙的孩童，应该……都能更好地享用这份美味。
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
            <h2 id="stories-title" class="card-title">秧秧纪事</h2>
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

<script setup lang="ts">
import { ref, onMounted, nextTick } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

const timeline = ref([
  {
    title: "底色湛蓝如洗",
    desc: ` 施放变奏技能·湛蓝礼赞后，秧秧的气动伤害加成额外提升15%，效果持续8秒。`,
  },
  {
    title: "雀鸟衔枝而行",
    desc: `重击命中目标时，秧秧额外回复10点共鸣能量，每20秒可触发1次。`,
  },
  {
    title: "流息声声不绝",
    desc: `共鸣技能伤害加成提升40%，风场对周围目标的牵引效果增强，牵引范围增加33%。`,
  },
  {
    title: "若可侧耳倾听",
    desc: `空中攻击释羽的伤害提升95%。`,
  },
  {
    title: "绪风于此响彻",
    desc: `共鸣解放朔风旋涌的伤害提升85%。`,
  },
  {
    title: "致美好以颂歌",
    desc: `施放空中攻击释羽后，队伍中的角色的攻击提升20%，效果持续20秒。`,
  },
]);

const reducedMotion =
  window.matchMedia &&
  window.matchMedia("(prefers-reduced-motion: reduce)").matches;

const items = [
  {
    id: "1",
    title: "优胜的秘法",
    content: `相处中，总会有这种人的吧，虽然还没开始或者从没做过，但如果是她的话，就觉得一定能做得到。

秧秧就是会给人这种感觉的人。

“秧秧她……似乎什么都能做好“，这份笃定的信任，大概来自于过往的实例。

自小成绩优异，礼、乐、书、数、武……各科教考每年总是名列前茅，如遇上新的知识点或难题，也是不露惧色，积极尝试。

一年一度的学院评优，秧秧总会是上台发言的代表之一。

但大家所不知的是，秧秧在发言之前，也会按着虎口小小吸气平复紧张的心情，每一次开始或尝试，她都不是抱有完全的把握的。

要说感悟事理，秧秧的确算有些天赋，但那点天赋在真正的天才面前不过尔尔，常人会遇到的问题她也都没法幸免。

第一次修行剑道，她因收不住挥剑的力气险些栽倒，更别提完整地舞出一套剑招。

可越是还不够好，越是要尽力做好。自那之后的两个月里，她每日练习一百下，不断请教更为优秀的前辈师长，毫不避讳地展现自己的不足，再以一种近乎自省的态度，继续每日一百下练习，完善他人指出的不足。如此这般，肌肉适应了酸痛，她已能和人打上几个来回。

而再两个月后，在教考剑道的考场，秧秧便又一次拔得头筹了。

也有人不忍见秧秧如此辛苦，劝慰她道，“人生嘛，哪有那么多的完满，大家的肯定和称赞也已足够，不做到那么好也是可以的吧？”

这种时候，秧秧总是先笑着谢过对方的好意，再摇摇头以作拒绝。

不行呐，少女所求并不是他人的嘉奖，她不是要与旁人争个高低，而是近乎执拗地和自己较着劲。

是不是真的竭尽全力？是不是每个环节都做到了做到了当下范围内的最好，没留下任何遗憾、无愧于心呢？

“不愧是秧秧，果然啊……就知道你没问题！”

在秧秧成为踏白，查出并帮着解决残象区域性聚集异常后，人们一如既往给予了她这样的评价。

这一次的确如此，但是下一次……需要努力的地方还有很多，没能掌握的东西也还有很多，再去请教下夜归的前辈们，该如何做得更好吧？

想成为被笃定信任着的人，要付出难以想象的努力。

不过，既然是秧秧认定的事情，无论会多辛苦，她都会不遗余力。`,
  },
  {
    id: "2",
    title: "倾听的秘法",
    content: `大多数人不知道的是，秧秧有着绝对音感。

这或许是因为秧秧从未主动在人前演唱过，又或许是因为大部分时候，她都退居角落，将舞台让给他人，似乎她生来就只适合担当倾听的角色。

所以当秧秧精准地哼唱出只听过一遍的旋律时，炽霞惊讶地瞪大了眼睛，“这，这么快就……而且，还唱得这么好听！”

恍然间，大家这才想起秧秧的另一重身份，声乐世家的二小姐，不通乐理、不懂歌唱什么的才说不过去吧。

秧秧的这一身份，让原先的部分困惑合理了起来，那恰到好处的待人处事，那再严苛的人挑不出错误的礼节，原是这么个渊源。但随即，更大的困惑涌上众人心头，世家的小姐放弃原有的优渥安稳、只身来到边境，是有什么非这样不可的缘由吗？

偏偏从没听秧秧提起过，踏白出勤还是日常相处，她没推脱过什么，也没抱怨过辛苦。

真要说起来，反倒是他们麻烦她更多，因为……她总是在听他们说。

有时人们不断讲述，所求并不是对错或者解法，只是想找个方法将郁结的情绪宣泄出去。秧秧深知这点，所以并不预设立场，而是怀着平常心，理解每一位讲述者当下的感受和需要。对方诉说，她并不打断，只在卡壳或为难时出声提示一二，然后又继续到静静聆听的状态。于是乎，那些原本理不顺、掰不清的情绪不知不觉就豁然开朗，一次倾诉真正发挥了功效。

当然，倾听绝不是一味的附和，如需建议，秧秧也从不吝啬，但她给出建议的首要原则，便是以当事人意愿为先。

炽霞的一番宣传，让众人好奇不已，都打趣秧秧什么时候给大家唱上一首。

秧秧却很是不好意思地摆摆手拒绝，“还是……等之后准备好的时候再唱给你们听吧？”

秧秧神情恳切，想来并不是敷衍或托词，而是发自内心真的这么想。只是，那是什么时候呢，恐怕……是要等所有想要被聆听之人统统说完吧？

能听懂他人演绎并给出建议的，自身也一定是通晓乐理之人。

但自身精通，未必就要登台献艺，能聆听也是好差事啊，少女这样想着又退后两步，将舞台让给了她觉得真正需要的人。`,
  },

  {
    id: "3",
    title: "相信的秘法",
    content: `从某种程度来说，十六岁的秧秧和六岁时的秧秧相比，并没有太大的差异。

她还是对世界抱有同样的期许，一如既往地相信童话，相信人与人之间的善意，相信奇迹，相信一切美好终会发生。

有人说，这种侥幸的天真，是没吃过生活苦头的宠儿才会有的想法。

的确，秧秧的成长没多少苦难的参与，优渥的家境、疼爱她的家人，她天然就被隔绝在相对幸运的那一边，顺遂地长大。

但并不是没体会过。

十一岁那年，名为现实的暴雨，顺着偶然撕裂的缝隙，开始斜进她世界的一角。

蛰伏已久的流放者袭击了家中的商队，秧秧还没来得及消化死亡、人类对待同胞的残忍，就开始了逃窜。她流落到某个不知名的村庄聚落，聚落贫瘠，靠自己的双手在野外活下来的人瘦削精干，惊魂未定间，秧秧分不清他们与那群穷凶极恶的歹徒是不是同类。对面同样警惕又戒备，他们用试探的眼神紧盯衣着华丽又独自一人的秧秧，疑心这是一个陷阱。

但最终她还是接过了他们递来的、她从没见过的食物。

最贫瘠的地方也滋生着善意，秧秧想回报他们率先给出的信任，但她的学识在野外派不上用场，她的剑法在真正的敌人面前又太过稚嫩。她与周边的一切如此格格不入，偏偏这一切或许才是大多数人需要面对的真实，而这真实如此苦涩，看不见尽头。

“阿婆，你们……一直这样生活嘛？”秧秧帮聚落里的阿婆一起清点着并不宽裕的物资，忍不住问出这个略显突兀的问题。

“咋啦，城里来的小丫头，被吓到了？放宽心，等到以后啊，就好咯。”

“那要到多久以后呢？真会有那样的以后吗？”

“当然有，要是你自己都不相信，那还怎么成真？就像那时候，我们也不敢保证你不是坏人嘛。人呀，总要试着先去相信些什么，知道结果了才信，那叫个屁的相信啊！”

“至于多久……喏，就你们长大以后吧。“

所以，或许秧秧行至此刻的生命中还没有经历足够的苦难，但又或许，期许与否并不取决于一个人的经历，而是一个人的本性。

就是会有人在最贫瘠的时候，因一份近乎于期许的相信，释放善意，而他们的身体力行，又催生出一份新的期许。

从那之后，面对陌生未知的人和事，秧秧会率先一步给出信任。

当然，她的交付不是毫无底线，一旦发现对方辜负了这份信任，和煦的轻风也会化为划破长空的利刃。

只是相信的心是一切的开始，先抱有美好的期许，再设法使这样的期许成真，这正是使世界不再变糟的万千方式中，她所选定的那一种。`,
  },

  {
    id: "4",
    title: "风教会她的",
    content: `秧秧曾三次于风中坠落。

第一次，是从母亲悠荡起的怀抱里。

彼时秧秧刚诞生于人世不久，被爱意包围，母亲将小小的她放在臂弯间晃荡，玩着假装腾起又假装落地的游戏。一次更为用力的晃荡，母亲一时不察，竟真的让她短暂腾空。

在通晓人言之前，秧秧先一步听懂了风的秘语，不过半臂距离的下落，她竟于无形中察觉到暴雨将至的潮湿。

秧秧咿咿呀呀，众人皆当她是因刚刚的意外受了惊吓，却无人想到，其实那是因为片刻后来临的一场风雨。

第二次，是因为过载而陷入昏厥。

随年岁渐长，原先那种朦胧的感应，已经变成一种可被感知的精准传达。无形的气流便在秧秧她掌间汇聚，被她称做“流息”，大到能量的波动、异常的先兆，小到一个人的服饰、肢体动作、甚至语气中的沉默，凡是暴露在风场中，她都能有所感知。

但也是因此，秧秧陷入了一种误区，她不愿辜负这种能力，总想着多感知一些……但秧秧越是想把一切纳入考量，就越迷失在茫茫细节中。

将后来发生的事归为坠落，也许并不准确，因为衡量它的维度不是距离，而是时间。

由过载而带来的负荷，断断续续地持续了数个月之久，浑浑噩噩间，秧秧几乎以为自己只是是被风挟裹的一片落羽，要永无止尽地向下坠去，直到——“面面俱到，面面皆失，我真正该做的……是在限度之内找准真正的关键“，如同顿悟一般，少女为自己划下了停歇的落点。

第三次，但或许不是最后一次，是秧秧主动跃起，拥风入怀。

当过载在发间留下不可逆转的印记，秧秧也彻底摆脱了过载的负荷。

流息依旧反映着冗杂的细节，但感知者却学会集中于所需的一点，筛选出真正有用的信息。

失踪处挣扎的痕迹，城郊外异常的波动，持续增强的能量反应……秧秧从没在如此急迫的情况下感知过，但她决意先不管结果的好坏，只纯粹地采取想要采取的行动。 流息涌动，逐渐汇聚成明确唯一的线，而那线所指向的尽头，正是失踪孩童的身影——被残象紧逼，她惶惶然就要跌落山崖，顾不得思索，秧秧跃向崖底，朝她伸出了手。

雀羽飘荡，气流飘荡的轨迹构成风场，轻柔却有力地托住了坠落的少女和孩童。

孩童看着四散的羽毛，惊喜地拍着小手，“姐姐，你是会在风中飞飞的雀鸟仙人吗？”

秧秧怔愣，在想起那些与风交缠的过往后浅笑，大概……真是如此吧。`,
  },

  {
    id: "5",
    title: "答案在风中",
    content: `离家远行的路上，秧秧曾见过这么两类人。

一类行色匆匆，却不知道自己为什么奔忙，最终迷失在路途之中，悻悻而返。而另一类，他们目标明确，哪怕偶有停歇、绕了远路，也总能抵达终点。秧秧望了望不远处依稀可见的今州城，不确定自己可否算作第二类，但这两类人的存在让她不置可否，又一次想起那个叫做“一生一器”的词。

一生一器，是秧秧家学中的最重要一条，本意为家中成员尽可以无所不长，但需择定一种乐器为专，终其一生不断修练，成为此间大拿。后随事局变化，渐渐衍生为一生只为一事，砥砺至极致，以求不枉此生。

母亲的“器”，应该就是制曲，秧秧见过大家在母亲的曲子中欢兴起舞，而姐姐，也已经能在商会独当一面了。

但秧秧却无法像她们那样轻易地做出决定，什么样的事才值得献上一生？她到底该孩成为什么样的人？

她向母亲求助，您希望我继承家业吗？或者说，您希望我做什么呢？我什么都可以一试，也一定会将尝试做到极致，人生虽有无限可能，但终究也只能选择其中一条吧？

母亲慈爱地看向她，孩子，正因如此，你才不该从我这寻找答案，而是该问一问自己的心，这本就是很难的问题，你不必着急。

于是，秧秧将这个问题放在心里，积压了很久很久。

久到她从贫瘠聚落重新返回家中，从永无止尽坠落再度爬了回来，久到她终于厌倦了家境的庇佑，想要走入她曾见过的其他人群里，如他们一样感悟世界的坎坷。她不想再只是轻薄一片，她想要变得充满份量，想要真实将她侵染。

野外突然袭击，前线因残象损失惨重的消息传回城里，哀戚、愤恨、绝望……情绪在聚集的人群中翻涌，仿佛下一秒就将滚沸。大家就着篝火，不止是谁先起的头，低沉的男声、沙哑的女声，大家抽噎着哭得厉害，但在第二日清晨就恢复如初了，悲伤变得奢侈，而秧秧没有具体的失去，她因他们的哭泣而流泪。

他们悲恸的泪光与被救下的孩童的笑容交相闪过，所有经历种种融会贯通，终于让她意识到自己想要成为怎样的人——她想要保护他人，如同曾被保护的自己一样，让他们免受不幸，让他们既可以放声哭泣，也可以期许美好。

授资加入踏白的那个夜晚，秧秧独自来到今州城的最边缘，她仰望天空，也俯瞰生活着的居民，她询问自己，这城市如此鲜活，自己是否真能如自己所想，拥有保护他们的力量？

流息从远方涌来，将少女轻柔地拥入怀中，拂去她最后一丝疑虑。

它告诉她，今州城，是世家二小姐秧秧的终点，但却只是踏白成员秧秧的起点。

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
interface Chibi {
  src: string;
  top: number;
  left: number;
}

const chibiList = ref<Chibi[]>([]);
onMounted(async () => {
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

  // 1. 基础配置信息
  const total = 10;
  let pickCount = 3; // 每次抽取 3 张
  const vw = window.innerWidth;
  const vh = window.innerHeight;
  const isMobile = window.innerWidth <= 768;
  // 如果已知单张小人图片的宽高，可避免超出边界；
  // 假设小人图片宽 100px、高 100px，按需替换：
  const imgWidth = 100;
  const imgHeight = 100;

  // 2. Fisher–Yates 洗牌函数
  function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  // 3. 随机选出 3 个编号
  if (isMobile) {
    pickCount = 1;
  }
  const nums = shuffle(Array.from({ length: total }, (_, k) => k + 1));
  const picks = nums.slice(0, pickCount);

  // 4. 生成随机位置并填充 chibiList
  chibiList.value = []; // 先清空
  picks.forEach((i) => {
    chibiList.value.push({
      src: `/QImages/1 (${i}).png`,
      left: Math.random() * (vw - imgWidth), // 保证不超出左右边界
      top: Math.random() * (vh - imgHeight), // 保证不超出上下边界
    });
  });

  // 2. 等 img 渲染到 DOM
  await nextTick();

  // 3. 给每个小人绑定 GSAP 动画
  const imgs = document.querySelectorAll<HTMLImageElement>(".chibi-img");
  imgs.forEach((img, index) => {
    const padding = 200; // 边缘预留空间
    // ✅ 初始出场动画（闪现）
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

    // ✅ 鼠标靠近闪避
    img.addEventListener("mouseenter", () => {
      gsap.killTweensOf(img);

      gsap.to(img, {
        x: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
        y: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
        duration: 1.2,
        ease: "back.out(2)",
        onComplete: () => {
          // 闪避完成后，再重新启用动画
          animate(img);
        },
      });
    });

    const animate = (img: HTMLImageElement) => {
      let { x, y } = img.getBoundingClientRect();
      let deltaX = (Math.random() - 0.5) * 200;
      let deltaY = (Math.random() - 0.5) * 200;

      // 预测一下偏移后的位置
      let nextX = x + deltaX;
      let nextY = y + deltaY;

      // 校正：防漂出左、右、上、下边界
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
</script>

<style scoped lang="scss">
/* 秧秧风格 —— 紫蓝海感（水母/幻海） + 毒药/治愈双重高光 */
$bg-deep: #031127;
$deep-2: #07162a;
$accent-1: #2e88c9;
$accent-2: #7fd3ff;

$text-main: #f3fbff; // 文字

$card-bg: rgba(6, 8, 12, 0.46);
$card-border: rgba(184, 107, 224, 0.06);
$soft-shadow: rgba(0, 0, 0, 0.6);
$inner-glow: rgba(184, 107, 224, 0.03);
/* 固定浮动小 chibi 层（保留） */
.floating-chibis {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 1;
  .chibi-img {
    position: absolute;
    width: 80px;
    user-select: none;
    transform-origin: center center;
    pointer-events: auto;
    z-index: 10;
  }
}

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
