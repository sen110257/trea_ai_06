<template>
  <div class="app-container">
    <!-- 顶部浪漫主标题区 -->
    <header class="header-section">
      <div class="decoration-top">
        <span class="heart heart-1">♥</span>
        <span class="heart heart-2">♥</span>
        <span class="heart heart-3">♥</span>
      </div>
      <h1 class="main-title">💗 我们的爱情日记 💗</h1>
      <p class="subtitle">每一刻与你共度，都是最珍贵的时光</p>
    </header>

    <!-- 情侣头像合照展示区 -->
    <section class="avatar-section section">
      <div class="avatar-container">
        <div class="avatar-item avatar-left">
          <img :src="coupleAvatar.left" alt="女方头像" class="avatar-img" />
          <span class="avatar-name">{{ coupleName.left }}</span>
        </div>
        <div class="heart-icon pulse">💕</div>
        <div class="avatar-item avatar-right">
          <img :src="coupleAvatar.right" alt="男方头像" class="avatar-img" />
          <span class="avatar-name">{{ coupleName.right }}</span>
        </div>
      </div>
    </section>

    <!-- 恋爱总天数统计 -->
    <section class="days-section section">
      <div class="card days-card fade-in-up">
        <h2 class="section-title">💑 恋爱时长</h2>
        <div class="days-display">
          <div class="days-number">{{ totalDays }}</div>
          <div class="days-label">天</div>
        </div>
        <p class="days-desc">从 {{ formatDate(startDate) }} 开始的美好时光</p>
      </div>
    </section>

    <!-- 时分秒实时倒计时模块 -->
    <section class="countdown-section section">
      <div class="card countdown-card fade-in-up">
        <h2 class="section-title">⏰ 距离下一个纪念日</h2>
        <div class="countdown-display">
          <div class="countdown-item">
            <div class="countdown-number">{{ countdown.days }}</div>
            <div class="countdown-label">天</div>
          </div>
          <div class="countdown-separator">:</div>
          <div class="countdown-item">
            <div class="countdown-number">{{ countdown.hours }}</div>
            <div class="countdown-label">时</div>
          </div>
          <div class="countdown-separator">:</div>
          <div class="countdown-item">
            <div class="countdown-number">{{ countdown.minutes }}</div>
            <div class="countdown-label">分</div>
          </div>
          <div class="countdown-separator">:</div>
          <div class="countdown-item">
            <div class="countdown-number">{{ countdown.seconds }}</div>
            <div class="countdown-label">秒</div>
          </div>
        </div>
        <p class="countdown-next">下一个纪念日: {{ nextAnniversary }}</p>
      </div>
    </section>

    <!-- 多节点重要纪念日清单 -->
    <section class="anniversary-section section">
      <div class="card fade-in-up">
        <h2 class="section-title">📅 重要纪念日</h2>
        <div class="anniversary-list">
          <div 
            v-for="(item, index) in anniversaryList" 
            :key="index" 
            class="anniversary-item"
          >
            <div class="anniversary-icon">{{ item.icon }}</div>
            <div class="anniversary-info">
              <div class="anniversary-name">{{ item.name }}</div>
              <div class="anniversary-date">{{ formatDate(item.date) }}</div>
            </div>
            <div class="anniversary-count">
              <div class="count-number">{{ item.daysUntil }}</div>
              <div class="count-label">{{ item.daysUntil > 0 ? '天后' : item.daysUntil < 0 ? '已过' : '今天' }}</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 甜蜜情话语录轮播 -->
    <section class="quotes-section section">
      <div class="card quotes-card fade-in-up">
        <h2 class="section-title">💌 甜蜜语录</h2>
        <div class="quotes-container">
          <div class="quote-item fade-in-up" :key="currentQuoteIndex">
            <div class="quote-icon">"</div>
            <p class="quote-text">{{ currentQuote }}</p>
            <div class="quote-author">—— 致最爱的你</div>
          </div>
        </div>
        <div class="quotes-dots">
          <span 
            v-for="(_, index) in sweetQuotes" 
            :key="index"
            class="dot"
            :class="{ active: index === currentQuoteIndex }"
            @click="currentQuoteIndex = index"
          ></span>
        </div>
      </div>
    </section>

    <!-- 情侣相册展示区 -->
    <section class="album-section section">
      <div class="card fade-in-up">
        <h2 class="section-title">📸 甜蜜相册</h2>
        <div class="album-grid">
          <div 
            v-for="(photo, index) in albumPhotos" 
            :key="index" 
            class="album-item"
          >
            <img :src="photo.url" :alt="photo.alt" class="album-img" />
            <div class="album-overlay">
              <span class="album-desc">{{ photo.description }}</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 十二星座运势 -->
    <section class="zodiac-section section">
      <div class="card fade-in-up">
        <h2 class="section-title">✨ 十二星座运势</h2>
        
        <!-- 星座选择 -->
        <div class="zodiac-selector">
          <div 
            v-for="(zodiac, index) in zodiacSigns" 
            :key="index"
            class="zodiac-item"
            :class="{ active: selectedZodiac === zodiac.name }"
            @click="selectZodiac(zodiac.name)"
          >
            <span class="zodiac-symbol">{{ zodiac.symbol }}</span>
            <span class="zodiac-name">{{ zodiac.name }}</span>
          </div>
        </div>

        <!-- 运势时段切换 -->
        <div class="fortune-period">
          <button 
            v-for="period in fortunePeriods" 
            :key="period"
            class="period-btn"
            :class="{ active: selectedPeriod === period }"
            @click="selectedPeriod = period"
          >
            {{ period }}
          </button>
        </div>

        <!-- 运势内容 -->
        <div class="fortune-content" v-if="currentZodiac">
          <div class="fortune-header">
            <span class="fortune-symbol">{{ currentZodiac.symbol }}</span>
            <div class="fortune-info">
              <h3 class="fortune-name">{{ currentZodiac.name }}</h3>
              <p class="fortune-date">{{ currentZodiac.dateRange }}</p>
            </div>
          </div>

          <!-- 四大维度运势 -->
          <div class="fortune-dimensions">
            <div class="dimension-item" v-for="(dimension, key) in currentFortune.dimensions" :key="key">
              <div class="dimension-header">
                <span class="dimension-icon">{{ dimension.icon }}</span>
                <span class="dimension-name">{{ dimension.name }}</span>
              </div>
              <div class="dimension-bar">
                <div 
                  class="dimension-progress" 
                  :style="{ width: dimension.score + '%' }"
                ></div>
              </div>
              <p class="dimension-desc">{{ dimension.desc }}</p>
            </div>
          </div>

          <!-- 整体运势 -->
          <div class="fortune-overall">
            <h4 class="overall-title">💫 整体运势</h4>
            <p class="overall-desc">{{ currentFortune.overall }}</p>
          </div>

          <!-- 性格解析 -->
          <div class="personality-section">
            <h4 class="personality-title">🎭 性格解析</h4>
            
            <div class="personality-traits">
              <div class="trait-item">
                <span class="trait-label positive">优点</span>
                <p class="trait-desc">{{ currentZodiac.personality.strengths }}</p>
              </div>
              <div class="trait-item">
                <span class="trait-label negative">缺点</span>
                <p class="trait-desc">{{ currentZodiac.personality.weaknesses }}</p>
              </div>
            </div>

            <div class="trait-item full">
              <span class="trait-label">处事风格</span>
              <p class="trait-desc">{{ currentZodiac.personality.style }}</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 底部暖心签名文案区域 -->
    <footer class="footer-section">
      <div class="signature-card">
        <div class="signature-decoration">
          <span class="deco-line left"></span>
          <span class="deco-heart">💖</span>
          <span class="deco-line right"></span>
        </div>
        <p class="signature-text">{{ signatureText }}</p>
        <p class="signature-author">—— 永远爱你的人</p>
        <div class="signature-date">{{ formatDate(new Date()) }}</div>
      </div>
      <div class="footer-bottom">
        <p class="footer-text">愿我们的爱情，如同星辰般永恒 ✨</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// 情侣头像和名字
const coupleAvatar = ref({
  left: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cute%20anime%20girl%20avatar%20pink%20hair%20romantic&image_size=square',
  right: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cute%20anime%20boy%20avatar%20brown%20hair%20romantic&image_size=square'
})

const coupleName = ref({
  left: '小仙女',
  right: '大暖男'
})

// 恋爱开始日期
const startDate = ref(new Date('2023-02-14'))

// 计算总天数
const totalDays = computed(() => {
  const now = new Date()
  const diff = now - startDate.value
  return Math.floor(diff / (1000 * 60 * 60 * 24))
})

// 纪念日列表
const anniversaryList = ref([
  { name: '相识日', date: new Date('2023-01-01'), icon: '🌸' },
  { name: '表白日', date: new Date('2023-02-14'), icon: '💐' },
  { name: '女方生日', date: new Date('2000-06-15'), icon: '🎂' },
  { name: '男方生日', date: new Date('1999-11-20'), icon: '🎈' },
  { name: '恋爱纪念日', date: new Date('2023-02-14'), icon: '💝' }
])

// 计算每个纪念日的天数
const updateAnniversaryDays = () => {
  const now = new Date()
  anniversaryList.value.forEach(item => {
    const anniversaryThisYear = new Date(now.getFullYear(), item.date.getMonth(), item.date.getDate())
    if (anniversaryThisYear < now) {
      anniversaryThisYear.setFullYear(now.getFullYear() + 1)
    }
    const diff = anniversaryThisYear - now
    item.daysUntil = Math.floor(diff / (1000 * 60 * 60 * 24))
  })
}

// 下一个纪念日
const nextAnniversary = computed(() => {
  const sorted = [...anniversaryList.value].filter(a => a.daysUntil >= 0).sort((a, b) => a.daysUntil - b.daysUntil)
  return sorted.length > 0 ? sorted[0].name : anniversaryList.value[0].name
})

// 倒计时
const countdown = ref({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0
})

const updateCountdown = () => {
  const now = new Date()
  const targetDate = new Date(now.getFullYear(), startDate.value.getMonth(), startDate.value.getDate())
  if (targetDate <= now) {
    targetDate.setFullYear(now.getFullYear() + 1)
  }
  
  const diff = targetDate - now
  countdown.value = {
    days: Math.floor(diff / (1000 * 60 * 60 * 24)),
    hours: Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)),
    minutes: Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60)),
    seconds: Math.floor((diff % (1000 * 60)) / 1000)
  }
}

// 甜蜜语录
const sweetQuotes = ref([
  "遇见你是我这辈子最美丽的意外，感谢上天让我遇到了你。",
  "我想和你一起慢慢变老，直到我们老得哪儿也去不了，我还依然把你当成手心里的宝。",
  "你的笑容是我见过最美的风景，你的声音是我听过最动听的旋律。",
  "我爱你，不是因为你是谁，而是因为和你在一起时，我变成了最好的自己。",
  "每一天有你的陪伴，都是我生命中最珍贵的礼物。",
  "我愿意用一生的时间，来证明我对你的爱有多深。",
  "你是我心中最柔软的角落，任何人都无法替代。",
  "和你在一起的每一分每一秒，都是我最幸福的时光。"
])

const currentQuoteIndex = ref(0)
const currentQuote = computed(() => sweetQuotes.value[currentQuoteIndex.value])

// 语录轮播定时器
let quoteInterval = null

// 相册照片
const albumPhotos = ref([
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=romantic%20couple%20walking%20on%20beach%20sunset%20love&image_size=square', 
    alt: '海边散步', 
    description: '海边的浪漫日落' 
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20having%20dinner%20candlelight%20romantic&image_size=square', 
    alt: '烛光晚餐', 
    description: '第一次烛光晚餐' 
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20traveling%20mountains%20beautiful%20scenery&image_size=square', 
    alt: '旅行合照', 
    description: '一起看过的风景' 
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20holding%20hands%20coffee%20shop%20cozy&image_size=square', 
    alt: '咖啡店', 
    description: '温馨的午后时光' 
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20celebrating%20birthday%20cake%20happy&image_size=square', 
    alt: '生日庆祝', 
    description: '陪你度过的生日' 
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20watching%20stars%20night%20sky%20romantic&image_size=square', 
    alt: '看星星', 
    description: '一起看星空的夜晚' 
  }
])

// 十二星座数据
const zodiacSigns = ref([
  {
    name: '白羊座',
    symbol: '♈',
    dateRange: '3.21 - 4.19',
    element: '火象星座',
    personality: {
      strengths: '热情、勇敢、自信、直率、乐观、有领导力',
      weaknesses: '冲动、急躁、自我、粗心、缺乏耐心',
      style: '白羊座的人做事雷厉风行，喜欢直接了当，不喜欢拖泥带水。他们充满活力，勇于挑战，是天生的领导者。在感情中，白羊热情直接，爱就大声说出来。'
    }
  },
  {
    name: '金牛座',
    symbol: '♉',
    dateRange: '4.20 - 5.20',
    element: '土象星座',
    personality: {
      strengths: '稳重、可靠、耐心、务实、忠诚、有毅力',
      weaknesses: '固执、保守、占有欲强、吝啬、过于敏感',
      style: '金牛座的人做事稳重踏实，喜欢循序渐进，不喜欢冒险。他们注重物质享受，对美食和艺术有独特品味。在感情中，金牛专一忠诚，一旦爱上就会全心全意。'
    }
  },
  {
    name: '双子座',
    symbol: '♊',
    dateRange: '5.21 - 6.21',
    element: '风象星座',
    personality: {
      strengths: '聪明、机智、灵活、善于沟通、适应力强、好奇心强',
      weaknesses: '善变、肤浅、浮躁、缺乏耐心、容易分心',
      style: '双子座的人思维敏捷，善于变通，喜欢新鲜事物。他们口才出众，社交能力强，是聚会中的活跃分子。在感情中，双子追求新鲜感，需要一个能和他们一起探索世界的伴侣。'
    }
  },
  {
    name: '巨蟹座',
    symbol: '♋',
    dateRange: '6.22 - 7.22',
    element: '水象星座',
    personality: {
      strengths: '温柔、体贴、顾家、有同情心、记忆力强、直觉敏锐',
      weaknesses: '敏感、多疑、情绪化、恋旧、缺乏安全感',
      style: '巨蟹座的人内心柔软，重视家庭和亲情。他们善于照顾他人，总能给予最温暖的关怀。在感情中，巨蟹渴望稳定的关系，需要被理解和安全感。'
    }
  },
  {
    name: '狮子座',
    symbol: '♌',
    dateRange: '7.23 - 8.22',
    element: '火象星座',
    personality: {
      strengths: '自信、大方、热情、有魅力、创造力强、慷慨',
      weaknesses: '自负、虚荣、骄傲、控制欲强、容易受伤',
      style: '狮子座的人天生自带光环，喜欢成为焦点。他们慷慨大方，对朋友讲义气。在感情中，狮子渴望被崇拜和赞美，需要一个懂得欣赏他们的伴侣。'
    }
  },
  {
    name: '处女座',
    symbol: '♍',
    dateRange: '8.23 - 9.22',
    element: '土象星座',
    personality: {
      strengths: '细心、认真、负责、有条理、分析能力强、追求完美',
      weaknesses: '挑剔、洁癖、过于严肃、容易焦虑、追求完美过度',
      style: '处女座的人做事一丝不苟，注重细节，追求完美。他们善于分析问题，总能给出实用的建议。在感情中，处女通过实际行动表达爱意，默默付出不求回报。'
    }
  },
  {
    name: '天秤座',
    symbol: '♎',
    dateRange: '9.23 - 10.23',
    element: '风象星座',
    personality: {
      strengths: '优雅、公正、善于社交、有审美眼光、追求和谐、合作精神强',
      weaknesses: '优柔寡断、过于追求平衡、容易妥协、依赖性强',
      style: '天秤座的人追求和谐与美，善于在人际关系中找到平衡点。他们优雅大方，审美品味出众。在感情中，天秤追求平等的关系，希望双方都能付出和收获。'
    }
  },
  {
    name: '天蝎座',
    symbol: '♏',
    dateRange: '10.24 - 11.22',
    element: '水象星座',
    personality: {
      strengths: '神秘、魅力、直觉敏锐、意志力强、忠诚、有深度',
      weaknesses: '多疑、报复心强、过于执着、控制欲强、容易走极端',
      style: '天蝎座的人内心深邃，神秘莫测。他们对感情极其忠诚，一旦认定就会全力以赴。在感情中，天蝎渴望深刻的灵魂交流，追求灵肉合一的爱情。'
    }
  },
  {
    name: '射手座',
    symbol: '♐',
    dateRange: '11.23 - 12.21',
    element: '火象星座',
    personality: {
      strengths: '乐观、自由、热情、诚实、有幽默感、热爱冒险',
      weaknesses: '鲁莽、粗心、过于乐观、缺乏耐心、容易冲动',
      style: '射手座的人热爱自由，喜欢探索未知的世界。他们乐观开朗，总能给身边的人带来快乐。在感情中，射手需要空间和自由，不喜欢被束缚。'
    }
  },
  {
    name: '摩羯座',
    symbol: '♑',
    dateRange: '12.22 - 1.19',
    element: '土象星座',
    personality: {
      strengths: '稳重、务实、有野心、责任心强、自制力强、有耐心',
      weaknesses: '过于严肃、悲观、工作狂、冷漠、过于保守',
      style: '摩羯座的人脚踏实地，目标明确，为了成功愿意付出一切努力。他们外表冷静，内心却有着火热的激情。在感情中，摩羯慢热但专一，会用时间证明自己的爱。'
    }
  },
  {
    name: '水瓶座',
    symbol: '♒',
    dateRange: '1.20 - 2.18',
    element: '风象星座',
    personality: {
      strengths: '聪明、创新、独立、有远见、人道主义、思想前卫',
      weaknesses: '叛逆、过于理性、冷漠、固执、难以捉摸',
      style: '水瓶座的人思维独特，喜欢与众不同。他们追求精神上的共鸣，不喜欢被传统束缚。在感情中，水瓶渴望灵魂伴侣，需要一个能理解他们思想的人。'
    }
  },
  {
    name: '双鱼座',
    symbol: '♓',
    dateRange: '2.19 - 3.20',
    element: '水象星座',
    personality: {
      strengths: '浪漫、敏感、富有同情心、有艺术天赋、善良、想象力丰富',
      weaknesses: '过于敏感、逃避现实、容易受伤、缺乏自信、过于依赖',
      style: '双鱼座的人浪漫多情，内心充满梦幻和想象。他们善良体贴，总能感受到他人的情绪。在感情中，双鱼渴望被宠爱和保护，会为了爱情付出一切。'
    }
  }
])

// 运势时段
const fortunePeriods = ref(['今日', '明日', '本周', '本月'])
const selectedPeriod = ref('今日')
const selectedZodiac = ref('白羊座')

// 当前选中的星座
const currentZodiac = computed(() => {
  return zodiacSigns.value.find(z => z.name === selectedZodiac.value)
})

// 选择星座
const selectZodiac = (name) => {
  selectedZodiac.value = name
}

// 运势数据（模拟）
const currentFortune = computed(() => {
  const fortunes = {
    '今日': {
      overall: '今天整体运势平稳，适合处理日常事务。保持积极的心态，好运自然会降临。工作中可能会遇到一些小挑战，但凭借你的能力一定能够顺利解决。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 75, desc: '工作中会有新的机会出现，保持专注，抓住机遇。' },
        love: { name: '爱情', icon: '💕', score: 82, desc: '感情运势不错，适合与伴侣共度温馨时光。' },
        wealth: { name: '财运', icon: '💰', score: 68, desc: '财务状况稳定，不宜进行大额投资。' },
        health: { name: '健康', icon: '💪', score: 85, desc: '身体状态良好，适当运动有助于保持活力。' }
      }
    },
    '明日': {
      overall: '明天运势有所上升，适合开展新的计划和项目。你的思维清晰，能够做出明智的决策。人际关系方面也会有不错的进展。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 85, desc: '工作效率高，适合处理重要事务。' },
        love: { name: '爱情', icon: '💕', score: 78, desc: '有伴者感情升温，单身者有机会遇到心仪对象。' },
        wealth: { name: '财运', icon: '💰', score: 72, desc: '财运平稳，可以考虑理财规划。' },
        health: { name: '健康', icon: '💪', score: 75, desc: '注意劳逸结合，保证充足睡眠。' }
      }
    },
    '本周': {
      overall: '本周整体运势向好，各项事务进展顺利。你会有更多的机会展现自己的才华。保持谦逊和努力，成功就在眼前。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 88, desc: '本周事业运极佳，有望获得晋升或加薪机会。' },
        love: { name: '爱情', icon: '💕', score: 90, desc: '感情甜蜜，适合安排浪漫约会。' },
        wealth: { name: '财运', icon: '💰', score: 80, desc: '财运亨通，投资方面可能有惊喜。' },
        health: { name: '健康', icon: '💪', score: 82, desc: '精力充沛，但要注意不要过度劳累。' }
      }
    },
    '本月': {
      overall: '本月是充满机遇的一个月。你将面临一些重要的抉择，但不要害怕，跟随内心的声音。保持积极向上的态度，好运会相伴左右。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 82, desc: '本月事业发展顺利，适合制定长期目标。' },
        love: { name: '爱情', icon: '💕', score: 85, desc: '感情稳定发展，适合深入交流。' },
        wealth: { name: '财运', icon: '💰', score: 78, desc: '财务状况稳定增长，适合稳健投资。' },
        health: { name: '健康', icon: '💪', score: 79, desc: '注意饮食健康，保持规律作息。' }
      }
    }
  }
  
  return fortunes[selectedPeriod.value]
})

// 底部签名文案
const signatureText = ref('愿我们的爱情，如同初升的太阳，温暖而耀眼；如同潺潺的溪流，绵长而清澈。每一个与你共度的日子，都是我生命中最美好的礼物。我愿用一生的时间，陪你走过春夏秋冬，看遍世间风景。爱你，是我这辈子做过最好的选择。')

// 格式化日期
const formatDate = (date) => {
  const d = new Date(date)
  const year = d.getFullYear()
  const month = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return `${year}年${month}月${day}日`
}

// 补零函数
const padZero = (num) => {
  return String(num).padStart(2, '0')
}

// 定时器
let countdownInterval = null

onMounted(() => {
  // 初始化
  updateAnniversaryDays()
  updateCountdown()
  
  // 倒计时定时器
  countdownInterval = setInterval(() => {
    updateCountdown()
  }, 1000)
  
  // 语录轮播定时器
  quoteInterval = setInterval(() => {
    currentQuoteIndex.value = (currentQuoteIndex.value + 1) % sweetQuotes.value.length
  }, 5000)
})

onUnmounted(() => {
  if (countdownInterval) {
    clearInterval(countdownInterval)
  }
  if (quoteInterval) {
    clearInterval(quoteInterval)
  }
})
</script>

<style scoped>
.app-container {
  padding-bottom: 2rem;
  background: linear-gradient(180deg, #fff5f8 0%, #ffe8f0 50%, #fff5f8 100%);
}

/* 头部样式 */
.header-section {
  text-align: center;
  padding: 2rem 1rem;
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  position: relative;
  overflow: hidden;
}

.decoration-top {
  position: absolute;
  top: 0.5rem;
  left: 0;
  right: 0;
  display: flex;
  justify-content: center;
  gap: 2rem;
}

.heart {
  color: rgba(255, 255, 255, 0.5);
  font-size: 1.5rem;
}

.heart-1 {
  animation: float 3s ease-in-out infinite;
}

.heart-2 {
  animation: float 3s ease-in-out infinite 0.5s;
}

.heart-3 {
  animation: float 3s ease-in-out infinite 1s;
}

.main-title {
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--white);
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  margin-bottom: 0.5rem;
}

.subtitle {
  font-size: 0.9rem;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 300;
}

/* 头像区域 */
.avatar-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}

.avatar-item {
  text-align: center;
}

.avatar-img {
  width: 5rem;
  height: 5rem;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid var(--white);
  box-shadow: var(--shadow);
}

.avatar-name {
  display: block;
  margin-top: 0.5rem;
  font-size: 0.85rem;
  color: var(--text-light);
  font-weight: 500;
}

.heart-icon {
  font-size: 1.5rem;
}

/* 天数统计 */
.days-card {
  text-align: center;
  background: linear-gradient(135deg, var(--white) 0%, #fff8fa 100%);
}

.days-display {
  display: flex;
  justify-content: center;
  align-items: baseline;
  margin: 1rem 0;
}

.days-number {
  font-size: 4rem;
  font-weight: 700;
  color: var(--primary-color);
  text-shadow: 0 2px 20px rgba(255, 107, 157, 0.3);
}

.days-label {
  font-size: 1.5rem;
  color: var(--primary-color);
  margin-left: 0.5rem;
  font-weight: 600;
}

.days-desc {
  font-size: 0.9rem;
  color: var(--text-light);
}

/* 倒计时 */
.countdown-card {
  background: linear-gradient(135deg, var(--white) 0%, #fff8fa 100%);
}

.countdown-display {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  margin: 1rem 0;
}

.countdown-item {
  text-align: center;
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  padding: 0.75rem 0.5rem;
  border-radius: 0.5rem;
  min-width: 3.5rem;
}

.countdown-number {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--white);
}

.countdown-label {
  font-size: 0.7rem;
  color: rgba(255, 255, 255, 0.9);
  margin-top: 0.25rem;
}

.countdown-separator {
  font-size: 1.5rem;
  color: var(--primary-color);
  font-weight: 700;
}

.countdown-next {
  text-align: center;
  font-size: 0.85rem;
  color: var(--text-light);
}

/* 纪念日列表 */
.anniversary-list {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.anniversary-item {
  display: flex;
  align-items: center;
  padding: 0.75rem;
  background: linear-gradient(90deg, #fff8fa 0%, var(--white) 100%);
  border-radius: 0.75rem;
  border-left: 4px solid var(--primary-color);
}

.anniversary-icon {
  font-size: 1.5rem;
  margin-right: 0.75rem;
}

.anniversary-info {
  flex: 1;
}

.anniversary-name {
  font-size: 1rem;
  font-weight: 600;
  color: var(--text-color);
}

.anniversary-date {
  font-size: 0.8rem;
  color: var(--text-light);
  margin-top: 0.25rem;
}

.anniversary-count {
  text-align: center;
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  padding: 0.5rem 0.75rem;
  border-radius: 0.5rem;
  min-width: 3.5rem;
}

.count-number {
  font-size: 1.1rem;
  font-weight: 700;
  color: var(--white);
}

.count-label {
  font-size: 0.65rem;
  color: rgba(255, 255, 255, 0.9);
}

/* 语录轮播 */
.quotes-card {
  background: linear-gradient(135deg, #fff8fa 0%, #ffe8f0 100%);
}

.quotes-container {
  min-height: 8rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.quote-item {
  text-align: center;
  padding: 0 0.5rem;
}

.quote-icon {
  font-size: 3rem;
  color: var(--primary-color);
  opacity: 0.3;
  margin-bottom: 0.5rem;
}

.quote-text {
  font-size: 1rem;
  color: var(--text-color);
  line-height: 1.8;
  font-style: italic;
  margin-bottom: 0.75rem;
}

.quote-author {
  font-size: 0.85rem;
  color: var(--text-light);
}

.quotes-dots {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-top: 1rem;
}

.dot {
  width: 0.5rem;
  height: 0.5rem;
  border-radius: 50%;
  background: rgba(255, 107, 157, 0.3);
  cursor: pointer;
  transition: all 0.3s ease;
}

.dot.active {
  background: var(--primary-color);
  transform: scale(1.2);
}

/* 相册区域 */
.album-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.75rem;
}

.album-item {
  position: relative;
  border-radius: 0.75rem;
  overflow: hidden;
  aspect-ratio: 1;
  cursor: pointer;
}

.album-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.album-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: linear-gradient(transparent, rgba(0, 0, 0, 0.7));
  padding: 1.5rem 0.75rem 0.75rem;
  transform: translateY(100%);
  transition: transform 0.3s ease;
}

.album-desc {
  color: var(--white);
  font-size: 0.85rem;
}

.album-item:hover .album-img {
  transform: scale(1.1);
}

.album-item:hover .album-overlay {
  transform: translateY(0);
}

/* 星座运势 */
.zodiac-section {
  position: relative;
}

.zodiac-selector {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.zodiac-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0.75rem 0.5rem;
  background: var(--bg-color);
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent;
}

.zodiac-item:hover {
  background: #ffe8f0;
}

.zodiac-item.active {
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  border-color: var(--primary-color);
}

.zodiac-symbol {
  font-size: 1.5rem;
  margin-bottom: 0.25rem;
}

.zodiac-item.active .zodiac-symbol,
.zodiac-item.active .zodiac-name {
  color: var(--white);
}

.zodiac-name {
  font-size: 0.75rem;
  color: var(--text-color);
  font-weight: 500;
}

/* 运势时段切换 */
.fortune-period {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
  justify-content: center;
}

.period-btn {
  padding: 0.5rem 1rem;
  border: none;
  background: var(--bg-color);
  color: var(--text-color);
  border-radius: 1rem;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.period-btn:hover {
  background: #ffe8f0;
}

.period-btn.active {
  background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  color: var(--white);
}

/* 运势内容 */
.fortune-content {
  background: var(--bg-color);
  border-radius: 0.75rem;
  padding: 1rem;
}

.fortune-header {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid rgba(255, 107, 157, 0.2);
}

.fortune-symbol {
  font-size: 2.5rem;
}

.fortune-name {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--text-color);
}

.fortune-date {
  font-size: 0.85rem;
  color: var(--text-light);
  margin-top: 0.25rem;
}

/* 四大维度 */
.fortune-dimensions {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.dimension-item {
  background: var(--white);
  padding: 0.75rem;
  border-radius: 0.5rem;
}

.dimension-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.dimension-icon {
  font-size: 1.25rem;
}

.dimension-name {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--text-color);
}

.dimension-bar {
  height: 0.5rem;
  background: rgba(255, 107, 157, 0.2);
  border-radius: 0.25rem;
  overflow: hidden;
  margin-bottom: 0.5rem;
}

.dimension-progress {
  height: 100%;
  background: linear-gradient(90deg, var(--primary-color) 0%, var(--secondary-color) 100%);
  border-radius: 0.25rem;
  transition: width 0.5s ease;
}

.dimension-desc {
  font-size: 0.75rem;
  color: var(--text-light);
  line-height: 1.4;
}

/* 整体运势 */
.fortune-overall {
  margin-bottom: 1rem;
  padding: 0.75rem;
  background: linear-gradient(135deg, #fff8fa 0%, #ffe8f0 100%);
  border-radius: 0.5rem;
}

.overall-title {
  font-size: 0.95rem;
  font-weight: 600;
  color: var(--primary-color);
  margin-bottom: 0.5rem;
}

.overall-desc {
  font-size: 0.85rem;
  color: var(--text-color);
  line-height: 1.6;
}

/* 性格解析 */
.personality-section {
  padding-top: 1rem;
  border-top: 1px solid rgba(255, 107, 157, 0.2);
}

.personality-title {
  font-size: 0.95rem;
  font-weight: 600;
  color: var(--primary-color);
  margin-bottom: 0.75rem;
}

.personality-traits {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.75rem;
  margin-bottom: 0.75rem;
}

.trait-item {
  background: var(--white);
  padding: 0.75rem;
  border-radius: 0.5rem;
}

.trait-item.full {
  grid-column: 1 / -1;
}

.trait-label {
  display: inline-block;
  padding: 0.25rem 0.75rem;
  background: var(--primary-color);
  color: var(--white);
  font-size: 0.75rem;
  border-radius: 1rem;
  margin-bottom: 0.5rem;
  font-weight: 600;
}

.trait-label.positive {
  background: linear-gradient(135deg, #4caf50 0%, #81c784 100%);
}

.trait-label.negative {
  background: linear-gradient(135deg, #ff9800 0%, #ffb74d 100%);
}

.trait-desc {
  font-size: 0.8rem;
  color: var(--text-color);
  line-height: 1.5;
}

/* 底部样式 */
.footer-section {
  padding: 1.5rem 1rem;
  text-align: center;
}

.signature-card {
  background: linear-gradient(135deg, var(--white) 0%, #fff8fa 100%);
  padding: 1.5rem;
  border-radius: 1rem;
  box-shadow: var(--shadow);
  margin-bottom: 1rem;
}

.signature-decoration {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.deco-line {
  width: 3rem;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--primary-color), transparent);
}

.deco-heart {
  font-size: 1.25rem;
}

.signature-text {
  font-size: 0.95rem;
  color: var(--text-color);
  line-height: 1.8;
  margin-bottom: 1rem;
  font-style: italic;
}

.signature-author {
  font-size: 0.9rem;
  color: var(--text-light);
  margin-bottom: 0.5rem;
}

.signature-date {
  font-size: 0.8rem;
  color: var(--primary-color);
  font-weight: 500;
}

.footer-bottom {
  padding-top: 1rem;
}

.footer-text {
  font-size: 0.85rem;
  color: var(--text-light);
}

/* 响应式调整 */
@media screen and (max-width: 360px) {
  .countdown-item {
    min-width: 2.8rem;
    padding: 0.5rem 0.3rem;
  }
  
  .countdown-number {
    font-size: 1.2rem;
  }
  
  .zodiac-selector {
    grid-template-columns: repeat(3, 1fr);
  }
  
  .fortune-dimensions {
    grid-template-columns: 1fr;
  }
  
  .personality-traits {
    grid-template-columns: 1fr;
  }
}

@media screen and (min-width: 751px) {
  .album-grid {
    grid-template-columns: repeat(3, 1fr);
  }
  
  .zodiac-selector {
    grid-template-columns: repeat(6, 1fr);
  }
}
</style>
