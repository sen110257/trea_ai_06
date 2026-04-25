<template>
  <div class="app-container" @touchstart="handleTouchStart" @touchmove="handleTouchMove" @touchend="handleTouchEnd">
    <!-- 顶部浪漫主标题区 -->
    <header class="header-section">
      <div class="decoration-float">
        <span v-for="(heart, i) in 8" :key="i" class="floating-heart" :style="getHeartStyle(i)">♥</span>
      </div>
      <div class="header-content">
        <div class="title-decoration">
          <span class="deco-star">✨</span>
          <h1 class="main-title">{{ mainTitle }}</h1>
          <span class="deco-star">✨</span>
        </div>
        <p class="subtitle">{{ subTitle }}</p>
        <div class="together-days-badge">
          <span class="badge-icon">💕</span>
          <span class="badge-text">我们已相恋</span>
          <span class="badge-days">{{ totalDays }}</span>
          <span class="badge-unit">天</span>
        </div>
      </div>
    </header>

    <!-- 情侣头像合照展示区 -->
    <section class="avatar-section">
      <div class="avatar-card premium-card">
        <div class="avatar-container">
          <div class="avatar-wrapper avatar-left">
            <div class="avatar-ring">
              <img :src="coupleAvatar.left" alt="女方头像" class="avatar-img" />
            </div>
            <div class="avatar-info">
              <span class="avatar-name">{{ coupleName.left }}</span>
              <span class="avatar-role">女朋友</span>
            </div>
          </div>
          
          <div class="heart-connector">
            <div class="connect-line left"></div>
            <div class="heart-center pulse-effect">
              <span class="heart-icon">💗</span>
            </div>
            <div class="connect-line right"></div>
          </div>
          
          <div class="avatar-wrapper avatar-right">
            <div class="avatar-ring">
              <img :src="coupleAvatar.right" alt="男方头像" class="avatar-img" />
            </div>
            <div class="avatar-info">
              <span class="avatar-name">{{ coupleName.right }}</span>
              <span class="avatar-role">男朋友</span>
            </div>
          </div>
        </div>
        
        <div class="love-progress">
          <div class="progress-bar">
            <div class="progress-fill" :style="{ width: progressPercent + '%' }"></div>
          </div>
          <div class="progress-info">
            <span class="progress-text">恋爱进度</span>
            <span class="progress-percent">{{ progressPercent }}%</span>
          </div>
        </div>
      </div>
    </section>

    <!-- 时分秒实时倒计时模块 -->
    <section class="countdown-section">
      <div class="countdown-card premium-card">
        <div class="card-header">
          <span class="header-icon">⏰</span>
          <h2 class="card-title">距离下一个纪念日</h2>
        </div>
        
        <div class="next-anniversary-info">
          <span class="anniversary-name">{{ nextAnniversary }}</span>
          <span class="anniversary-date">{{ nextAnniversaryDate }}</span>
        </div>
        
        <div class="countdown-display">
          <div class="countdown-item" v-for="(item, key) in countdownItems" :key="key">
            <div class="countdown-box">
              <div class="countdown-flip">
                <span class="flip-number">{{ padZero(countdown[key]) }}</span>
              </div>
            </div>
            <span class="countdown-label">{{ item.label }}</span>
          </div>
        </div>
        
        <div class="countdown-hint">
          <span class="hint-icon">💡</span>
          <span class="hint-text">珍惜每一个与你共度的时刻</span>
        </div>
      </div>
    </section>

    <!-- 多节点重要纪念日清单 -->
    <section class="anniversary-section">
      <div class="premium-card">
        <div class="card-header">
          <span class="header-icon">📅</span>
          <h2 class="card-title">重要纪念日</h2>
        </div>
        
        <div class="anniversary-list">
          <div 
            v-for="(item, index) in anniversaryList" 
            :key="index" 
            class="anniversary-item"
            :class="{ 'is-today': item.daysUntil === 0, 'is-passed': item.daysUntil < 0 }"
          >
            <div class="anniversary-icon-wrapper">
              <span class="anniversary-icon">{{ item.icon }}</span>
            </div>
            
            <div class="anniversary-content">
              <div class="anniversary-header">
                <h3 class="anniversary-name">{{ item.name }}</h3>
                <div class="days-badge" :class="getDaysBadgeClass(item.daysUntil)">
                  <span class="days-count">{{ Math.abs(item.daysUntil) }}</span>
                  <span class="days-unit">{{ item.daysUntil > 0 ? '天后' : item.daysUntil < 0 ? '已过' : '今天' }}</span>
                </div>
              </div>
              <div class="anniversary-date">{{ formatDate(item.date) }}</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 甜蜜情话语录轮播 -->
    <section class="quotes-section">
      <div class="quotes-card premium-card">
        <div class="card-header">
          <span class="header-icon">💌</span>
          <h2 class="card-title">甜蜜语录</h2>
        </div>
        
        <div class="quotes-carousel">
          <div class="quote-slide" :key="currentQuoteIndex">
            <div class="quote-decoration">
              <span class="quote-mark left">"</span>
            </div>
            <p class="quote-text">{{ currentQuote }}</p>
            <div class="quote-decoration">
              <span class="quote-mark right">"</span>
            </div>
          </div>
        </div>
        
        <div class="quotes-controls">
          <button class="control-btn prev" @click="prevQuote">
            <span class="btn-icon">‹</span>
          </button>
          <div class="quotes-dots">
            <span 
              v-for="(_, index) in sweetQuotes" 
              :key="index"
              class="dot"
              :class="{ active: index === currentQuoteIndex }"
              @click="currentQuoteIndex = index"
            ></span>
          </div>
          <button class="control-btn next" @click="nextQuote">
            <span class="btn-icon">›</span>
          </button>
        </div>
      </div>
    </section>

    <!-- 情侣相册展示区 -->
    <section class="album-section">
      <div class="premium-card">
        <div class="card-header">
          <span class="header-icon">📸</span>
          <h2 class="card-title">甜蜜相册</h2>
          <span class="photo-count">{{ albumPhotos.length }}张照片</span>
        </div>
        
        <div class="album-masonry">
          <div 
            v-for="(photo, index) in albumPhotos" 
            :key="index" 
            class="album-item"
            :class="'size-' + photo.size"
          >
            <div class="photo-wrapper">
              <img :src="photo.url" :alt="photo.alt" class="album-img" loading="lazy" />
              <div class="photo-overlay">
                <div class="overlay-content">
                  <span class="photo-icon">💖</span>
                  <span class="photo-desc">{{ photo.description }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- 星座运势与配对专区 -->
    <section class="zodiac-section">
      <div class="premium-card">
        <div class="card-header">
          <span class="header-icon">✨</span>
          <h2 class="card-title">星座运势</h2>
        </div>
        
        <!-- Tab切换 -->
        <div class="zodiac-tabs">
          <button 
            v-for="tab in zodiacTabs" 
            :key="tab.value"
            class="tab-btn"
            :class="{ active: activeZodiacTab === tab.value }"
            @click="activeZodiacTab = tab.value"
          >
            {{ tab.label }}
          </button>
        </div>

        <!-- 单人运势 -->
        <div v-show="activeZodiacTab === 'single'" class="tab-content">
          <!-- 星座选择 -->
          <div class="zodiac-selector">
            <div 
              v-for="(zodiac, index) in zodiacSigns" 
              :key="index"
              class="zodiac-chip"
              :class="{ active: selectedZodiac === zodiac.name }"
              @click="selectZodiac(zodiac.name)"
            >
              <span class="chip-symbol">{{ zodiac.symbol }}</span>
              <span class="chip-name">{{ zodiac.name }}</span>
            </div>
          </div>

          <!-- 运势时段切换 -->
          <div class="fortune-period-tabs">
            <button 
              v-for="period in fortunePeriods" 
              :key="period"
              class="period-chip"
              :class="{ active: selectedPeriod === period }"
              @click="selectedPeriod = period"
            >
              {{ period }}
            </button>
          </div>

          <!-- 运势内容 -->
          <div class="fortune-display" v-if="currentZodiac">
            <!-- 星座信息卡片 -->
            <div class="zodiac-info-card">
              <div class="zodiac-main">
                <div class="zodiac-symbol-large">{{ currentZodiac.symbol }}</div>
                <div class="zodiac-details">
                  <h3 class="zodiac-name-large">{{ currentZodiac.name }}</h3>
                  <p class="zodiac-date">{{ currentZodiac.dateRange }}</p>
                  <span class="zodiac-element">{{ currentZodiac.element }}</span>
                </div>
              </div>
              
              <!-- 幸运信息 -->
              <div class="lucky-info">
                <div class="lucky-item">
                  <span class="lucky-label">幸运数字</span>
                  <span class="lucky-value number">{{ currentZodiac.lucky.number }}</span>
                </div>
                <div class="lucky-item">
                  <span class="lucky-label">幸运颜色</span>
                  <span class="lucky-value color" :style="{ backgroundColor: currentZodiac.lucky.color.hex }">
                    {{ currentZodiac.lucky.color.name }}
                  </span>
                </div>
                <div class="lucky-item">
                  <span class="lucky-label">幸运方位</span>
                  <span class="lucky-value">{{ currentZodiac.lucky.direction }}</span>
                </div>
              </div>
              
              <!-- 开运提示 -->
              <div class="lucky-tip">
                <span class="tip-icon">💫</span>
                <span class="tip-text">{{ currentZodiac.lucky.tip }}</span>
              </div>
            </div>

            <!-- 四大维度运势 -->
            <div class="fortune-dimensions">
              <div class="dimension-card" v-for="(dimension, key) in currentFortune.dimensions" :key="key">
                <div class="dimension-header">
                  <span class="dimension-icon">{{ dimension.icon }}</span>
                  <span class="dimension-name">{{ dimension.name }}</span>
                  <span class="dimension-score" :class="getScoreClass(dimension.score)">{{ dimension.score }}分</span>
                </div>
                <div class="dimension-bar-wrapper">
                  <div class="dimension-bar">
                    <div 
                      class="dimension-fill" 
                      :style="{ width: dimension.score + '%' }"
                      :class="getScoreClass(dimension.score)"
                    ></div>
                  </div>
                </div>
                <p class="dimension-desc">{{ dimension.desc }}</p>
              </div>
            </div>

            <!-- 整体运势 -->
            <div class="overall-fortune">
              <div class="overall-header">
                <span class="overall-icon">🌟</span>
                <h4 class="overall-title">整体运势</h4>
              </div>
              <p class="overall-text">{{ currentFortune.overall }}</p>
            </div>

            <!-- 星座专属语录轮播 -->
            <div class="zodiac-quotes-section">
              <div class="zodiac-quotes-header">
                <span class="quotes-icon">💭</span>
                <h4 class="quotes-title">星座专属语录</h4>
              </div>
              <div class="zodiac-quote-slider">
                <div class="zodiac-quote-item" :key="currentZodiacQuoteIndex">
                  <p class="zodiac-quote-text">{{ currentZodiacQuote }}</p>
                </div>
              </div>
              <div class="zodiac-quotes-dots">
                <span 
                  v-for="(_, index) in currentZodiacQuotes" 
                  :key="index"
                  class="small-dot"
                  :class="{ active: index === currentZodiacQuoteIndex }"
                ></span>
              </div>
            </div>

            <!-- 性格解析 -->
            <div class="personality-analysis">
              <div class="analysis-header">
                <span class="analysis-icon">🎭</span>
                <h4 class="analysis-title">性格解析</h4>
              </div>
              
              <div class="personality-cards">
                <div class="personality-card strengths">
                  <div class="card-badge">
                    <span class="badge-icon">💪</span>
                    <span class="badge-text">优点</span>
                  </div>
                  <div class="tags-container">
                    <span v-for="(trait, i) in currentZodiac.personality.strengthsList" :key="i" class="trait-tag positive">
                      {{ trait }}
                    </span>
                  </div>
                  <p class="personality-desc">{{ currentZodiac.personality.strengths }}</p>
                </div>
                
                <div class="personality-card weaknesses">
                  <div class="card-badge">
                    <span class="badge-icon">⚠️</span>
                    <span class="badge-text">缺点</span>
                  </div>
                  <div class="tags-container">
                    <span v-for="(trait, i) in currentZodiac.personality.weaknessesList" :key="i" class="trait-tag negative">
                      {{ trait }}
                    </span>
                  </div>
                  <p class="personality-desc">{{ currentZodiac.personality.weaknesses }}</p>
                </div>
              </div>
              
              <div class="style-card">
                <div class="card-badge">
                  <span class="badge-icon">🎯</span>
                  <span class="badge-text">处事风格</span>
                </div>
                <p class="style-desc">{{ currentZodiac.personality.style }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 双人配对 -->
        <div v-show="activeZodiacTab === 'pair'" class="tab-content">
          <div class="pair-selector">
            <div class="select-column">
              <label class="select-label">她的星座</label>
              <div class="zodiac-dropdown" @click="showFemaleDropdown = !showFemaleDropdown">
                <div class="dropdown-display">
                  <span class="display-symbol">{{ femaleZodiac.symbol }}</span>
                  <span class="display-name">{{ femaleZodiac.name }}</span>
                  <span class="dropdown-arrow" :class="{ open: showFemaleDropdown }">▼</span>
                </div>
              </div>
              <div class="dropdown-options" v-show="showFemaleDropdown">
                <div 
                  v-for="zodiac in zodiacSigns" 
                  :key="zodiac.name"
                  class="option-item"
                  :class="{ selected: femaleZodiac.name === zodiac.name }"
                  @click="selectFemaleZodiac(zodiac.name)"
                >
                  <span class="option-symbol">{{ zodiac.symbol }}</span>
                  <span class="option-name">{{ zodiac.name }}</span>
                </div>
              </div>
            </div>
            
            <div class="pair-connector">
              <span class="connector-heart">💕</span>
            </div>
            
            <div class="select-column">
              <label class="select-label">他的星座</label>
              <div class="zodiac-dropdown" @click="showMaleDropdown = !showMaleDropdown">
                <div class="dropdown-display">
                  <span class="display-symbol">{{ maleZodiac.symbol }}</span>
                  <span class="display-name">{{ maleZodiac.name }}</span>
                  <span class="dropdown-arrow" :class="{ open: showMaleDropdown }">▼</span>
                </div>
              </div>
              <div class="dropdown-options" v-show="showMaleDropdown">
                <div 
                  v-for="zodiac in zodiacSigns" 
                  :key="zodiac.name"
                  class="option-item"
                  :class="{ selected: maleZodiac.name === zodiac.name }"
                  @click="selectMaleZodiac(zodiac.name)"
                >
                  <span class="option-symbol">{{ zodiac.symbol }}</span>
                  <span class="option-name">{{ zodiac.name }}</span>
                </div>
              </div>
            </div>
          </div>
          
          <!-- 开始配对按钮 -->
          <button class="start-pair-btn" @click="calculateCompatibility">
            <span class="btn-icon">🔮</span>
            <span class="btn-text">开始配对</span>
          </button>
          
          <!-- 配对结果 -->
          <div v-if="pairResult" class="pair-result">
            <div class="compatibility-score">
              <div class="score-circle" :class="getCompatibilityClass(pairResult.compatibility)">
                <span class="score-value">{{ pairResult.compatibility }}</span>
                <span class="score-unit">%</span>
              </div>
              <div class="score-label">契合度</div>
              <div class="score-level">{{ pairResult.level }}</div>
            </div>
            
            <div class="compatibility-bars">
              <div class="bar-item">
                <div class="bar-header">
                  <span class="bar-icon">💕</span>
                  <span class="bar-label">爱情契合</span>
                  <span class="bar-score">{{ pairResult.loveScore }}%</span>
                </div>
                <div class="bar-bg">
                  <div class="bar-fill love" :style="{ width: pairResult.loveScore + '%' }"></div>
                </div>
              </div>
              
              <div class="bar-item">
                <div class="bar-header">
                  <span class="bar-icon">💬</span>
                  <span class="bar-label">沟通默契</span>
                  <span class="bar-score">{{ pairResult.communicationScore }}%</span>
                </div>
                <div class="bar-bg">
                  <div class="bar-fill communication" :style="{ width: pairResult.communicationScore + '%' }"></div>
                </div>
              </div>
              
              <div class="bar-item">
                <div class="bar-header">
                  <span class="bar-icon">🤝</span>
                  <span class="bar-label">相处和谐</span>
                  <span class="bar-score">{{ pairResult.harmonyScore }}%</span>
                </div>
                <div class="bar-bg">
                  <div class="bar-fill harmony" :style="{ width: pairResult.harmonyScore + '%' }"></div>
                </div>
              </div>
              
              <div class="bar-item">
                <div class="bar-header">
                  <span class="bar-icon">💰</span>
                  <span class="bar-label">价值观</span>
                  <span class="bar-score">{{ pairResult.valueScore }}%</span>
                </div>
                <div class="bar-bg">
                  <div class="bar-fill value" :style="{ width: pairResult.valueScore + '%' }"></div>
                </div>
              </div>
            </div>
            
            <div class="pair-analysis">
              <div class="analysis-card">
                <div class="card-title-wrapper">
                  <span class="card-title-icon">💡</span>
                  <h4 class="card-title-text">配对分析</h4>
                </div>
                <p class="analysis-text">{{ pairResult.analysis }}</p>
              </div>
              
              <div class="advice-card">
                <div class="card-title-wrapper">
                  <span class="card-title-icon">💝</span>
                  <h4 class="card-title-text">感情建议</h4>
                </div>
                <p class="advice-text">{{ pairResult.advice }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 占卜抽签 -->
        <div v-show="activeZodiacTab === 'divination'" class="tab-content">
          <div class="divination-intro">
            <div class="intro-icon">🎴</div>
            <h3 class="intro-title">今日运势占卜</h3>
            <p class="intro-desc">静下心来，抽取属于你的今日运势</p>
          </div>
          
          <div class="divination-area">
            <div v-if="!divinationResult" class="card-stack" @click="drawCard">
              <div class="back-card back-1"></div>
              <div class="back-card back-2"></div>
              <div class="back-card back-3">
                <div class="card-back-design">
                  <span class="card-icon">🎴</span>
                  <span class="card-hint">点击抽取</span>
                </div>
              </div>
            </div>
            
            <div v-else class="result-card-wrapper" :class="{ 'show-result': divinationResult }">
              <div class="result-card">
                <div class="card-header-deco">
                  <span class="deco-line left"></span>
                  <span class="deco-star">✨</span>
                  <span class="deco-line right"></span>
                </div>
                
                <div class="card-result-type">
                  <span class="result-icon">{{ divinationResult.icon }}</span>
                  <span class="result-title">{{ divinationResult.title }}</span>
                </div>
                
                <div class="card-lucky-numbers" v-if="divinationResult.luckyNumbers">
                  <span class="numbers-label">幸运数字</span>
                  <div class="numbers-list">
                    <span v-for="num in divinationResult.luckyNumbers" :key="num" class="lucky-num">{{ num }}</span>
                  </div>
                </div>
                
                <div class="card-fortune-text">
                  <p class="fortune-content">{{ divinationResult.content }}</p>
                </div>
                
                <div class="card-footer-deco">
                  <span class="footer-icon">🌟</span>
                </div>
              </div>
            </div>
          </div>
          
          <div class="divination-actions">
            <button class="action-btn primary" @click="drawCard">
              <span class="action-icon">🎴</span>
              <span class="action-text">{{ divinationResult ? '重新占卜' : '抽取运势' }}</span>
            </button>
            
            <button v-if="divinationResult" class="action-btn secondary" @click="shareResult">
              <span class="action-icon">📤</span>
              <span class="action-text">分享运势</span>
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- 底部暖心签名文案区域 -->
    <footer class="footer-section">
      <div class="signature-card premium-card">
        <div class="signature-header">
          <div class="signature-decoration">
            <span class="deco-flower left">🌸</span>
            <span class="deco-heart">💖</span>
            <span class="deco-flower right">🌸</span>
          </div>
        </div>
        
        <div class="signature-content">
          <p class="signature-text">{{ signatureText }}</p>
        </div>
        
        <div class="signature-footer">
          <div class="signer-info">
            <span class="signer-name">{{ coupleName.right }}</span>
            <span class="signer-to">致</span>
            <span class="signer-name">{{ coupleName.left }}</span>
          </div>
          <div class="signature-date">
            <span class="date-icon">📅</span>
            <span class="date-text">{{ formatDate(new Date()) }}</span>
          </div>
        </div>
      </div>
      
      <div class="footer-bottom">
        <div class="footer-hearts">
          <span v-for="i in 5" :key="i" class="footer-heart">♥</span>
        </div>
        <p class="footer-text">愿我们的爱情，如同星辰般永恒 ✨</p>
        <p class="footer-copyright">Made with 💗 for our love story</p>
      </div>
    </footer>

    <!-- 占卜结果弹窗 -->
    <div v-if="showResultPopup" class="popup-overlay" @click="closePopup">
      <div class="popup-content" @click.stop>
        <div class="popup-header">
          <span class="popup-title">占卜结果</span>
          <button class="popup-close" @click="closePopup">×</button>
        </div>
        <div class="popup-body">
          <div v-if="divinationResult" class="popup-result">
            <div class="popup-icon">{{ divinationResult.icon }}</div>
            <h3 class="popup-result-title">{{ divinationResult.title }}</h3>
            <p class="popup-result-text">{{ divinationResult.content }}</p>
            <div v-if="divinationResult.luckyNumbers" class="popup-lucky">
              <span class="popup-lucky-label">今日幸运数字：</span>
              <span v-for="num in divinationResult.luckyNumbers" :key="num" class="popup-lucky-num">{{ num }}</span>
            </div>
          </div>
        </div>
        <div class="popup-footer">
          <button class="popup-btn" @click="closePopup">知道了</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

// ========== 基础配置 ==========
const mainTitle = ref('我们的爱情日记')
const subTitle = ref('每一刻与你共度，都是最珍贵的时光')

// 情侣头像和名字
const coupleAvatar = ref({
  left: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cute%20anime%20girl%20avatar%20pink%20hair%20soft%20romantic%20style&image_size=square',
  right: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cute%20anime%20boy%20avatar%20brown%20hair%20warm%20romantic%20style&image_size=square'
})

const coupleName = ref({
  left: '小仙女',
  right: '大暖男'
})

// 恋爱开始日期
const startDate = ref(new Date('2023-02-14'))

// ========== 计算属性 ==========
// 总天数
const totalDays = computed(() => {
  const now = new Date()
  const diff = now - startDate.value
  return Math.floor(diff / (1000 * 60 * 60 * 24))
})

// 恋爱进度百分比
const progressPercent = computed(() => {
  const yearProgress = (totalDays.value % 365) / 365
  return Math.floor(yearProgress * 100)
})

// ========== 纪念日列表 ==========
const anniversaryList = ref([
  { name: '相识日', date: new Date('2023-01-01'), icon: '🌸', daysUntil: 0 },
  { name: '表白日', date: new Date('2023-02-14'), icon: '💐', daysUntil: 0 },
  { name: '女方生日', date: new Date('2000-06-15'), icon: '🎂', daysUntil: 0 },
  { name: '男方生日', date: new Date('1999-11-20'), icon: '🎈', daysUntil: 0 },
  { name: '恋爱纪念日', date: new Date('2023-02-14'), icon: '💝', daysUntil: 0 },
  { name: '第一次约会', date: new Date('2023-02-20'), icon: '🌹', daysUntil: 0 }
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

const nextAnniversaryDate = computed(() => {
  const sorted = [...anniversaryList.value].filter(a => a.daysUntil >= 0).sort((a, b) => a.daysUntil - b.daysUntil)
  const next = sorted.length > 0 ? sorted[0] : anniversaryList.value[0]
  return formatDate(next.date)
})

// ========== 倒计时 ==========
const countdown = ref({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0
})

const countdownItems = ref([
  { key: 'days', label: '天' },
  { key: 'hours', label: '时' },
  { key: 'minutes', label: '分' },
  { key: 'seconds', label: '秒' }
])

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

// ========== 甜蜜语录 ==========
const sweetQuotes = ref([
  "遇见你是我这辈子最美丽的意外，感谢上天让我遇到了你。",
  "我想和你一起慢慢变老，直到我们老得哪儿也去不了，我还依然把你当成手心里的宝。",
  "你的笑容是我见过最美的风景，你的声音是我听过最动听的旋律。",
  "我爱你，不是因为你是谁，而是因为和你在一起时，我变成了最好的自己。",
  "每一天有你的陪伴，都是我生命中最珍贵的礼物。",
  "我愿意用一生的时间，来证明我对你的爱有多深。",
  "你是我心中最柔软的角落，任何人都无法替代。",
  "和你在一起的每一分每一秒，都是我最幸福的时光。",
  "春风十里不如你，夏阳满山不如你，秋雨淅沥不如你，冬雪皑皑不如你，心中满眼都是你。",
  "我想去看世界的每一个角落，但更想和你一起走过每一个春夏秋冬。"
])

const currentQuoteIndex = ref(0)
const currentQuote = computed(() => sweetQuotes.value[currentQuoteIndex.value])

const prevQuote = () => {
  currentQuoteIndex.value = (currentQuoteIndex.value - 1 + sweetQuotes.value.length) % sweetQuotes.value.length
}

const nextQuote = () => {
  currentQuoteIndex.value = (currentQuoteIndex.value + 1) % sweetQuotes.value.length
}

// ========== 相册照片 ==========
const albumPhotos = ref([
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=romantic%20couple%20walking%20on%20beach%20golden%20sunset%20holding%20hands%20love&image_size=square', 
    alt: '海边散步', 
    description: '海边的浪漫日落',
    size: 'large'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20having%20romantic%20candlelight%20dinner%20wine%20glasses%20cozy%20atmosphere&image_size=square', 
    alt: '烛光晚餐', 
    description: '第一次烛光晚餐',
    size: 'medium'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20traveling%20mountains%20beautiful%20scenery%20happy%20adventure&image_size=square', 
    alt: '旅行合照', 
    description: '一起看过的风景',
    size: 'medium'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20holding%20hands%20cozy%20coffee%20shop%20warm%20afternoon%20sunlight&image_size=square', 
    alt: '咖啡店', 
    description: '温馨的午后时光',
    size: 'large'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20celebrating%20birthday%20cake%20happy%20surprise%20party&image_size=square', 
    alt: '生日庆祝', 
    description: '陪你度过的生日',
    size: 'medium'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20watching%20stars%20night%20sky%20romantic%20moment%20together&image_size=square', 
    alt: '看星星', 
    description: '一起看星空的夜晚',
    size: 'medium'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20picnic%20in%20park%20flowers%20spring%20romantic%20date&image_size=square', 
    alt: '公园野餐', 
    description: '春日浪漫野餐',
    size: 'large'
  },
  { 
    url: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20dancing%20in%20rain%20romantic%20movie%20scene%20happy&image_size=square', 
    alt: '雨中漫步', 
    description: '雨中的浪漫舞蹈',
    size: 'medium'
  }
])

// ========== 十二星座数据 ==========
const zodiacSigns = ref([
  {
    name: '白羊座',
    symbol: '♈',
    dateRange: '3.21 - 4.19',
    element: '火象星座',
    lucky: {
      number: 9,
      color: { name: '红色', hex: '#FF6B6B' },
      direction: '东方',
      tip: '今天适合大胆行动，你的热情会带来好运！'
    },
    personality: {
      strengths: '热情、勇敢、自信、直率、乐观、有领导力',
      weaknesses: '冲动、急躁、自我、粗心、缺乏耐心',
      style: '白羊座的人做事雷厉风行，喜欢直接了当，不喜欢拖泥带水。他们充满活力，勇于挑战，是天生的领导者。',
      strengthsList: ['热情', '勇敢', '自信', '直率', '乐观'],
      weaknessesList: ['冲动', '急躁', '粗心', '自我', '没耐心']
    },
    quotes: ['热情如火，勇往直前', '我思故我在，我行故我胜', '生命不息，奋斗不止']
  },
  {
    name: '金牛座',
    symbol: '♉',
    dateRange: '4.20 - 5.20',
    element: '土象星座',
    lucky: {
      number: 6,
      color: { name: '绿色', hex: '#4CAF50' },
      direction: '东北方',
      tip: '稳扎稳打，今天的你运势稳健，适合理财规划。'
    },
    personality: {
      strengths: '稳重、可靠、耐心、务实、忠诚、有毅力',
      weaknesses: '固执、保守、占有欲强、吝啬、过于敏感',
      style: '金牛座的人做事稳重踏实，喜欢循序渐进，不喜欢冒险。他们注重物质享受，对美食和艺术有独特品味。',
      strengthsList: ['稳重', '可靠', '耐心', '务实', '忠诚'],
      weaknessesList: ['固执', '保守', '敏感', '吝啬', '占有欲']
    },
    quotes: ['稳中求胜，厚积薄发', '坚持就是胜利', '美食与爱不可辜负']
  },
  {
    name: '双子座',
    symbol: '♊',
    dateRange: '5.21 - 6.21',
    element: '风象星座',
    lucky: {
      number: 5,
      color: { name: '黄色', hex: '#FFEB3B' },
      direction: '西方',
      tip: '今天社交运势极佳，多与人交流会有意外收获。'
    },
    personality: {
      strengths: '聪明、机智、灵活、善于沟通、适应力强、好奇心强',
      weaknesses: '善变、肤浅、浮躁、缺乏耐心、容易分心',
      style: '双子座的人思维敏捷，善于变通，喜欢新鲜事物。他们口才出众，社交能力强，是聚会中的活跃分子。',
      strengthsList: ['聪明', '机智', '灵活', '沟通', '好奇'],
      weaknessesList: ['善变', '浮躁', '分心', '肤浅', '没耐心']
    },
    quotes: ['信息就是力量', '变化是唯一的不变', '沟通创造价值']
  },
  {
    name: '巨蟹座',
    symbol: '♋',
    dateRange: '6.22 - 7.22',
    element: '水象星座',
    lucky: {
      number: 2,
      color: { name: '银色', hex: '#C0C0C0' },
      direction: '北方',
      tip: '今天家庭运势旺盛，与家人共度温馨时光吧。'
    },
    personality: {
      strengths: '温柔、体贴、顾家、有同情心、记忆力强、直觉敏锐',
      weaknesses: '敏感、多疑、情绪化、恋旧、缺乏安全感',
      style: '巨蟹座的人内心柔软，重视家庭和亲情。他们善于照顾他人，总能给予最温暖的关怀。',
      strengthsList: ['温柔', '体贴', '顾家', '同情', '直觉'],
      weaknessesList: ['敏感', '情绪化', '多疑', '恋旧', '没安全感']
    },
    quotes: ['家是心灵的港湾', '温柔是最强大的力量', '回忆是最珍贵的财富']
  },
  {
    name: '狮子座',
    symbol: '♌',
    dateRange: '7.23 - 8.22',
    element: '火象星座',
    lucky: {
      number: 1,
      color: { name: '金色', hex: '#FFD700' },
      direction: '西南方',
      tip: '今天领导力运势强，适合展现你的魅力和才华。'
    },
    personality: {
      strengths: '自信、大方、热情、有魅力、创造力强、慷慨',
      weaknesses: '自负、虚荣、骄傲、控制欲强、容易受伤',
      style: '狮子座的人天生自带光环，喜欢成为焦点。他们慷慨大方，对朋友讲义气。',
      strengthsList: ['自信', '大方', '热情', '魅力', '慷慨'],
      weaknessesList: ['自负', '虚荣', '骄傲', '控制欲', '脆弱']
    },
    quotes: ['我是自己人生的主角', '魅力是最好的名片', '光芒万丈，照耀他人']
  },
  {
    name: '处女座',
    symbol: '♍',
    dateRange: '8.23 - 9.22',
    element: '土象星座',
    lucky: {
      number: 7,
      color: { name: '灰色', hex: '#9E9E9E' },
      direction: '西北方',
      tip: '今天细节运势极佳，注意细节会带来意外好运。'
    },
    personality: {
      strengths: '细心、认真、负责、有条理、分析能力强、追求完美',
      weaknesses: '挑剔、洁癖、过于严肃、容易焦虑、追求完美过度',
      style: '处女座的人做事一丝不苟，注重细节，追求完美。他们善于分析问题，总能给出实用的建议。',
      strengthsList: ['细心', '认真', '负责', '条理', '完美'],
      weaknessesList: ['挑剔', '焦虑', '严肃', '洁癖', '过度']
    },
    quotes: ['细节决定成败', '完美是一种态度', '秩序创造效率']
  },
  {
    name: '天秤座',
    symbol: '♎',
    dateRange: '9.23 - 10.23',
    element: '风象星座',
    lucky: {
      number: 6,
      color: { name: '粉色', hex: '#FFB6C1' },
      direction: '西方',
      tip: '今天人际关系运势极佳，适合社交活动和合作洽谈。'
    },
    personality: {
      strengths: '优雅、公正、善于社交、有审美眼光、追求和谐、合作精神强',
      weaknesses: '优柔寡断、过于追求平衡、容易妥协、依赖性强',
      style: '天秤座的人追求和谐与美，善于在人际关系中找到平衡点。他们优雅大方，审美品味出众。',
      strengthsList: ['优雅', '公正', '社交', '审美', '和谐'],
      weaknessesList: ['犹豫', '妥协', '依赖', '纠结', '没主见']
    },
    quotes: ['和谐是最美的风景', '优雅是一种生活态度', '平衡创造美好']
  },
  {
    name: '天蝎座',
    symbol: '♏',
    dateRange: '10.24 - 11.22',
    element: '水象星座',
    lucky: {
      number: 8,
      color: { name: '黑色', hex: '#212121' },
      direction: '北方',
      tip: '今天直觉运势超强，相信你的第六感会带来好运。'
    },
    personality: {
      strengths: '神秘、魅力、直觉敏锐、意志力强、忠诚、有深度',
      weaknesses: '多疑、报复心强、过于执着、控制欲强、容易走极端',
      style: '天蝎座的人内心深邃，神秘莫测。他们对感情极其忠诚，一旦认定就会全力以赴。',
      strengthsList: ['神秘', '魅力', '直觉', '意志', '忠诚'],
      weaknessesList: ['多疑', '执着', '控制欲', '极端', '报复心']
    },
    quotes: ['深度是最高的魅力', '直觉是灵魂的语言', '专注创造奇迹']
  },
  {
    name: '射手座',
    symbol: '♐',
    dateRange: '11.23 - 12.21',
    element: '火象星座',
    lucky: {
      number: 3,
      color: { name: '紫色', hex: '#9C27B0' },
      direction: '南方',
      tip: '今天探索运势极佳，适合学习新知识和旅行。'
    },
    personality: {
      strengths: '乐观、自由、热情、诚实、有幽默感、热爱冒险',
      weaknesses: '鲁莽、粗心、过于乐观、缺乏耐心、容易冲动',
      style: '射手座的人热爱自由，喜欢探索未知的世界。他们乐观开朗，总能给身边的人带来快乐。',
      strengthsList: ['乐观', '自由', '热情', '诚实', '冒险'],
      weaknessesList: ['鲁莽', '粗心', '冲动', '没耐心', '过度']
    },
    quotes: ['自由是灵魂的氧气', '世界那么大，我想去看看', '乐观是最好的良药']
  },
  {
    name: '摩羯座',
    symbol: '♑',
    dateRange: '12.22 - 1.19',
    element: '土象星座',
    lucky: {
      number: 4,
      color: { name: '棕色', hex: '#795548' },
      direction: '北方',
      tip: '今天事业运势极佳，适合制定长期目标和规划。'
    },
    personality: {
      strengths: '稳重、务实、有野心、责任心强、自制力强、有耐心',
      weaknesses: '过于严肃、悲观、工作狂、冷漠、过于保守',
      style: '摩羯座的人脚踏实地，目标明确，为了成功愿意付出一切努力。他们外表冷静，内心却有着火热的激情。',
      strengthsList: ['稳重', '务实', '野心', '责任', '自律'],
      weaknessesList: ['严肃', '悲观', '冷漠', '保守', '工作狂']
    },
    quotes: ['成功是坚持的结果', '厚积薄发，终成大器', '责任是成熟的标志']
  },
  {
    name: '水瓶座',
    symbol: '♒',
    dateRange: '1.20 - 2.18',
    element: '风象星座',
    lucky: {
      number: 4,
      color: { name: '蓝色', hex: '#2196F3' },
      direction: '东北方',
      tip: '今天创意运势极佳，适合进行创造性工作和思考。'
    },
    personality: {
      strengths: '聪明、创新、独立、有远见、人道主义、思想前卫',
      weaknesses: '叛逆、过于理性、冷漠、固执、难以捉摸',
      style: '水瓶座的人思维独特，喜欢与众不同。他们追求精神上的共鸣，不喜欢被传统束缚。',
      strengthsList: ['聪明', '创新', '独立', '远见', '前卫'],
      weaknessesList: ['叛逆', '冷漠', '固执', '理性', '难捉摸']
    },
    quotes: ['创新是进步的灵魂', '独立思考，自由飞翔', '与众不同就是优势']
  },
  {
    name: '双鱼座',
    symbol: '♓',
    dateRange: '2.19 - 3.20',
    element: '水象星座',
    lucky: {
      number: 7,
      color: { name: '海蓝色', hex: '#00BCD4' },
      direction: '东南方',
      tip: '今天灵感运势极佳，适合艺术创作和冥想。'
    },
    personality: {
      strengths: '浪漫、敏感、富有同情心、有艺术天赋、善良、想象力丰富',
      weaknesses: '过于敏感、逃避现实、容易受伤、缺乏自信、过于依赖',
      style: '双鱼座的人浪漫多情，内心充满梦幻和想象。他们善良体贴，总能感受到他人的情绪。',
      strengthsList: ['浪漫', '敏感', '同情', '艺术', '想象'],
      weaknessesList: ['敏感', '逃避', '脆弱', '依赖', '不自信']
    },
    quotes: ['梦想是最好的养分', '浪漫是生活的仪式感', '善良是最美的底色']
  }
])

// ========== 星座运势 ==========
const zodiacTabs = ref([
  { label: '单人运势', value: 'single' },
  { label: '双人配对', value: 'pair' },
  { label: '占卜抽签', value: 'divination' }
])

const activeZodiacTab = ref('single')
const selectedPeriod = ref('今日')
const selectedZodiac = ref('白羊座')
const fortunePeriods = ref(['今日', '明日', '本周', '本月'])

// 当前选中的星座
const currentZodiac = computed(() => {
  return zodiacSigns.value.find(z => z.name === selectedZodiac.value)
})

// 选择星座
const selectZodiac = (name) => {
  selectedZodiac.value = name
  currentZodiacQuoteIndex.value = 0
}

// 星座专属语录
const currentZodiacQuoteIndex = ref(0)

const currentZodiacQuotes = computed(() => {
  return currentZodiac.value ? currentZodiac.value.quotes : []
})

const currentZodiacQuote = computed(() => {
  return currentZodiacQuotes.value[currentZodiacQuoteIndex.value] || ''
})

// 运势数据
const currentFortune = computed(() => {
  const fortunes = {
    '今日': {
      overall: '今天整体运势平稳，适合处理日常事务。保持积极的心态，好运自然会降临。工作中可能会遇到一些小挑战，但凭借你的能力一定能够顺利解决。人际关系方面，保持谦逊和友善会让你获得更多支持。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 75, desc: '工作中会有新的机会出现，保持专注，抓住机遇。领导可能会交给你重要任务，这是展现能力的好时机。' },
        love: { name: '爱情', icon: '💕', score: 82, desc: '感情运势不错，适合与伴侣共度温馨时光。单身者有机会遇到心仪对象，多参加社交活动。' },
        wealth: { name: '财运', icon: '💰', score: 68, desc: '财务状况稳定，不宜进行大额投资。可以考虑稳健的理财方式，避免冲动消费。' },
        health: { name: '健康', icon: '💪', score: 85, desc: '身体状态良好，适当运动有助于保持活力。注意饮食均衡，多喝水。' }
      }
    },
    '明日': {
      overall: '明天运势有所上升，适合开展新的计划和项目。你的思维清晰，能够做出明智的决策。人际关系方面也会有不错的进展，可能会认识新朋友或建立新的合作关系。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 85, desc: '工作效率高，适合处理重要事务。团队合作会很顺利，多与同事沟通交流。' },
        love: { name: '爱情', icon: '💕', score: 78, desc: '有伴者感情升温，适合安排约会。单身者魅力四射，容易吸引异性注意。' },
        wealth: { name: '财运', icon: '💰', score: 72, desc: '财运平稳，可以考虑理财规划。可能会有意外的小收入。' },
        health: { name: '健康', icon: '💪', score: 75, desc: '注意劳逸结合，保证充足睡眠。适当放松心情。' }
      }
    },
    '本周': {
      overall: '本周整体运势向好，各项事务进展顺利。你会有更多的机会展现自己的才华。保持谦逊和努力，成功就在眼前。本周适合制定计划和目标，为未来做好准备。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 88, desc: '本周事业运极佳，有望获得晋升或加薪机会。多关注行业动态，把握发展机遇。' },
        love: { name: '爱情', icon: '💕', score: 90, desc: '感情甜蜜，适合安排浪漫约会。有伴者可以计划短途旅行，增进感情。' },
        wealth: { name: '财运', icon: '💰', score: 80, desc: '财运亨通，投资方面可能有惊喜。适合进行财务规划和投资决策。' },
        health: { name: '健康', icon: '💪', score: 82, desc: '精力充沛，但要注意不要过度劳累。保持规律作息。' }
      }
    },
    '本月': {
      overall: '本月是充满机遇的一个月。你将面临一些重要的抉择，但不要害怕，跟随内心的声音。保持积极向上的态度，好运会相伴左右。本月适合拓展人脉和学习新技能。',
      dimensions: {
        career: { name: '事业', icon: '💼', score: 82, desc: '本月事业发展顺利，适合制定长期目标。可能会有重要的项目或合作机会。' },
        love: { name: '爱情', icon: '💕', score: 85, desc: '感情稳定发展，适合深入交流。有伴者可以考虑共同规划未来。' },
        wealth: { name: '财运', icon: '💰', score: 78, desc: '财务状况稳定增长，适合稳健投资。注意控制支出。' },
        health: { name: '健康', icon: '💪', score: 79, desc: '注意饮食健康，保持规律作息。适当运动增强体质。' }
      }
    }
  }
  
  return fortunes[selectedPeriod.value]
})

// ========== 双人配对 ==========
const showFemaleDropdown = ref(false)
const showMaleDropdown = ref(false)
const selectedFemaleZodiac = ref('双鱼座')
const selectedMaleZodiac = ref('天蝎座')
const pairResult = ref(null)

const femaleZodiac = computed(() => {
  return zodiacSigns.value.find(z => z.name === selectedFemaleZodiac.value) || zodiacSigns.value[0]
})

const maleZodiac = computed(() => {
  return zodiacSigns.value.find(z => z.name === selectedMaleZodiac.value) || zodiacSigns.value[0]
})

const selectFemaleZodiac = (name) => {
  selectedFemaleZodiac.value = name
  showFemaleDropdown.value = false
  pairResult.value = null
}

const selectMaleZodiac = (name) => {
  selectedMaleZodiac.value = name
  showMaleDropdown.value = false
  pairResult.value = null
}

// 星座配对数据
const zodiacCompatibility = {
  '白羊座': {
    '白羊座': { compatibility: 85, love: 80, communication: 85, harmony: 82, value: 88, level: '天作之合', analysis: '两个火象星座的组合，热情如火，充满活力。你们在一起会有说不完的话，做不完的事。', advice: '注意控制各自的脾气，多一些耐心和理解。' },
    '金牛座': { compatibility: 70, love: 72, communication: 68, harmony: 65, value: 75, level: '需要磨合', analysis: '火象与土象的组合，一动一静，一快一慢。需要找到平衡点。', advice: '学会欣赏对方的稳重，对方也需要理解你的热情。' },
    '双子座': { compatibility: 78, love: 82, communication: 85, harmony: 70, value: 75, level: '欢乐情侣', analysis: '火象与风象的组合，充满趣味和变化。你们在一起永远不会无聊。', advice: '保持新鲜感，多给对方一些自由空间。' },
    '巨蟹座': { compatibility: 65, love: 70, communication: 60, harmony: 65, value: 68, level: '需要包容', analysis: '火象与水象的组合，性格差异较大。需要更多的包容和理解。', advice: '学会照顾对方的情绪，对方也需要学会表达自己。' },
    '狮子座': { compatibility: 88, love: 90, communication: 85, harmony: 82, value: 88, level: '热情如火', analysis: '两个火象星座的组合，如同火焰相遇，激情四射。你们互相欣赏，彼此激励。', advice: '学会给对方留面子，不要太强势。' },
    '处女座': { compatibility: 62, love: 65, communication: 58, harmony: 60, value: 65, level: '互补学习', analysis: '火象与土象的组合，性格差异明显。可以从对方身上学到很多。', advice: '学会慢下来，对方也需要学会放开自己。' },
    '天秤座': { compatibility: 75, love: 78, communication: 80, harmony: 72, value: 70, level: '优雅配对', analysis: '火象与风象的组合，优雅与热情的碰撞。', advice: '学会平衡，对方也需要学会果断。' },
    '天蝎座': { compatibility: 72, love: 85, communication: 65, harmony: 68, value: 70, level: '爱恨交织', analysis: '火象与水象的组合，激情四射，但也容易产生矛盾。', advice: '学会控制情绪，多一些信任和沟通。' },
    '射手座': { compatibility: 92, love: 95, communication: 90, harmony: 88, value: 95, level: '灵魂伴侣', analysis: '两个火象星座的完美组合，热爱自由，追求梦想。', advice: '给彼此足够的自由空间，一起探索世界。' },
    '摩羯座': { compatibility: 58, love: 60, communication: 55, harmony: 58, value: 60, level: '需要努力', analysis: '火象与土象的组合，性格差异较大。需要更多的努力和包容。', advice: '学会理解对方的务实，对方也需要学会享受当下。' },
    '水瓶座': { compatibility: 76, love: 72, communication: 85, harmony: 70, value: 78, level: '有趣组合', analysis: '火象与风象的组合，充满创意和惊喜。', advice: '尊重对方的独特性，保持沟通。' },
    '双鱼座': { compatibility: 68, love: 75, communication: 62, harmony: 68, value: 65, level: '浪漫配对', analysis: '火象与水象的组合，热情与浪漫的结合。', advice: '学会照顾对方的情绪，对方也需要学会表达需求。' }
  },
  '金牛座': {
    '白羊座': { compatibility: 70, love: 72, communication: 68, harmony: 65, value: 75, level: '需要磨合', analysis: '土象与火象的组合，稳重与热情的碰撞。', advice: '学会欣赏对方的活力，对方也需要理解你的稳重。' },
    '金牛座': { compatibility: 90, love: 88, communication: 92, harmony: 95, value: 85, level: '稳定伴侣', analysis: '两个土象星座的组合，稳重踏实，追求稳定。', advice: '保持浪漫，不要让生活太平淡。' },
    '双子座': { compatibility: 65, love: 68, communication: 70, harmony: 58, value: 65, level: '差异组合', analysis: '土象与风象的组合，稳重与多变的矛盾。', advice: '学会适应变化，对方也需要学会稳定。' },
    '巨蟹座': { compatibility: 85, love: 88, communication: 80, harmony: 85, value: 87, level: '温馨组合', analysis: '土象与水象的组合，稳重与温柔的完美结合。', advice: '多表达感情，不要太沉默。' },
    '狮子座': { compatibility: 68, love: 75, communication: 62, harmony: 65, value: 70, level: '需要包容', analysis: '土象与火象的组合，低调与张扬的矛盾。', advice: '学会欣赏对方的光芒，对方也需要学会低调。' },
    '处女座': { compatibility: 92, love: 85, communication: 90, harmony: 92, value: 95, level: '完美搭档', analysis: '两个土象星座的组合，注重细节，追求完美。', advice: '不要太挑剔，学会包容对方的小缺点。' },
    '天秤座': { compatibility: 75, love: 78, communication: 72, harmony: 75, value: 75, level: '优雅组合', analysis: '土象与风象的组合，稳重与优雅的结合。', advice: '学会表达，不要太内向。' },
    '天蝎座': { compatibility: 88, love: 92, communication: 80, harmony: 85, value: 90, level: '深情配对', analysis: '土象与水象的组合，稳重与深情的完美结合。', advice: '保持信任，不要有太多猜忌。' },
    '射手座': { compatibility: 55, love: 60, communication: 50, harmony: 52, value: 58, level: '需要努力', analysis: '土象与火象的组合，稳重与自由的矛盾。', advice: '给对方更多自由，对方也需要学会稳定。' },
    '摩羯座': { compatibility: 95, love: 88, communication: 92, harmony: 95, value: 95, level: '事业伙伴', analysis: '两个土象星座的组合，务实稳重，目标一致。', advice: '多关注感情，不要太专注于工作。' },
    '水瓶座': { compatibility: 58, love: 55, communication: 65, harmony: 52, value: 60, level: '差异较大', analysis: '土象与风象的组合，传统与前卫的矛盾。', advice: '学会欣赏对方的独特，保持尊重。' },
    '双鱼座': { compatibility: 78, love: 82, communication: 72, harmony: 78, value: 80, level: '浪漫组合', analysis: '土象与水象的组合，稳重与浪漫的结合。', advice: '学会表达感情，对方也需要学会现实。' }
  },
  '双子座': {
    '白羊座': { compatibility: 78, love: 82, communication: 85, harmony: 70, value: 75, level: '欢乐情侣', analysis: '风象与火象的组合，充满趣味和活力。', advice: '保持新鲜感，多一些耐心。' },
    '金牛座': { compatibility: 65, love: 68, communication: 70, harmony: 58, value: 65, level: '差异组合', analysis: '风象与土象的组合，多变与稳重的矛盾。', advice: '学会稳定，对方也需要学会变通。' },
    '双子座': { compatibility: 75, love: 70, communication: 95, harmony: 65, value: 75, level: '聊天达人', analysis: '两个风象星座的组合，沟通无障碍，但可能缺乏深度。', advice: '多关注对方的内心，不要只停留在表面。' },
    '巨蟹座': { compatibility: 60, love: 65, communication: 55, harmony: 60, value: 62, level: '需要包容', analysis: '风象与水象的组合，理性与感性的矛盾。', advice: '学会照顾对方情绪，对方也需要学会表达。' },
    '狮子座': { compatibility: 82, love: 85, communication: 88, harmony: 75, value: 80, level: '闪耀组合', analysis: '风象与火象的组合，风趣与热情的结合。', advice: '学会赞美对方，保持热情。' },
    '处女座': { compatibility: 68, love: 65, communication: 75, harmony: 62, value: 70, level: '互补学习', analysis: '风象与土象的组合，灵活与谨慎的矛盾。', advice: '学会专注，对方也需要学会灵活。' },
    '天秤座': { compatibility: 85, love: 82, communication: 90, harmony: 80, value: 88, level: '优雅配对', analysis: '两个风象星座的组合，优雅风趣，社交达人。', advice: '不要太优柔寡断，学会做决定。' },
    '天蝎座': { compatibility: 55, love: 60, communication: 50, harmony: 55, value: 55, level: '差异较大', analysis: '风象与水象的组合，理性与感性的矛盾。', advice: '学会深入，对方也需要学会放开。' },
    '射手座': { compatibility: 88, love: 85, communication: 92, harmony: 82, value: 90, level: '灵魂伴侣', analysis: '风象与火象的组合，充满活力和探索精神。', advice: '一起探索世界，保持好奇心。' },
    '摩羯座': { compatibility: 58, love: 55, communication: 65, harmony: 52, value: 60, level: '需要努力', analysis: '风象与土象的组合，多变与稳重的矛盾。', advice: '学会稳定，对方也需要学会灵活。' },
    '水瓶座': { compatibility: 85, love: 80, communication: 92, harmony: 78, value: 88, level: '精神伴侣', analysis: '两个风象星座的组合，思想前卫，追求自由。', advice: '尊重彼此的独特性，保持精神交流。' },
    '双鱼座': { compatibility: 68, love: 75, communication: 62, harmony: 68, value: 65, level: '梦幻组合', analysis: '风象与水象的组合，理性与浪漫的结合。', advice: '学会表达情感，对方也需要学会现实。' }
  },
  '巨蟹座': {
    '白羊座': { compatibility: 65, love: 70, communication: 60, harmony: 65, value: 68, level: '需要包容', analysis: '水象与火象的组合，温柔与热情的碰撞。', advice: '学会表达需求，对方也需要学会温柔。' },
    '金牛座': { compatibility: 85, love: 88, communication: 80, harmony: 85, value: 87, level: '温馨组合', analysis: '水象与土象的组合，温柔与稳重的完美结合。', advice: '多沟通需求，不要默默承受。' },
    '双子座': { compatibility: 60, love: 65, communication: 55, harmony: 60, value: 62, level: '需要包容', analysis: '水象与风象的组合，感性与理性的矛盾。', advice: '学会直接表达，对方也需要学会体贴。' },
    '巨蟹座': { compatibility: 90, love: 92, communication: 85, harmony: 90, value: 88, level: '温馨伴侣', analysis: '两个水象星座的组合，温柔体贴，彼此理解。', advice: '不要太敏感，多一些信任。' },
    '狮子座': { compatibility: 72, love: 78, communication: 65, harmony: 70, value: 75, level: '互补组合', analysis: '水象与火象的组合，温柔与热情的互补。', advice: '学会欣赏对方的热烈，对方也需要学会温柔。' },
    '处女座': { compatibility: 82, love: 80, communication: 78, harmony: 85, value: 82, level: '踏实组合', analysis: '水象与土象的组合，温柔与细心的结合。', advice: '不要太挑剔，学会包容。' },
    '天秤座': { compatibility: 70, love: 75, communication: 68, harmony: 68, value: 70, level: '优雅组合', analysis: '水象与风象的组合，温柔与优雅的结合。', advice: '学会做决定，对方也需要学会体贴。' },
    '天蝎座': { compatibility: 95, love: 98, communication: 88, harmony: 92, value: 95, level: '灵魂伴侣', analysis: '两个水象星座的组合，深情款款，灵魂共鸣。', advice: '保持信任，不要猜忌。' },
    '射手座': { compatibility: 60, love: 68, communication: 55, harmony: 58, value: 62, level: '需要努力', analysis: '水象与火象的组合，稳定与自由的矛盾。', advice: '给对方空间，对方也需要学会安定。' },
    '摩羯座': { compatibility: 78, love: 75, communication: 72, harmony: 80, value: 78, level: '稳定组合', analysis: '水象与土象的组合，温柔与稳重的结合。', advice: '多表达感情，不要太沉默。' },
    '水瓶座': { compatibility: 58, love: 62, communication: 55, harmony: 55, value: 58, level: '差异较大', analysis: '水象与风象的组合，感性与理性的矛盾。', advice: '学会独立，对方也需要学会体贴。' },
    '双鱼座': { compatibility: 92, love: 95, communication: 88, harmony: 90, value: 92, level: '梦幻组合', analysis: '两个水象星座的组合，浪漫至极，心灵相通。', advice: '不要太梦幻，要面对现实。' }
  },
  '狮子座': {
    '白羊座': { compatibility: 88, love: 90, communication: 85, harmony: 82, value: 88, level: '热情如火', analysis: '两个火象星座的组合，如同太阳相遇，光芒万丈。', advice: '学会谦让，不要抢风头。' },
    '金牛座': { compatibility: 68, love: 75, communication: 62, harmony: 65, value: 70, level: '需要包容', analysis: '火象与土象的组合，张扬与低调的矛盾。', advice: '学会欣赏对方的稳重，对方也需要学会热情。' },
    '双子座': { compatibility: 82, love: 85, communication: 88, harmony: 75, value: 80, level: '闪耀组合', analysis: '火象与风象的组合，热情与风趣的完美结合。', advice: '保持新鲜感，多赞美对方。' },
    '巨蟹座': { compatibility: 72, love: 78, communication: 65, harmony: 70, value: 75, level: '互补组合', analysis: '火象与水象的组合，热烈与温柔的互补。', advice: '学会温柔体贴，对方也需要学会热情。' },
    '狮子座': { compatibility: 85, love: 82, communication: 78, harmony: 75, value: 85, level: '王者组合', analysis: '两个火象星座的组合，如同双日同辉，光芒四射。', advice: '学会给对方空间，不要太强势。' },
    '处女座': { compatibility: 65, love: 68, communication: 60, harmony: 62, value: 65, level: '需要磨合', analysis: '火象与土象的组合，张扬与内敛的矛盾。', advice: '学会耐心，对方也需要学会放开。' },
    '天秤座': { compatibility: 80, love: 82, communication: 85, harmony: 78, value: 78, level: '优雅组合', analysis: '火象与风象的组合，热情与优雅的结合。', advice: '学会优雅表达，对方也需要学会果断。' },
    '天蝎座': { compatibility: 75, love: 82, communication: 68, harmony: 70, value: 75, level: '爱恨交织', analysis: '火象与水象的组合，热情与深情的碰撞。', advice: '学会控制情绪，多一些信任。' },
    '射手座': { compatibility: 90, love: 92, communication: 90, harmony: 88, value: 90, level: '自由伴侣', analysis: '两个火象星座的组合，热爱自由，追求梦想。', advice: '给彼此自由，一起探索世界。' },
    '摩羯座': { compatibility: 62, love: 65, communication: 58, harmony: 60, value: 65, level: '需要努力', analysis: '火象与土象的组合，张扬与稳重的矛盾。', advice: '学会务实，对方也需要学会热情。' },
    '水瓶座': { compatibility: 78, love: 75, communication: 82, harmony: 72, value: 78, level: '独特组合', analysis: '火象与风象的组合，热情与独特的结合。', advice: '尊重对方的独特，保持热情。' },
    '双鱼座': { compatibility: 70, love: 78, communication: 65, harmony: 68, value: 70, level: '浪漫组合', analysis: '火象与水象的组合，热情与浪漫的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '处女座': {
    '白羊座': { compatibility: 62, love: 65, communication: 58, harmony: 60, value: 65, level: '互补学习', analysis: '土象与火象的组合，谨慎与热情的矛盾。', advice: '学会放开，对方也需要学会谨慎。' },
    '金牛座': { compatibility: 92, love: 85, communication: 90, harmony: 92, value: 95, level: '完美搭档', analysis: '两个土象星座的组合，注重细节，追求完美。', advice: '不要太挑剔，学会包容。' },
    '双子座': { compatibility: 68, love: 65, communication: 75, harmony: 62, value: 70, level: '互补学习', analysis: '土象与风象的组合，谨慎与灵活的矛盾。', advice: '学会灵活，对方也需要学会专注。' },
    '巨蟹座': { compatibility: 82, love: 80, communication: 78, harmony: 85, value: 82, level: '踏实组合', analysis: '土象与水象的组合，细心与温柔的完美结合。', advice: '多表达感情，不要太内敛。' },
    '狮子座': { compatibility: 65, love: 68, communication: 60, harmony: 62, value: 65, level: '需要磨合', analysis: '土象与火象的组合，内敛与张扬的矛盾。', advice: '学会欣赏对方，对方也需要学会耐心。' },
    '处女座': { compatibility: 88, love: 82, communication: 90, harmony: 88, value: 88, level: '完美组合', analysis: '两个土象星座的组合，细致入微，追求完美。', advice: '不要太苛求，学会放松。' },
    '天秤座': { compatibility: 75, love: 72, communication: 78, harmony: 70, value: 75, level: '优雅组合', analysis: '土象与风象的组合，谨慎与优雅的结合。', advice: '学会做决定，不要太纠结。' },
    '天蝎座': { compatibility: 85, love: 88, communication: 80, harmony: 85, value: 88, level: '深情组合', analysis: '土象与水象的组合，谨慎与深情的结合。', advice: '保持信任，不要猜忌。' },
    '射手座': { compatibility: 58, love: 62, communication: 55, harmony: 55, value: 58, level: '需要努力', analysis: '土象与火象的组合，谨慎与自由的矛盾。', advice: '学会放开，对方也需要学会稳定。' },
    '摩羯座': { compatibility: 90, love: 85, communication: 92, harmony: 90, value: 90, level: '务实搭档', analysis: '两个土象星座的组合，务实稳重，目标明确。', advice: '多关注感情，不要太专注于工作。' },
    '水瓶座': { compatibility: 60, love: 58, communication: 65, harmony: 58, value: 62, level: '差异较大', analysis: '土象与风象的组合，传统与前卫的矛盾。', advice: '学会欣赏独特，对方也需要学会稳定。' },
    '双鱼座': { compatibility: 78, love: 82, communication: 75, harmony: 78, value: 78, level: '梦幻组合', analysis: '土象与水象的组合，现实与梦幻的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '天秤座': {
    '白羊座': { compatibility: 75, love: 78, communication: 80, harmony: 72, value: 70, level: '优雅配对', analysis: '风象与火象的组合，优雅与热情的碰撞。', advice: '学会果断，对方也需要学会优雅。' },
    '金牛座': { compatibility: 75, love: 78, communication: 72, harmony: 75, value: 75, level: '优雅组合', analysis: '风象与土象的组合，优雅与稳重的结合。', advice: '学会务实，对方也需要学会优雅。' },
    '双子座': { compatibility: 85, love: 82, communication: 90, harmony: 80, value: 88, level: '优雅配对', analysis: '两个风象星座的组合，优雅风趣，社交达人。', advice: '不要太犹豫，学会做决定。' },
    '巨蟹座': { compatibility: 70, love: 75, communication: 68, harmony: 68, value: 70, level: '优雅组合', analysis: '风象与水象的组合，优雅与温柔的结合。', advice: '学会体贴，对方也需要学会直接。' },
    '狮子座': { compatibility: 80, love: 82, communication: 85, harmony: 78, value: 78, level: '优雅组合', analysis: '风象与火象的组合，优雅与热情的结合。', advice: '学会赞美，保持优雅。' },
    '处女座': { compatibility: 75, love: 72, communication: 78, harmony: 70, value: 75, level: '优雅组合', analysis: '风象与土象的组合，优雅与谨慎的结合。', advice: '学会做决定，不要太纠结。' },
    '天秤座': { compatibility: 82, love: 78, communication: 85, harmony: 78, value: 82, level: '优雅伴侣', analysis: '两个风象星座的组合，优雅和谐，追求平衡。', advice: '不要太优柔寡断，学会果断。' },
    '天蝎座': { compatibility: 68, love: 72, communication: 65, harmony: 65, value: 68, level: '需要磨合', analysis: '风象与水象的组合，理性与感性的矛盾。', advice: '学会深入，对方也需要学会放开。' },
    '射手座': { compatibility: 82, love: 80, communication: 85, harmony: 78, value: 82, level: '自由组合', analysis: '风象与火象的组合，优雅与自由的结合。', advice: '一起探索，保持好奇心。' },
    '摩羯座': { compatibility: 65, love: 62, communication: 68, harmony: 65, value: 65, level: '需要努力', analysis: '风象与土象的组合，优雅与稳重的矛盾。', advice: '学会务实，对方也需要学会优雅。' },
    '水瓶座': { compatibility: 88, love: 85, communication: 90, harmony: 85, value: 88, level: '精神伴侣', analysis: '两个风象星座的组合，思想前卫，追求自由。', advice: '尊重彼此独特，保持精神交流。' },
    '双鱼座': { compatibility: 72, love: 78, communication: 68, harmony: 70, value: 72, level: '梦幻组合', analysis: '风象与水象的组合，优雅与浪漫的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '天蝎座': {
    '白羊座': { compatibility: 72, love: 85, communication: 65, harmony: 68, value: 70, level: '爱恨交织', analysis: '水象与火象的组合，深情与热情的碰撞。', advice: '学会控制情绪，多一些信任。' },
    '金牛座': { compatibility: 88, love: 92, communication: 80, harmony: 85, value: 90, level: '深情配对', analysis: '水象与土象的组合，深情与稳重的完美结合。', advice: '保持信任，不要猜忌。' },
    '双子座': { compatibility: 55, love: 60, communication: 50, harmony: 55, value: 55, level: '差异较大', analysis: '水象与风象的组合，深情与多变的矛盾。', advice: '学会放开，对方也需要学会深入。' },
    '巨蟹座': { compatibility: 95, love: 98, communication: 88, harmony: 92, value: 95, level: '灵魂伴侣', analysis: '两个水象星座的组合，深情款款，灵魂共鸣。', advice: '保持信任，不要猜忌。' },
    '狮子座': { compatibility: 75, love: 82, communication: 68, harmony: 70, value: 75, level: '爱恨交织', analysis: '水象与火象的组合，深情与热烈的碰撞。', advice: '学会包容，多一些信任。' },
    '处女座': { compatibility: 85, love: 88, communication: 80, harmony: 85, value: 88, level: '深情组合', analysis: '水象与土象的组合，深情与细心的结合。', advice: '不要太挑剔，学会包容。' },
    '天秤座': { compatibility: 68, love: 72, communication: 65, harmony: 65, value: 68, level: '需要磨合', analysis: '水象与风象的组合，深情与优雅的矛盾。', advice: '学会表达，对方也需要学会体贴。' },
    '天蝎座': { compatibility: 88, love: 90, communication: 82, harmony: 85, value: 88, level: '深情伴侣', analysis: '两个水象星座的组合，深情至极，灵魂相通。', advice: '不要太猜忌，学会信任。' },
    '射手座': { compatibility: 62, love: 70, communication: 58, harmony: 60, value: 62, level: '需要努力', analysis: '水象与火象的组合，稳定与自由的矛盾。', advice: '给对方空间，对方也需要学会安定。' },
    '摩羯座': { compatibility: 80, love: 78, communication: 75, harmony: 82, value: 80, level: '稳定组合', analysis: '水象与土象的组合，深情与稳重的结合。', advice: '多表达感情，不要太沉默。' },
    '水瓶座': { compatibility: 60, love: 65, communication: 55, harmony: 58, value: 60, level: '差异较大', analysis: '水象与风象的组合，感性与理性的矛盾。', advice: '学会独立，对方也需要学会体贴。' },
    '双鱼座': { compatibility: 90, love: 95, communication: 88, harmony: 90, value: 92, level: '梦幻组合', analysis: '两个水象星座的组合，浪漫深情，心灵相通。', advice: '不要太梦幻，要面对现实。' }
  },
  '射手座': {
    '白羊座': { compatibility: 92, love: 95, communication: 90, harmony: 88, value: 95, level: '灵魂伴侣', analysis: '两个火象星座的完美组合，热爱自由，追求梦想。', advice: '给彼此足够的自由空间，一起探索世界。' },
    '金牛座': { compatibility: 55, love: 60, communication: 50, harmony: 52, value: 58, level: '需要努力', analysis: '火象与土象的组合，自由与稳重的矛盾。', advice: '学会稳定，对方也需要学会放开。' },
    '双子座': { compatibility: 88, love: 85, communication: 92, harmony: 82, value: 90, level: '灵魂伴侣', analysis: '火象与风象的组合，充满活力和探索精神。', advice: '一起探索世界，保持好奇心。' },
    '巨蟹座': { compatibility: 60, love: 68, communication: 55, harmony: 58, value: 62, level: '需要努力', analysis: '火象与水象的组合，自由与稳定的矛盾。', advice: '学会安定，对方也需要学会放开。' },
    '狮子座': { compatibility: 90, love: 92, communication: 90, harmony: 88, value: 90, level: '自由伴侣', analysis: '两个火象星座的组合，热爱自由，追求梦想。', advice: '给彼此自由，一起探索世界。' },
    '处女座': { compatibility: 58, love: 62, communication: 55, harmony: 55, value: 58, level: '需要努力', analysis: '火象与土象的组合，自由与谨慎的矛盾。', advice: '学会专注，对方也需要学会放开。' },
    '天秤座': { compatibility: 82, love: 80, communication: 85, harmony: 78, value: 82, level: '自由组合', analysis: '火象与风象的组合，自由与优雅的结合。', advice: '一起探索，保持优雅。' },
    '天蝎座': { compatibility: 62, love: 70, communication: 58, harmony: 60, value: 62, level: '需要努力', analysis: '火象与水象的组合，自由与深情的矛盾。', advice: '学会安定，对方也需要学会放开。' },
    '射手座': { compatibility: 85, love: 82, communication: 88, harmony: 80, value: 85, level: '自由伴侣', analysis: '两个火象星座的组合，热爱自由，追求冒险。', advice: '给彼此自由，不要束缚。' },
    '摩羯座': { compatibility: 55, love: 58, communication: 52, harmony: 55, value: 55, level: '需要努力', analysis: '火象与土象的组合，自由与稳重的矛盾。', advice: '学会务实，对方也需要学会放开。' },
    '水瓶座': { compatibility: 85, love: 80, communication: 88, harmony: 82, value: 85, level: '自由组合', analysis: '火象与风象的组合，自由与独特的结合。', advice: '尊重独特，保持自由。' },
    '双鱼座': { compatibility: 68, love: 75, communication: 65, harmony: 68, value: 68, level: '浪漫组合', analysis: '火象与水象的组合，自由与浪漫的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '摩羯座': {
    '白羊座': { compatibility: 58, love: 60, communication: 55, harmony: 58, value: 60, level: '需要努力', analysis: '土象与火象的组合，稳重与热情的矛盾。', advice: '学会热情，对方也需要学会稳重。' },
    '金牛座': { compatibility: 95, love: 88, communication: 92, harmony: 95, value: 95, level: '事业伙伴', analysis: '两个土象星座的组合，务实稳重，目标一致。', advice: '多关注感情，不要太专注于工作。' },
    '双子座': { compatibility: 58, love: 55, communication: 65, harmony: 52, value: 60, level: '差异较大', analysis: '土象与风象的组合，稳重与多变的矛盾。', advice: '学会灵活，对方也需要学会稳定。' },
    '巨蟹座': { compatibility: 78, love: 75, communication: 72, harmony: 80, value: 78, level: '稳定组合', analysis: '土象与水象的组合，稳重与温柔的结合。', advice: '多表达感情，不要太沉默。' },
    '狮子座': { compatibility: 62, love: 65, communication: 58, harmony: 60, value: 65, level: '需要努力', analysis: '土象与火象的组合，稳重与张扬的矛盾。', advice: '学会热情，对方也需要学会稳重。' },
    '处女座': { compatibility: 90, love: 85, communication: 92, harmony: 90, value: 90, level: '务实搭档', analysis: '两个土象星座的组合，务实稳重，目标明确。', advice: '多关注感情，不要太专注于工作。' },
    '天秤座': { compatibility: 65, love: 62, communication: 68, harmony: 65, value: 65, level: '需要努力', analysis: '土象与风象的组合，稳重与优雅的矛盾。', advice: '学会优雅，对方也需要学会稳重。' },
    '天蝎座': { compatibility: 80, love: 78, communication: 75, harmony: 82, value: 80, level: '稳定组合', analysis: '土象与水象的组合，稳重与深情的结合。', advice: '多表达感情，不要太沉默。' },
    '射手座': { compatibility: 55, love: 58, communication: 52, harmony: 55, value: 55, level: '需要努力', analysis: '土象与火象的组合，稳重与自由的矛盾。', advice: '学会放开，对方也需要学会稳定。' },
    '摩羯座': { compatibility: 88, love: 82, communication: 90, harmony: 88, value: 88, level: '务实伴侣', analysis: '两个土象星座的组合，务实稳重，目标一致。', advice: '多关注感情，不要太专注于工作。' },
    '水瓶座': { compatibility: 58, love: 55, communication: 62, harmony: 55, value: 58, level: '差异较大', analysis: '土象与风象的组合，传统与前卫的矛盾。', advice: '学会欣赏独特，对方也需要学会稳定。' },
    '双鱼座': { compatibility: 75, love: 78, communication: 72, harmony: 75, value: 75, level: '梦幻组合', analysis: '土象与水象的组合，稳重与浪漫的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '水瓶座': {
    '白羊座': { compatibility: 76, love: 72, communication: 85, harmony: 70, value: 78, level: '有趣组合', analysis: '风象与火象的组合，独特与热情的结合。', advice: '尊重独特，保持热情。' },
    '金牛座': { compatibility: 58, love: 55, communication: 65, harmony: 52, value: 60, level: '差异较大', analysis: '风象与土象的组合，前卫与传统的矛盾。', advice: '学会欣赏传统，对方也需要学会开放。' },
    '双子座': { compatibility: 85, love: 80, communication: 92, harmony: 78, value: 88, level: '精神伴侣', analysis: '两个风象星座的组合，思想前卫，追求自由。', advice: '尊重彼此的独特性，保持精神交流。' },
    '巨蟹座': { compatibility: 58, love: 62, communication: 55, harmony: 55, value: 58, level: '差异较大', analysis: '风象与水象的组合，理性与感性的矛盾。', advice: '学会体贴，对方也需要学会独立。' },
    '狮子座': { compatibility: 78, love: 75, communication: 82, harmony: 72, value: 78, level: '独特组合', analysis: '风象与火象的组合，独特与热情的结合。', advice: '尊重独特，保持热情。' },
    '处女座': { compatibility: 60, love: 58, communication: 65, harmony: 58, value: 62, level: '差异较大', analysis: '风象与土象的组合，前卫与传统的矛盾。', advice: '学会欣赏传统，对方也需要学会开放。' },
    '天秤座': { compatibility: 88, love: 85, communication: 90, harmony: 85, value: 88, level: '精神伴侣', analysis: '两个风象星座的组合，思想前卫，追求自由。', advice: '尊重彼此独特，保持精神交流。' },
    '天蝎座': { compatibility: 60, love: 65, communication: 55, harmony: 58, value: 60, level: '差异较大', analysis: '风象与水象的组合，理性与感性的矛盾。', advice: '学会深入，对方也需要学会放开。' },
    '射手座': { compatibility: 85, love: 80, communication: 88, harmony: 82, value: 85, level: '自由组合', analysis: '风象与火象的组合，独特与自由的结合。', advice: '尊重独特，保持自由。' },
    '摩羯座': { compatibility: 58, love: 55, communication: 62, harmony: 55, value: 58, level: '差异较大', analysis: '风象与土象的组合，前卫与传统的矛盾。', advice: '学会欣赏传统，对方也需要学会开放。' },
    '水瓶座': { compatibility: 82, love: 78, communication: 85, harmony: 80, value: 82, level: '独特伴侣', analysis: '两个风象星座的组合，思想前卫，追求独特。', advice: '尊重彼此独特，不要太疏离。' },
    '双鱼座': { compatibility: 70, love: 75, communication: 68, harmony: 68, value: 70, level: '梦幻组合', analysis: '风象与水象的组合，独特与浪漫的结合。', advice: '学会浪漫，对方也需要学会现实。' }
  },
  '双鱼座': {
    '白羊座': { compatibility: 68, love: 75, communication: 62, harmony: 68, value: 65, level: '浪漫配对', analysis: '水象与火象的组合，浪漫与热情的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '金牛座': { compatibility: 78, love: 82, communication: 72, harmony: 78, value: 80, level: '浪漫组合', analysis: '水象与土象的组合，浪漫与稳重的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '双子座': { compatibility: 68, love: 75, communication: 62, harmony: 68, value: 65, level: '梦幻组合', analysis: '水象与风象的组合，浪漫与多变的矛盾。', advice: '学会稳定，对方也需要学会浪漫。' },
    '巨蟹座': { compatibility: 92, love: 95, communication: 88, harmony: 90, value: 92, level: '梦幻组合', analysis: '两个水象星座的组合，浪漫至极，心灵相通。', advice: '不要太梦幻，要面对现实。' },
    '狮子座': { compatibility: 70, love: 78, communication: 65, harmony: 68, value: 70, level: '浪漫组合', analysis: '水象与火象的组合，浪漫与热情的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '处女座': { compatibility: 78, love: 82, communication: 75, harmony: 78, value: 78, level: '梦幻组合', analysis: '水象与土象的组合，浪漫与现实的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '天秤座': { compatibility: 72, love: 78, communication: 68, harmony: 70, value: 72, level: '梦幻组合', analysis: '水象与风象的组合，浪漫与优雅的结合。', advice: '学会优雅，对方也需要学会浪漫。' },
    '天蝎座': { compatibility: 90, love: 95, communication: 88, harmony: 90, value: 92, level: '梦幻组合', analysis: '两个水象星座的组合，浪漫深情，心灵相通。', advice: '不要太梦幻，要面对现实。' },
    '射手座': { compatibility: 68, love: 75, communication: 65, harmony: 68, value: 68, level: '浪漫组合', analysis: '水象与火象的组合，浪漫与自由的矛盾。', advice: '学会安定，对方也需要学会浪漫。' },
    '摩羯座': { compatibility: 75, love: 78, communication: 72, harmony: 75, value: 75, level: '梦幻组合', analysis: '水象与土象的组合，浪漫与稳重的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '水瓶座': { compatibility: 70, love: 75, communication: 68, harmony: 68, value: 70, level: '梦幻组合', analysis: '水象与风象的组合，浪漫与独特的结合。', advice: '学会现实，对方也需要学会浪漫。' },
    '双鱼座': { compatibility: 88, love: 92, communication: 85, harmony: 88, value: 88, level: '梦幻伴侣', analysis: '两个水象星座的组合，浪漫至极，心灵相通。', advice: '不要太梦幻，要面对现实。' }
  }
}

// 计算配对
const calculateCompatibility = () => {
  const female = selectedFemaleZodiac.value
  const male = selectedMaleZodiac.value
  
  let data = zodiacCompatibility[female]?.[male]
  
  if (!data) {
    data = zodiacCompatibility[male]?.[female]
  }
  
  if (!data) {
    data = {
      compatibility: 70,
      love: 72,
      communication: 68,
      harmony: 70,
      value: 70,
      level: '缘分天定',
      analysis: '你们的组合充满可能性，需要更多的了解和沟通。',
      advice: '多花时间了解对方，发现彼此的闪光点。'
    }
  }
  
  pairResult.value = {
    ...data,
    loveScore: data.love,
    communicationScore: data.communication,
    harmonyScore: data.harmony,
    valueScore: data.value
  }
}

// ========== 占卜抽签 ==========
const divinationResult = ref(null)
const showResultPopup = ref(false)

const divinationCards = [
  {
    icon: '🌟',
    title: '大吉',
    luckyNumbers: [3, 7, 9],
    content: '今天运势极佳，诸事顺利。你的努力会得到回报，好运会伴随着你。适合做重要决定，把握机会！'
  },
  {
    icon: '💫',
    title: '中吉',
    luckyNumbers: [2, 5, 8],
    content: '今天运势平稳，保持平常心。适合稳步推进现有计划，不要急于求成。耐心等待，好运自然来。'
  },
  {
    icon: '✨',
    title: '小吉',
    luckyNumbers: [1, 4, 6],
    content: '今天有小惊喜等着你。保持积极心态，注意身边的人和事。可能会有意外的小确幸。'
  },
  {
    icon: '🌙',
    title: '末吉',
    luckyNumbers: [2, 6, 8],
    content: '今天运势平平，但也没有坏运。适合反思和调整，为明天做好准备。保持内心平静，静待时机。'
  },
  {
    icon: '🌈',
    title: '运势上升',
    luckyNumbers: [3, 6, 9],
    content: '你的运势正在上升期。继续保持努力，好运会越来越近。今天适合学习新事物，提升自己。'
  },
  {
    icon: '🍀',
    title: '幸运加持',
    luckyNumbers: [4, 7, 8],
    content: '今天幸运女神眷顾你。适合尝试新事物，大胆行动。你的直觉很准，相信自己的判断！'
  }
]

const drawCard = () => {
  const randomIndex = Math.floor(Math.random() * divinationCards.length)
  divinationResult.value = divinationCards[randomIndex]
  showResultPopup.value = true
}

const closePopup = () => {
  showResultPopup.value = false
}

const shareResult = () => {
  if (divinationResult.value) {
    const text = `我抽到了【${divinationResult.value.title}】！${divinationResult.value.content}`
    alert('分享功能：' + text)
  }
}

// ========== 签名文案 ==========
const signatureText = ref('愿我们的爱情，如同初升的太阳，温暖而耀眼；如同潺潺的溪流，绵长而清澈。每一个与你共度的日子，都是我生命中最美好的礼物。我愿用一生的时间，陪你走过春夏秋冬，看遍世间风景。爱你，是我这辈子做过最好的选择。')

// ========== 工具函数 ==========
const formatDate = (date) => {
  const d = new Date(date)
  const year = d.getFullYear()
  const month = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return `${year}年${month}月${day}日`
}

const padZero = (num) => {
  return String(num).padStart(2, '0')
}

const getHeartStyle = (index) => {
  const positions = ['10%', '25%', '40%', '55%', '70%', '85%', '15%', '75%']
  const delays = ['0s', '0.5s', '1s', '1.5s', '2s', '0.3s', '0.8s', '1.2s']
  const durations = ['3s', '3.5s', '4s', '3s', '3.5s', '4s', '3.2s', '3.8s']
  return {
    left: positions[index],
    animationDelay: delays[index],
    animationDuration: durations[index]
  }
}

const getDaysBadgeClass = (days) => {
  if (days === 0) return 'today'
  if (days > 0 && days <= 7) return 'soon'
  if (days < 0) return 'passed'
  return 'normal'
}

const getScoreClass = (score) => {
  if (score >= 85) return 'excellent'
  if (score >= 70) return 'good'
  if (score >= 55) return 'average'
  return 'needs-improvement'
}

const getCompatibilityClass = (score) => {
  if (score >= 90) return 'perfect'
  if (score >= 80) return 'great'
  if (score >= 70) return 'good'
  if (score >= 60) return 'average'
  return 'needs-work'
}

// ========== 滑动触摸 ==========
const touchStartX = ref(0)
const touchStartY = ref(0)

const handleTouchStart = (e) => {
  touchStartX.value = e.touches[0].clientX
  touchStartY.value = e.touches[0].clientY
}

const handleTouchMove = (e) => {
  // 可以在这里处理滑动逻辑
}

const handleTouchEnd = (e) => {
  // 可以在这里处理滑动结束逻辑
}

// ========== 定时器 ==========
let countdownInterval = null
let quoteInterval = null
let zodiacQuoteInterval = null

onMounted(() => {
  updateAnniversaryDays()
  updateCountdown()
  
  countdownInterval = setInterval(() => {
    updateCountdown()
  }, 1000)
  
  quoteInterval = setInterval(() => {
    currentQuoteIndex.value = (currentQuoteIndex.value + 1) % sweetQuotes.value.length
  }, 6000)
  
  zodiacQuoteInterval = setInterval(() => {
    if (currentZodiacQuotes.value.length > 0) {
      currentZodiacQuoteIndex.value = (currentZodiacQuoteIndex.value + 1) % currentZodiacQuotes.value.length
    }
  }, 5000)
})

onUnmounted(() => {
  if (countdownInterval) clearInterval(countdownInterval)
  if (quoteInterval) clearInterval(quoteInterval)
  if (zodiacQuoteInterval) clearInterval(zodiacQuoteInterval)
})
</script>

<style scoped>
:root {
  --primary: #FF6B9D;
  --primary-light: #FF8FB1;
  --primary-dark: #FF4081;
  --secondary: #FFE4EC;
  --accent: #FFD700;
  --bg-gradient-1: #FFF5F8;
  --bg-gradient-2: #FFE8F0;
  --bg-gradient-3: #FFF0F5;
  --text-primary: #2D3436;
  --text-secondary: #636E72;
  --text-light: #B2BEC3;
  --white: #FFFFFF;
  --card-bg: rgba(255, 255, 255, 0.95);
  --shadow-soft: 0 8px 32px rgba(255, 107, 157, 0.15);
  --shadow-medium: 0 12px 48px rgba(255, 107, 157, 0.2);
  --shadow-large: 0 16px 64px rgba(255, 107, 157, 0.25);
  --radius-sm: 8px;
  --radius-md: 16px;
  --radius-lg: 24px;
  --radius-xl: 32px;
  --border-glow: 0 0 0 1px rgba(255, 107, 157, 0.2);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

.app-container {
  min-height: 100vh;
  background: linear-gradient(180deg, var(--bg-gradient-1) 0%, var(--bg-gradient-2) 35%, var(--bg-gradient-3) 70%, var(--bg-gradient-1) 100%);
  padding-bottom: 48px;
  overflow-x: hidden;
}

/* ========== 顶部区域 ========== */
.header-section {
  position: relative;
  padding: 40px 20px 32px;
  background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 50%, #FFB6C1 100%);
  overflow: hidden;
  border-bottom-left-radius: var(--radius-xl);
  border-bottom-right-radius: var(--radius-xl);
  box-shadow: var(--shadow-large);
}

.decoration-float {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

.floating-heart {
  position: absolute;
  color: rgba(255, 255, 255, 0.4);
  font-size: 20px;
  animation: floatUp 4s ease-in-out infinite;
  opacity: 0;
}

@keyframes floatUp {
  0% {
    transform: translateY(100%) scale(0.5);
    opacity: 0;
  }
  20% {
    opacity: 1;
  }
  80% {
    opacity: 0.8;
  }
  100% {
    transform: translateY(-200%) scale(1);
    opacity: 0;
  }
}

.header-content {
  position: relative;
  z-index: 1;
  text-align: center;
}

.title-decoration {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 8px;
}

.deco-star {
  font-size: 20px;
  animation: twinkle 2s ease-in-out infinite;
}

@keyframes twinkle {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.6; transform: scale(0.9); }
}

.main-title {
  font-size: 28px;
  font-weight: 700;
  color: var(--white);
  text-shadow: 0 2px 20px rgba(0, 0, 0, 0.1);
  letter-spacing: 2px;
}

.subtitle {
  font-size: 14px;
  color: rgba(255, 255, 255, 0.9);
  font-weight: 300;
  margin-top: 8px;
  letter-spacing: 1px;
}

.together-days-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  margin-top: 20px;
  padding: 12px 24px;
  background: rgba(255, 255, 255, 0.25);
  backdrop-filter: blur(10px);
  border-radius: 50px;
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.badge-icon {
  font-size: 18px;
}

.badge-text {
  font-size: 13px;
  color: var(--white);
  font-weight: 400;
}

.badge-days {
  font-size: 24px;
  font-weight: 700;
  color: var(--white);
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.badge-unit {
  font-size: 13px;
  color: var(--white);
  font-weight: 500;
}

/* ========== 通用卡片样式 ========== */
.premium-card {
  background: var(--card-bg);
  backdrop-filter: blur(20px);
  border-radius: var(--radius-lg);
  padding: 24px;
  margin: 16px 20px;
  box-shadow: var(--shadow-soft);
  border: 1px solid rgba(255, 107, 157, 0.1);
  position: relative;
  overflow: hidden;
}

.premium-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: linear-gradient(90deg, var(--primary), var(--primary-light), var(--primary));
  opacity: 0.6;
}

.card-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid rgba(255, 107, 157, 0.1);
}

.header-icon {
  font-size: 22px;
}

.card-title {
  font-size: 17px;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: 0.5px;
}

/* ========== 头像区域 ========== */
.avatar-section {
  margin-top: -20px;
  position: relative;
  z-index: 2;
}

.avatar-card {
  padding: 28px 24px;
}

.avatar-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  margin-bottom: 24px;
}

.avatar-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.avatar-ring {
  position: relative;
  padding: 4px;
  background: linear-gradient(135deg, var(--primary), var(--primary-light), var(--accent));
  border-radius: 50%;
  box-shadow: var(--shadow-medium);
  animation: ringPulse 3s ease-in-out infinite;
}

@keyframes ringPulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(255, 107, 157, 0.4); }
  50% { box-shadow: 0 0 0 12px rgba(255, 107, 157, 0.1); }
}

.avatar-img {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid var(--white);
}

.avatar-info {
  text-align: center;
}

.avatar-name {
  display: block;
  font-size: 16px;
  font-weight: 600;
  color: var(--text-primary);
}

.avatar-role {
  display: block;
  font-size: 12px;
  color: var(--text-secondary);
  margin-top: 2px;
}

.heart-connector {
  display: flex;
  align-items: center;
  gap: 8px;
}

.connect-line {
  width: 32px;
  height: 2px;
  background: linear-gradient(90deg, var(--primary), var(--primary-light));
  border-radius: 1px;
}

.heart-center {
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  padding: 12px;
  border-radius: 50%;
  box-shadow: var(--shadow-soft);
}

.heart-icon {
  font-size: 20px;
  display: block;
}

.love-progress {
  margin-top: 8px;
}

.progress-bar {
  height: 8px;
  background: var(--secondary);
  border-radius: 4px;
  overflow: hidden;
  position: relative;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--primary), var(--primary-light));
  border-radius: 4px;
  transition: width 1s ease;
  position: relative;
}

.progress-fill::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

.progress-info {
  display: flex;
  justify-content: space-between;
  margin-top: 8px;
}

.progress-text {
  font-size: 13px;
  color: var(--text-secondary);
}

.progress-percent {
  font-size: 13px;
  font-weight: 600;
  color: var(--primary);
}

/* ========== 倒计时区域 ========== */
.countdown-card {
  padding: 24px;
}

.next-anniversary-info {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 8px;
  margin-bottom: 20px;
}

.anniversary-name {
  font-size: 18px;
  font-weight: 600;
  color: var(--primary);
}

.anniversary-date {
  font-size: 13px;
  color: var(--text-secondary);
}

.countdown-display {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-bottom: 20px;
}

.countdown-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.countdown-box {
  position: relative;
  padding: 16px 20px;
  background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 100%);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-soft);
  min-width: 64px;
}

.countdown-box::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  height: 1px;
  background: rgba(0, 0, 0, 0.1);
}

.flip-number {
  font-size: 28px;
  font-weight: 700;
  color: var(--white);
  text-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  font-variant-numeric: tabular-nums;
}

.countdown-label {
  font-size: 12px;
  font-weight: 500;
  color: var(--text-secondary);
}

.countdown-hint {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding-top: 16px;
  border-top: 1px dashed rgba(255, 107, 157, 0.2);
}

.hint-icon {
  font-size: 16px;
}

.hint-text {
  font-size: 13px;
  color: var(--text-secondary);
  font-style: italic;
}

/* ========== 纪念日列表 ========== */
.anniversary-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.anniversary-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  background: linear-gradient(135deg, #FFF8FA 0%, var(--white) 100%);
  border-radius: var(--radius-md);
  border-left: 4px solid var(--primary);
  box-shadow: 0 2px 12px rgba(255, 107, 157, 0.08);
  transition: all 0.3s ease;
}

.anniversary-item:hover {
  transform: translateX(4px);
  box-shadow: 0 4px 16px rgba(255, 107, 157, 0.12);
}

.anniversary-item.is-today {
  border-left-color: var(--accent);
  background: linear-gradient(135deg, #FFFEF5 0%, var(--white) 100%);
}

.anniversary-item.is-passed {
  opacity: 0.7;
}

.anniversary-icon-wrapper {
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--secondary);
  border-radius: 50%;
}

.anniversary-icon {
  font-size: 24px;
}

.anniversary-content {
  flex: 1;
}

.anniversary-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 4px;
}

.anniversary-name {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-primary);
}

.anniversary-date {
  font-size: 13px;
  color: var(--text-secondary);
}

.days-badge {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 8px 12px;
  border-radius: 12px;
  min-width: 60px;
}

.days-badge.today {
  background: linear-gradient(135deg, #FFD700 0%, #FFA500 100%);
}

.days-badge.soon {
  background: linear-gradient(135deg, #FF6B9D 0%, #FF8FB1 100%);
}

.days-badge.passed {
  background: var(--text-light);
}

.days-badge.normal {
  background: var(--secondary);
}

.days-count {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
}

.days-badge.today .days-count,
.days-badge.soon .days-count {
  color: var(--white);
}

.days-badge.passed .days-count {
  color: var(--white);
}

.days-unit {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-secondary);
}

.days-badge.today .days-unit,
.days-badge.soon .days-unit {
  color: rgba(255, 255, 255, 0.9);
}

.days-badge.passed .days-unit {
  color: rgba(255, 255, 255, 0.9);
}

/* ========== 甜蜜语录 ========== */
.quotes-card {
  padding: 24px;
}

.quotes-carousel {
  min-height: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 16px;
  background: linear-gradient(135deg, #FFF8FA 0%, #FFE8F0 100%);
  border-radius: var(--radius-md);
  position: relative;
}

.quote-slide {
  text-align: center;
}

.quote-decoration {
  margin-bottom: 8px;
}

.quote-mark {
  font-size: 32px;
  color: var(--primary);
  opacity: 0.3;
  font-family: serif;
}

.quote-text {
  font-size: 15px;
  color: var(--text-primary);
  line-height: 1.8;
  font-style: italic;
  padding: 0 16px;
}

.quotes-controls {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  margin-top: 16px;
}

.control-btn {
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--secondary);
  border: none;
  border-radius: 50%;
  cursor: pointer;
  transition: all 0.3s ease;
}

.control-btn:hover {
  background: var(--primary);
}

.control-btn:hover .btn-icon {
  color: var(--white);
}

.btn-icon {
  font-size: 18px;
  color: var(--primary);
  font-weight: 600;
  transition: color 0.3s ease;
}

.quotes-dots {
  display: flex;
  gap: 8px;
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--secondary);
  cursor: pointer;
  transition: all 0.3s ease;
}

.dot.active {
  width: 24px;
  border-radius: 4px;
  background: linear-gradient(90deg, var(--primary), var(--primary-light));
}

/* ========== 相册区域 ========== */
.album-section .card-header {
  justify-content: space-between;
}

.photo-count {
  font-size: 13px;
  color: var(--text-secondary);
  background: var(--secondary);
  padding: 4px 12px;
  border-radius: 20px;
}

.album-masonry {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

.album-item {
  position: relative;
  border-radius: var(--radius-md);
  overflow: hidden;
  box-shadow: 0 4px 16px rgba(255, 107, 157, 0.1);
  transition: all 0.3s ease;
}

.album-item.size-large {
  grid-row: span 2;
  aspect-ratio: 3/4;
}

.album-item.size-medium {
  aspect-ratio: 1;
}

.photo-wrapper {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.album-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.album-item:hover .album-img {
  transform: scale(1.1);
}

.photo-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: linear-gradient(transparent, rgba(0, 0, 0, 0.7));
  padding: 24px 12px 12px;
  transform: translateY(100%);
  transition: transform 0.3s ease;
}

.album-item:hover .photo-overlay {
  transform: translateY(0);
}

.overlay-content {
  display: flex;
  align-items: center;
  gap: 8px;
}

.photo-icon {
  font-size: 16px;
}

.photo-desc {
  font-size: 13px;
  color: var(--white);
  font-weight: 500;
}

/* ========== 星座区域 ========== */
.zodiac-tabs {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
  padding: 4px;
  background: var(--secondary);
  border-radius: 50px;
}

.tab-btn {
  flex: 1;
  padding: 10px 16px;
  border: none;
  background: transparent;
  border-radius: 50px;
  font-size: 14px;
  font-weight: 500;
  color: var(--text-secondary);
  cursor: pointer;
  transition: all 0.3s ease;
}

.tab-btn.active {
  background: var(--white);
  color: var(--primary);
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(255, 107, 157, 0.15);
}

.tab-content {
  transition: all 0.3s ease;
}

/* 星座选择器 */
.zodiac-selector {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  margin-bottom: 16px;
}

.zodiac-chip {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 12px 8px;
  background: var(--secondary);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid transparent;
}

.zodiac-chip:hover {
  background: #FFE4EC;
  transform: translateY(-2px);
}

.zodiac-chip.active {
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  border-color: var(--primary);
  transform: translateY(-2px);
  box-shadow: var(--shadow-soft);
}

.chip-symbol {
  font-size: 24px;
  margin-bottom: 4px;
}

.zodiac-chip.active .chip-symbol,
.zodiac-chip.active .chip-name {
  color: var(--white);
}

.chip-name {
  font-size: 12px;
  font-weight: 500;
  color: var(--text-primary);
}

/* 时段选择 */
.fortune-period-tabs {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
}

.period-chip {
  padding: 8px 16px;
  border: none;
  background: var(--secondary);
  border-radius: 50px;
  font-size: 13px;
  font-weight: 500;
  color: var(--text-secondary);
  cursor: pointer;
  transition: all 0.3s ease;
}

.period-chip:hover {
  background: #FFE4EC;
}

.period-chip.active {
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  color: var(--white);
}

/* 星座信息卡片 */
.zodiac-info-card {
  padding: 20px;
  background: linear-gradient(135deg, #FFF8FA 0%, #FFE8F0 100%);
  border-radius: var(--radius-md);
  margin-bottom: 16px;
}

.zodiac-main {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}

.zodiac-symbol-large {
  font-size: 48px;
}

.zodiac-details {
  flex: 1;
}

.zodiac-name-large {
  font-size: 20px;
  font-weight: 700;
  color: var(--text-primary);
  margin-bottom: 4px;
}

.zodiac-date {
  font-size: 13px;
  color: var(--text-secondary);
  margin-bottom: 4px;
}

.zodiac-element {
  font-size: 12px;
  color: var(--primary);
  background: rgba(255, 107, 157, 0.1);
  padding: 2px 10px;
  border-radius: 20px;
}

/* 幸运信息 */
.lucky-info {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-bottom: 16px;
}

.lucky-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
}

.lucky-label {
  font-size: 12px;
  color: var(--text-secondary);
}

.lucky-value {
  font-size: 15px;
  font-weight: 600;
  color: var(--primary);
}

.lucky-value.color {
  padding: 4px 12px;
  border-radius: 20px;
  color: var(--white);
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

/* 开运提示 */
.lucky-tip {
  display: flex;
  align-items: flex-start;
  gap: 8px;
  padding: 12px;
  background: rgba(255, 215, 0, 0.1);
  border-radius: var(--radius-sm);
  border-left: 3px solid var(--accent);
}

.tip-icon {
  font-size: 18px;
}

.tip-text {
  font-size: 13px;
  color: var(--text-primary);
  line-height: 1.5;
}

/* 运势维度 */
.fortune-dimensions {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  margin-bottom: 16px;
}

.dimension-card {
  padding: 16px;
  background: var(--white);
  border-radius: var(--radius-md);
  box-shadow: 0 2px 8px rgba(255, 107, 157, 0.08);
}

.dimension-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.dimension-icon {
  font-size: 20px;
}

.dimension-name {
  flex: 1;
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.dimension-score {
  font-size: 14px;
  font-weight: 700;
  padding: 2px 8px;
  border-radius: 12px;
}

.dimension-score.excellent {
  background: rgba(76, 175, 80, 0.1);
  color: #4CAF50;
}

.dimension-score.good {
  background: rgba(255, 152, 0, 0.1);
  color: #FF9800;
}

.dimension-score.average {
  background: rgba(33, 150, 243, 0.1);
  color: #2196F3;
}

.dimension-score.needs-improvement {
  background: rgba(244, 67, 54, 0.1);
  color: #F44336;
}

.dimension-bar-wrapper {
  margin-bottom: 8px;
}

.dimension-bar {
  height: 8px;
  background: var(--secondary);
  border-radius: 4px;
  overflow: hidden;
}

.dimension-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 1s ease;
}

.dimension-fill.excellent {
  background: linear-gradient(90deg, #4CAF50, #81C784);
}

.dimension-fill.good {
  background: linear-gradient(90deg, #FF9800, #FFB74D);
}

.dimension-fill.average {
  background: linear-gradient(90deg, #2196F3, #64B5F6);
}

.dimension-fill.needs-improvement {
  background: linear-gradient(90deg, #F44336, #E57373);
}

.dimension-desc {
  font-size: 12px;
  color: var(--text-secondary);
  line-height: 1.4;
}

/* 整体运势 */
.overall-fortune {
  padding: 16px;
  background: linear-gradient(135deg, #FFF8FA 0%, #FFE8F0 100%);
  border-radius: var(--radius-md);
  margin-bottom: 16px;
}

.overall-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.overall-icon {
  font-size: 20px;
}

.overall-title {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-primary);
}

.overall-text {
  font-size: 14px;
  color: var(--text-primary);
  line-height: 1.6;
}

/* 星座语录 */
.zodiac-quotes-section {
  margin-bottom: 16px;
}

.zodiac-quotes-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.quotes-icon {
  font-size: 18px;
}

.quotes-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.zodiac-quote-slider {
  padding: 16px;
  background: var(--secondary);
  border-radius: var(--radius-md);
  text-align: center;
}

.zodiac-quote-text {
  font-size: 14px;
  color: var(--primary);
  font-style: italic;
  line-height: 1.6;
}

.zodiac-quotes-dots {
  display: flex;
  justify-content: center;
  gap: 6px;
  margin-top: 12px;
}

.small-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--text-light);
}

.small-dot.active {
  background: var(--primary);
  width: 16px;
  border-radius: 3px;
}

/* 性格解析 */
.personality-analysis {
  padding-top: 16px;
  border-top: 1px solid var(--secondary);
}

.analysis-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 16px;
}

.analysis-icon {
  font-size: 18px;
}

.analysis-title {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-primary);
}

.personality-cards {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  margin-bottom: 12px;
}

.personality-card {
  padding: 12px;
  border-radius: var(--radius-md);
}

.personality-card.strengths {
  background: rgba(76, 175, 80, 0.05);
  border: 1px solid rgba(76, 175, 80, 0.2);
}

.personality-card.weaknesses {
  background: rgba(255, 152, 0, 0.05);
  border: 1px solid rgba(255, 152, 0, 0.2);
}

.card-badge {
  display: flex;
  align-items: center;
  gap: 6px;
  margin-bottom: 8px;
}

.personality-card.strengths .badge-text {
  color: #4CAF50;
}

.personality-card.weaknesses .badge-text {
  color: #FF9800;
}

.badge-text {
  font-size: 13px;
  font-weight: 600;
}

.tags-container {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin-bottom: 8px;
}

.trait-tag {
  font-size: 11px;
  padding: 2px 8px;
  border-radius: 12px;
}

.trait-tag.positive {
  background: rgba(76, 175, 80, 0.1);
  color: #4CAF50;
}

.trait-tag.negative {
  background: rgba(255, 152, 0, 0.1);
  color: #FF9800;
}

.personality-desc {
  font-size: 12px;
  color: var(--text-secondary);
  line-height: 1.4;
}

.style-card {
  padding: 12px;
  background: var(--secondary);
  border-radius: var(--radius-md);
}

.style-desc {
  font-size: 13px;
  color: var(--text-primary);
  line-height: 1.6;
}

/* ========== 双人配对 ========== */
.pair-selector {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 20px;
}

.select-column {
  flex: 1;
}

.select-label {
  display: block;
  font-size: 13px;
  color: var(--text-secondary);
  margin-bottom: 8px;
  text-align: center;
}

.zodiac-dropdown {
  position: relative;
}

.dropdown-display {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 16px;
  background: var(--secondary);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: all 0.3s ease;
}

.dropdown-display:hover {
  background: #FFE4EC;
}

.display-symbol {
  font-size: 24px;
}

.display-name {
  flex: 1;
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.dropdown-arrow {
  font-size: 12px;
  color: var(--text-secondary);
  transition: transform 0.3s ease;
}

.dropdown-arrow.open {
  transform: rotate(180deg);
}

.dropdown-options {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 4px;
  background: var(--white);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-medium);
  max-height: 240px;
  overflow-y: auto;
  z-index: 100;
  border: 1px solid var(--secondary);
}

.option-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px 16px;
  cursor: pointer;
  transition: background 0.2s ease;
}

.option-item:hover {
  background: var(--secondary);
}

.option-item.selected {
  background: rgba(255, 107, 157, 0.1);
}

.option-symbol {
  font-size: 20px;
}

.option-name {
  font-size: 14px;
  color: var(--text-primary);
}

.pair-connector {
  display: flex;
  align-items: center;
  justify-content: center;
  padding-top: 24px;
}

.connector-heart {
  font-size: 28px;
  animation: pulse 1.5s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.start-pair-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  width: 100%;
  padding: 14px 24px;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  border: none;
  border-radius: 50px;
  font-size: 16px;
  font-weight: 600;
  color: var(--white);
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: var(--shadow-soft);
}

.start-pair-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-medium);
}

/* 配对结果 */
.pair-result {
  margin-top: 24px;
  animation: fadeInUp 0.5s ease;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.compatibility-score {
  text-align: center;
  margin-bottom: 24px;
}

.score-circle {
  width: 120px;
  height: 120px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0 auto 12px;
  border-radius: 50%;
  position: relative;
}

.score-circle::before {
  content: '';
  position: absolute;
  top: 4px;
  left: 4px;
  right: 4px;
  bottom: 4px;
  background: var(--white);
  border-radius: 50%;
}

.score-circle.perfect {
  background: conic-gradient(#4CAF50 0deg 360deg);
}

.score-circle.great {
  background: conic-gradient(#8BC34A 0deg 324deg, #E0E0E0 324deg 360deg);
}

.score-circle.good {
  background: conic-gradient(#FF9800 0deg 288deg, #E0E0E0 288deg 360deg);
}

.score-circle.average {
  background: conic-gradient(#2196F3 0deg 216deg, #E0E0E0 216deg 360deg);
}

.score-circle.needs-work {
  background: conic-gradient(#F44336 0deg 180deg, #E0E0E0 180deg 360deg);
}

.score-value {
  font-size: 36px;
  font-weight: 700;
  color: var(--text-primary);
  position: relative;
  z-index: 1;
}

.score-unit {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-secondary);
  position: relative;
  z-index: 1;
}

.score-label {
  font-size: 14px;
  color: var(--text-secondary);
  margin-bottom: 4px;
}

.score-level {
  font-size: 18px;
  font-weight: 700;
}

.score-circle.perfect ~ .score-level { color: #4CAF50; }
.score-circle.great ~ .score-level { color: #8BC34A; }
.score-circle.good ~ .score-level { color: #FF9800; }
.score-circle.average ~ .score-level { color: #2196F3; }
.score-circle.needs-work ~ .score-level { color: #F44336; }

.compatibility-bars {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-bottom: 24px;
}

.bar-item {
  width: 100%;
}

.bar-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.bar-icon {
  font-size: 16px;
}

.bar-label {
  flex: 1;
  font-size: 13px;
  color: var(--text-secondary);
}

.bar-score {
  font-size: 13px;
  font-weight: 600;
  color: var(--primary);
}

.bar-bg {
  height: 8px;
  background: var(--secondary);
  border-radius: 4px;
  overflow: hidden;
}

.bar-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 1s ease;
}

.bar-fill.love {
  background: linear-gradient(90deg, #FF6B9D, #FF8FB1);
}

.bar-fill.communication {
  background: linear-gradient(90deg, #2196F3, #64B5F6);
}

.bar-fill.harmony {
  background: linear-gradient(90deg, #4CAF50, #81C784);
}

.bar-fill.value {
  background: linear-gradient(90deg, #FF9800, #FFB74D);
}

/* 配对分析卡片 */
.pair-analysis {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.analysis-card,
.advice-card {
  padding: 16px;
  border-radius: var(--radius-md);
}

.analysis-card {
  background: rgba(33, 150, 243, 0.05);
  border: 1px solid rgba(33, 150, 243, 0.15);
}

.advice-card {
  background: rgba(255, 107, 157, 0.05);
  border: 1px solid rgba(255, 107, 157, 0.15);
}

.card-title-wrapper {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.analysis-text,
.advice-text {
  font-size: 14px;
  color: var(--text-primary);
  line-height: 1.6;
}

/* ========== 占卜抽签 ========== */
.divination-intro {
  text-align: center;
  margin-bottom: 24px;
}

.intro-icon {
  font-size: 48px;
  margin-bottom: 12px;
}

.intro-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 8px;
}

.intro-desc {
  font-size: 14px;
  color: var(--text-secondary);
}

.divination-area {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 200px;
  margin-bottom: 24px;
}

.card-stack {
  position: relative;
  width: 140px;
  height: 200px;
  cursor: pointer;
}

.back-card {
  position: absolute;
  width: 140px;
  height: 200px;
  background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 50%, #FFB6C1 100%);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-soft);
}

.back-card.back-1 {
  transform: rotate(-3deg) translateY(8px);
  z-index: 1;
}

.back-card.back-2 {
  transform: rotate(2deg) translateY(4px);
  z-index: 2;
}

.back-card.back-3 {
  z-index: 3;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.back-card.back-3:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-medium);
}

.card-back-design {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.card-icon {
  font-size: 36px;
}

.card-hint {
  font-size: 14px;
  font-weight: 500;
  color: var(--white);
}

/* 结果卡片 */
.result-card-wrapper {
  opacity: 0;
  transform: scale(0.8) rotateY(-180deg);
  transition: all 0.6s ease;
}

.result-card-wrapper.show-result {
  opacity: 1;
  transform: scale(1) rotateY(0);
}

.result-card {
  width: 160px;
  padding: 24px 16px;
  background: linear-gradient(135deg, #FFF8FA 0%, #FFE8F0 100%);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-medium);
  text-align: center;
  position: relative;
  border: 2px solid rgba(255, 107, 157, 0.2);
}

.card-header-deco {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 16px;
}

.deco-line {
  width: 32px;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--primary), transparent);
}

.result-icon {
  font-size: 40px;
  margin-bottom: 8px;
}

.result-title {
  font-size: 24px;
  font-weight: 700;
  color: var(--primary);
  margin-bottom: 16px;
}

.card-lucky-numbers {
  margin-bottom: 16px;
}

.numbers-label {
  display: block;
  font-size: 12px;
  color: var(--text-secondary);
  margin-bottom: 8px;
}

.numbers-list {
  display: flex;
  justify-content: center;
  gap: 8px;
}

.lucky-num {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  border-radius: 50%;
  font-size: 14px;
  font-weight: 600;
  color: var(--white);
}

.fortune-content {
  font-size: 13px;
  color: var(--text-primary);
  line-height: 1.6;
  text-align: left;
}

/* 占卜操作按钮 */
.divination-actions {
  display: flex;
  gap: 12px;
  justify-content: center;
}

.action-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  border: none;
  border-radius: 50px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.action-btn.primary {
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  color: var(--white);
  box-shadow: var(--shadow-soft);
}

.action-btn.primary:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-medium);
}

.action-btn.secondary {
  background: var(--secondary);
  color: var(--text-primary);
}

.action-btn.secondary:hover {
  background: #FFE4EC;
}

/* ========== 弹窗样式 ========== */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  padding: 20px;
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.popup-content {
  width: 100%;
  max-width: 360px;
  background: var(--white);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-large);
  overflow: hidden;
  animation: slideUp 0.3s ease;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
}

.popup-title {
  font-size: 17px;
  font-weight: 600;
  color: var(--white);
}

.popup-close {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.2);
  border: none;
  border-radius: 50%;
  font-size: 20px;
  color: var(--white);
  cursor: pointer;
  transition: background 0.3s ease;
}

.popup-close:hover {
  background: rgba(255, 255, 255, 0.3);
}

.popup-body {
  padding: 24px 20px;
}

.popup-result {
  text-align: center;
}

.popup-icon {
  font-size: 48px;
  margin-bottom: 12px;
}

.popup-result-title {
  font-size: 24px;
  font-weight: 700;
  color: var(--primary);
  margin-bottom: 12px;
}

.popup-result-text {
  font-size: 14px;
  color: var(--text-primary);
  line-height: 1.6;
  margin-bottom: 16px;
}

.popup-lucky {
  padding: 12px;
  background: var(--secondary);
  border-radius: var(--radius-md);
}

.popup-lucky-label {
  font-size: 13px;
  color: var(--text-secondary);
  margin-right: 8px;
}

.popup-lucky-num {
  display: inline-block;
  width: 28px;
  height: 28px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  border-radius: 50%;
  font-size: 13px;
  font-weight: 600;
  color: var(--white);
  margin-right: 6px;
}

.popup-footer {
  padding: 16px 20px 24px;
}

.popup-btn {
  width: 100%;
  padding: 14px;
  background: linear-gradient(135deg, var(--primary), var(--primary-light));
  border: none;
  border-radius: 50px;
  font-size: 16px;
  font-weight: 600;
  color: var(--white);
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: var(--shadow-soft);
}

.popup-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-medium);
}

/* ========== 底部签名区域 ========== */
.signature-card {
  padding: 28px 24px;
  text-align: center;
}

.signature-header {
  margin-bottom: 20px;
}

.signature-decoration {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
}

.deco-flower {
  font-size: 24px;
  animation: sway 3s ease-in-out infinite;
}

@keyframes sway {
  0%, 100% { transform: rotate(-5deg); }
  50% { transform: rotate(5deg); }
}

.signature-content {
  margin-bottom: 20px;
}

.signature-text {
  font-size: 14px;
  color: var(--text-primary);
  line-height: 2;
  font-style: italic;
  padding: 0 8px;
}

.signature-footer {
  padding-top: 20px;
  border-top: 1px dashed rgba(255, 107, 157, 0.2);
}

.signer-info {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 8px;
  margin-bottom: 8px;
}

.signer-name {
  font-size: 16px;
  font-weight: 600;
  color: var(--primary);
}

.signer-to {
  font-size: 14px;
  color: var(--text-secondary);
}

.signature-date {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
}

.date-icon {
  font-size: 14px;
}

.date-text {
  font-size: 13px;
  color: var(--text-secondary);
}

/* 页脚底部 */
.footer-bottom {
  text-align: center;
  padding: 24px 20px;
}

.footer-hearts {
  display: flex;
  justify-content: center;
  gap: 8px;
  margin-bottom: 12px;
}

.footer-heart {
  font-size: 14px;
  color: var(--primary);
  opacity: 0.6;
}

.footer-text {
  font-size: 13px;
  color: var(--text-secondary);
  margin-bottom: 4px;
}

.footer-copyright {
  font-size: 11px;
  color: var(--text-light);
}

/* ========== 响应式设计 ========== */
@media (max-width: 375px) {
  .main-title {
    font-size: 24px;
  }
  
  .countdown-box {
    padding: 12px 14px;
    min-width: 56px;
  }
  
  .flip-number {
    font-size: 24px;
  }
  
  .zodiac-selector {
    grid-template-columns: repeat(3, 1fr);
  }
  
  .fortune-dimensions {
    grid-template-columns: 1fr;
  }
  
  .personality-cards {
    grid-template-columns: 1fr;
  }
  
  .album-masonry {
    grid-template-columns: 1fr;
  }
  
  .album-item.size-large {
    grid-row: span 1;
    aspect-ratio: 16/9;
  }
  
  .pair-selector {
    flex-direction: column;
  }
  
  .lucky-info {
    grid-template-columns: 1fr;
    gap: 16px;
  }
}

@media (min-width: 768px) {
  .app-container {
    max-width: 480px;
    margin: 0 auto;
    border-left: 1px solid rgba(255, 107, 157, 0.1);
    border-right: 1px solid rgba(255, 107, 157, 0.1);
  }
  
  .premium-card {
    margin: 16px 24px;
  }
  
  .zodiac-selector {
    grid-template-columns: repeat(6, 1fr);
  }
  
  .album-masonry {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* 深色模式适配 */
@media (prefers-color-scheme: dark) {
  :root {
    --text-primary: #E0E0E0;
    --text-secondary: #9E9E9E;
    --card-bg: rgba(30, 30, 30, 0.95);
  }
}

/* 滚动优化 */
.app-container {
  -webkit-overflow-scrolling: touch;
  scroll-behavior: smooth;
}

/* 选择文本样式 */
::selection {
  background: rgba(255, 107, 157, 0.3);
  color: var(--text-primary);
}

/* 滚动条样式 */
::-webkit-scrollbar {
  width: 4px;
  height: 4px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background: rgba(255, 107, 157, 0.3);
  border-radius: 2px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 107, 157, 0.5);
}
</style>