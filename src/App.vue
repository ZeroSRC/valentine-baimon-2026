<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// States: 'question' -> 'password' -> 'story'
const step = ref('question')
const noBtnStyle = ref({})
const password = ref('')
const passwordError = ref('')
const currentPage = ref(0)
const isTransitioning = ref(false)
const storyContainer = ref(null)
const envelopeOpened = ref(false)
const letterPulled = ref(false)

// Hardcoded password - น่ารัก!
const SECRET_PASSWORD = 'iloveyou'

// Story pages - แก้ไขข้อมูลตรงนี้ได้เลย!
const storyPages = [
  {
    image: '/kitty-kitty-heart.gif',
    title: 'วันแรก',
    text: 'วันที่เราเจอกัน... 💕',
    bgGradient: 'linear-gradient(135deg, #FF9A9E 0%, #FECFEF 50%, #FFF6F6 100%)'
  },
  {
    image: 'https://media.tenor.com/gUiu1zyxfzYAAAAi/bear-kiss-bear-kisses.gif',
    title: 'ความทรงจำ',
    text: 'ทุกวันที่อยู่ด้วยกัน 🌸',
    bgGradient: 'linear-gradient(135deg, #FFECD2 0%, #FCB69F 50%, #FF9A9E 100%)'
  },
  {
    image: '/kitty-kitty-heart.gif',
    title: 'ผ่านมาด้วยกัน',
    text: 'ทั้งสุขและทุกข์ 🤗',
    bgGradient: 'linear-gradient(135deg, #A18CD1 0%, #FBC2EB 50%, #FFECD2 100%)'
  },
  {
    title: 'จดหมายรัก 💌',
    isEnvelope: true,
    isFloralBg: true,
    letterMessage: 'ถึงคนที่รักที่สุด...\n\nเค้าอยากบอกว่า\nเค้ารักตัวมากนะ ❤️\n\nขอบคุณที่อยู่ด้วยกันมาตลอด\nทุกวันที่อยู่กับตัวคือวันที่มีความสุข\n\nHappy Valentine\'s Day!\n\n💕 รักตัวเสมอ 💕'
  }
]

const handleYes = () => {
  step.value = 'password'
}

const checkPassword = () => {
  if (password.value.toLowerCase() === SECRET_PASSWORD) {
    step.value = 'story'
    currentPage.value = 0
    passwordError.value = ''
  } else {
    passwordError.value = 'รหัสผ่านไม่ถูกต้องนะ ลองใหม่อีกครั้ง! 🥺'
  }
}

const goToPage = (index) => {
  if (isTransitioning.value || index === currentPage.value) return
  if (index < 0 || index >= storyPages.length) return
  
  isTransitioning.value = true
  currentPage.value = index
  
  setTimeout(() => {
    isTransitioning.value = false
  }, 600)
}

const nextPage = () => {
  if (currentPage.value < storyPages.length - 1) {
    goToPage(currentPage.value + 1)
  }
}

const prevPage = () => {
  if (currentPage.value > 0) {
    goToPage(currentPage.value - 1)
  }
}

// Scroll/Swipe handling
let touchStartY = 0
let touchEndY = 0
let lastScrollTime = 0

const handleWheel = (e) => {
  e.preventDefault()
  const now = Date.now()
  if (now - lastScrollTime < 800) return
  
  if (e.deltaY > 30) {
    nextPage()
    lastScrollTime = now
  } else if (e.deltaY < -30) {
    prevPage()
    lastScrollTime = now
  }
}

const handleTouchStart = (e) => {
  touchStartY = e.touches[0].clientY
}

const handleTouchEnd = (e) => {
  touchEndY = e.changedTouches[0].clientY
  const diff = touchStartY - touchEndY
  
  if (Math.abs(diff) > 50) {
    if (diff > 0) {
      nextPage()
    } else {
      prevPage()
    }
  }
}

const handleKeydown = (e) => {
  if (step.value !== 'story') return
  
  if (e.key === 'ArrowDown' || e.key === 'ArrowRight' || e.key === ' ') {
    e.preventDefault()
    nextPage()
  } else if (e.key === 'ArrowUp' || e.key === 'ArrowLeft') {
    e.preventDefault()
    prevPage()
  }
}

const moveButton = () => {
  const x = Math.random() * (window.innerWidth - 100)
  const y = Math.random() * (window.innerHeight - 50)
  
  noBtnStyle.value = {
    position: 'fixed',
    left: `${x}px`,
    top: `${y}px`,
    transition: 'all 0.2s ease'
  }
}

const openEnvelope = () => {
  if (!envelopeOpened.value) {
    envelopeOpened.value = true
    setTimeout(() => {
      letterPulled.value = true
    }, 600)
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
})
</script>

<template>
  <div class="container">
    <!-- Floating Hearts Background -->
    <div class="floating-hearts">
      <span v-for="i in 15" :key="i" class="floating-heart" :style="{ '--delay': i * 0.5 + 's', '--left': (i * 7) % 100 + '%' }">💕</span>
    </div>

    <!-- Step 1: Question -->
    <div v-if="step === 'question'" class="content fade-in">
      <h1 class="title">เค้ารักตัวนะ รักเค้ามั้ย? 🥺</h1>
      <img src="/kitty-kitty-heart.gif" alt="Cute Kitty" class="main-image" />
      
      <div class="buttons">
        <button class="btn yes-btn" @click="handleYes">รัก ❤️</button>
        <button 
          class="btn no-btn" 
          @mouseover="moveButton" 
          @click="moveButton"
          :style="noBtnStyle"
        >
          ไม่รัก 💔
        </button>
      </div>
    </div>

    <!-- Step 2: Password -->
    <div v-else-if="step === 'password'" class="content fade-in">
      <h1 class="title">พิสูจน์ความรักหน่อยสิ! 💕</h1>
      <img src="/kitty-kitty-heart.gif" alt="Cute Kitty" class="main-image" />
      
      <div class="password-section">
        <p class="password-hint">ใส่รหัสลับแห่งความรัก 🔐</p>
        <input 
          type="password" 
          v-model="password" 
          class="password-input"
          placeholder="รหัสผ่าน..."
          @keyup.enter="checkPassword"
        />
        <p v-if="passwordError" class="error-msg">{{ passwordError }}</p>
        <button class="btn yes-btn" @click="checkPassword">ยืนยัน 💖</button>
      </div>
    </div>

    <!-- Step 3: Scrolling Story -->
    <div 
      v-else 
      class="story-wrapper"
      ref="storyContainer"
      @wheel.prevent="handleWheel"
      @touchstart="handleTouchStart"
      @touchend="handleTouchEnd"
    >
      <!-- Story Pages -->
      <div class="story-viewport">
        <TransitionGroup name="story-slide" tag="div" class="story-pages-container">
          <div 
            v-for="(page, index) in storyPages" 
            :key="index"
            v-show="index === currentPage"
            class="story-page"
            :class="{ 'floral-garden': page.isFloralBg }"
            :style="{ background: page.bgGradient }"
          >
            <!-- Floating Flower Petals for Envelope Page -->
            <div v-if="page.isFloralBg" class="flower-petals">
              <span v-for="i in 20" :key="'petal-'+i" class="petal" :style="{ '--i': i }"></span>
            </div>
            
            <!-- Regular Story Content -->
            <div v-if="!page.isEnvelope" class="story-content">
              <div class="page-number-badge">{{ index + 1 }} / {{ storyPages.length }}</div>
              
              <h2 class="story-title">{{ page.title }}</h2>
              
              <div class="story-image-container">
                <div class="image-glow"></div>
                <img :src="page.image" :alt="page.title" class="story-image" />
              </div>
              
              <p class="story-text">{{ page.text }}</p>
            </div>
            
            <!-- Envelope Page - Premium Design -->
            <div v-else class="envelope-content">
              <div class="page-number-badge">{{ index + 1 }} / {{ storyPages.length }}</div>
              
              <h2 class="story-title">{{ page.title }}</h2>
              
              <p class="envelope-hint" v-show="!envelopeOpened">
                <span class="hint-icon">👆</span>
                แตะซองจดหมายเพื่อเปิดอ่าน
              </p>
              
              <div class="envelope-scene">
                <div class="envelope-wrapper" @click="openEnvelope">
                  <!-- The Envelope -->
                  <div class="envelope" :class="{ opened: envelopeOpened }">
                    
                    <!-- Envelope Back -->
                    <div class="envelope-back"></div>
                    
                    <!-- Letter -->
                    <div class="letter" :class="{ pulled: letterPulled }">
                      <div class="letter-paper">
                        <div class="letter-header">
                          <span>💌</span>
                        </div>
                        <p class="letter-text">{{ page.letterMessage }}</p>
                        <div class="letter-footer">
                          <span>💕</span>
                          <span>∿∿∿</span>
                          <span>💕</span>
                        </div>
                      </div>
                    </div>
                    
                    <!-- Envelope Front -->
                    <div class="envelope-front">
                      <!-- Wax Seal -->
                      <div class="wax-seal" :class="{ broken: envelopeOpened }">
                        <span class="seal-heart">♥</span>
                      </div>
                    </div>
                    
                    <!-- Envelope Flap -->
                    <div class="envelope-flap">
                      <div class="flap-inner"></div>
                    </div>
                    
                  </div>
                </div>
                
                <!-- Celebration Hearts -->
                <div class="celebration" v-show="letterPulled">
                  <span v-for="i in 12" :key="'celeb-'+i" class="celeb-heart" :style="{ '--i': i }">💖</span>
                </div>
              </div>
            </div>
          </div>
        </TransitionGroup>
      </div>

      <!-- Page Indicator Dots -->
      <div class="page-indicators">
        <button 
          v-for="(page, index) in storyPages" 
          :key="index"
          class="indicator-dot"
          :class="{ active: index === currentPage }"
          @click="goToPage(index)"
        >
          <span class="dot-inner"></span>
        </button>
      </div>

      <!-- Navigation Arrows -->
      <button 
        class="nav-arrow nav-prev" 
        @click="prevPage"
        :disabled="currentPage === 0"
        v-show="currentPage > 0"
      >
        <span>▲</span>
      </button>
      
      <button 
        class="nav-arrow nav-next" 
        @click="nextPage"
        :disabled="currentPage === storyPages.length - 1"
        v-show="currentPage < storyPages.length - 1"
      >
        <span>▼</span>
      </button>

      <!-- Scroll Hint -->
      <div class="scroll-hint" v-show="currentPage < storyPages.length - 1">
        <span class="scroll-text">เลื่อนลงเพื่อดูต่อ</span>
        <span class="scroll-icon">👇</span>
      </div>
    </div>
  </div>
</template>

<style>
/* Global Resets */
body {
  margin: 0;
  padding: 0;
  background-color: #FFB7C5;
  font-family: 'Mali', cursive;
  overflow: hidden;
}

#app {
  width: 100vw;
  height: 100vh;
}
</style>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
  position: relative;
  overflow: hidden;
}

/* Floating Hearts Background */
.floating-hearts {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
  overflow: hidden;
}

.floating-heart {
  position: absolute;
  bottom: -50px;
  font-size: 1.5rem;
  left: var(--left);
  animation: floatUp 8s ease-in-out infinite;
  animation-delay: var(--delay);
  opacity: 0.6;
}

@keyframes floatUp {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.6;
  }
  100% {
    transform: translateY(-110vh) rotate(360deg);
    opacity: 0;
  }
}

/* Fade In Animation */
.fade-in {
  animation: fadeIn 0.8s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.content {
  text-align: center;
  background: rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(10px);
  padding: 3rem;
  border-radius: 20px;
  box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.18);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  max-width: 90%;
  z-index: 1;
}

.title {
  color: #D32F2F;
  font-size: 2.5rem;
  margin: 0;
  text-shadow: 2px 2px 0px rgba(255, 255, 255, 0.5);
}

.main-image {
  width: 200px;
  height: auto;
  border-radius: 15px;
  animation: float 3s ease-in-out infinite;
}

.buttons {
  display: flex;
  gap: 2rem;
  align-items: center;
  justify-content: center;
  position: relative;
}

.btn {
  font-family: 'Mali', cursive;
  font-size: 1.5rem;
  padding: 0.8rem 2.5rem;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

.yes-btn {
  background-color: #FF69B4;
  color: white;
  font-weight: bold;
}

.yes-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 20px rgba(255, 105, 180, 0.4);
}

.no-btn {
  background-color: #ffffff;
  color: #555;
  border: 2px solid #ddd;
}

.password-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.password-hint {
  color: #D32F2F;
  font-size: 1.2rem;
  margin: 0;
}

.password-input {
  font-family: 'Mali', cursive;
  font-size: 1.2rem;
  padding: 0.8rem 1.5rem;
  border: 3px solid #FF69B4;
  border-radius: 50px;
  outline: none;
  text-align: center;
  width: 200px;
  transition: all 0.3s ease;
}

.password-input:focus {
  border-color: #D32F2F;
  box-shadow: 0 0 15px rgba(255, 105, 180, 0.4);
}

.error-msg {
  color: #D32F2F;
  font-size: 1rem;
  margin: 0;
  animation: shake 0.5s ease;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

@keyframes float {
  0% { transform: translateY(0px); }
  50% { transform: translateY(-10px); }
  100% { transform: translateY(0px); }
}

/* ====== Scrolling Story Styles ====== */
.story-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.story-viewport {
  position: relative;
  width: 100%;
  height: 100%;
}

.story-pages-container {
  position: relative;
  width: 100%;
  height: 100%;
}

.story-page {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.6s ease;
}

/* Story Slide Transition */
.story-slide-enter-active,
.story-slide-leave-active {
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

.story-slide-enter-from {
  transform: translateY(100%);
  opacity: 0;
}

.story-slide-leave-to {
  transform: translateY(-100%);
  opacity: 0;
}

.story-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
  padding: 2rem;
  max-width: 500px;
  text-align: center;
  animation: contentFadeIn 0.8s ease-out 0.2s both;
}

@keyframes contentFadeIn {
  from {
    opacity: 0;
    transform: translateY(30px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.page-number-badge {
  background: rgba(255, 255, 255, 0.9);
  color: #D32F2F;
  padding: 0.5rem 1.2rem;
  border-radius: 50px;
  font-size: 0.9rem;
  font-weight: bold;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.story-title {
  color: white;
  font-size: 2.5rem;
  margin: 0;
  text-shadow: 2px 2px 10px rgba(0, 0, 0, 0.3);
  animation: titlePop 0.6s ease-out 0.4s both;
}

@keyframes titlePop {
  0% {
    opacity: 0;
    transform: scale(0.5);
  }
  70% {
    transform: scale(1.1);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}

.story-image-container {
  position: relative;
  animation: imageSlideIn 0.7s ease-out 0.5s both;
}

@keyframes imageSlideIn {
  from {
    opacity: 0;
    transform: translateY(40px) rotate(-5deg);
  }
  to {
    opacity: 1;
    transform: translateY(0) rotate(0deg);
  }
}

.image-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 120%;
  height: 120%;
  background: radial-gradient(circle, rgba(255, 255, 255, 0.4) 0%, transparent 70%);
  z-index: 0;
  animation: glowPulse 3s ease-in-out infinite;
}

@keyframes glowPulse {
  0%, 100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 0.4;
  }
  50% {
    transform: translate(-50%, -50%) scale(1.1);
    opacity: 0.6;
  }
}

.story-image {
  position: relative;
  z-index: 1;
  width: 280px;
  max-width: 80vw;
  height: auto;
  border-radius: 20px;
  box-shadow: 
    0 20px 60px rgba(0, 0, 0, 0.3),
    0 0 0 5px rgba(255, 255, 255, 0.5);
  transition: transform 0.4s ease;
}

.story-image:hover {
  transform: scale(1.05) rotate(2deg);
}

.story-text {
  color: white;
  font-size: 1.4rem;
  line-height: 1.8;
  margin: 0;
  white-space: pre-line;
  text-shadow: 1px 1px 5px rgba(0, 0, 0, 0.3);
  animation: textFadeIn 0.6s ease-out 0.6s both;
}

@keyframes textFadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.final-hearts {
  animation: finalHeartsIn 0.8s ease-out 0.8s both;
}

@keyframes finalHeartsIn {
  from {
    opacity: 0;
    transform: scale(0);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.big-heart {
  font-size: 4rem;
  display: block;
  animation: heartBeat 1s ease-in-out infinite;
}

@keyframes heartBeat {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.3); }
}

/* Page Indicators */
.page-indicators {
  position: fixed;
  right: 30px;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  gap: 12px;
  z-index: 100;
}

.indicator-dot {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.8);
  background: transparent;
  cursor: pointer;
  transition: all 0.3s ease;
  padding: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}

.indicator-dot:hover {
  transform: scale(1.3);
  border-color: white;
}

.indicator-dot.active {
  border-color: white;
}

.indicator-dot.active .dot-inner {
  width: 8px;
  height: 8px;
  background: white;
  border-radius: 50%;
  animation: dotPop 0.3s ease-out;
}

@keyframes dotPop {
  0% { transform: scale(0); }
  70% { transform: scale(1.3); }
  100% { transform: scale(1); }
}

/* Navigation Arrows */
.nav-arrow {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: none;
  background: rgba(255, 255, 255, 0.9);
  color: #D32F2F;
  font-size: 1.2rem;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
}

.nav-arrow:hover {
  transform: translateX(-50%) scale(1.15);
  background: white;
  box-shadow: 0 6px 25px rgba(0, 0, 0, 0.3);
}

.nav-prev {
  top: 20px;
}

.nav-next {
  bottom: 20px;
  animation: bounceDown 2s ease-in-out infinite;
}

@keyframes bounceDown {
  0%, 100% { transform: translateX(-50%) translateY(0); }
  50% { transform: translateX(-50%) translateY(8px); }
}

/* Scroll Hint */
.scroll-hint {
  position: fixed;
  bottom: 80px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
  z-index: 90;
  animation: fadeInUp 1s ease-out 1s both;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateX(-50%) translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateX(-50%) translateY(0);
  }
}

.scroll-text {
  color: white;
  font-size: 0.9rem;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
  opacity: 0.8;
}

.scroll-icon {
  font-size: 1.5rem;
  animation: bounce 1.5s ease-in-out infinite;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(8px); }
}

/* Mobile Responsive */
@media (max-width: 600px) {
  .story-title {
    font-size: 1.8rem;
  }
  
  .story-text {
    font-size: 1.2rem;
  }
  
  .story-image {
    width: 220px;
  }
  
  .page-indicators {
    right: 15px;
  }
  
  .indicator-dot {
    width: 12px;
    height: 12px;
  }
  
  .nav-arrow {
    width: 40px;
    height: 40px;
    font-size: 1rem;
  }
  
  .title {
    font-size: 1.8rem;
  }
  
  .content {
    padding: 2rem;
  }
  
  .envelope {
    width: 250px;
    height: 160px;
  }
  
  .letter-text {
    font-size: 0.9rem;
  }
}

/* ====== Floral Garden Background ====== */
.floral-garden {
  background: 
    radial-gradient(ellipse at 20% 80%, rgba(255, 200, 200, 0.6) 0%, transparent 50%),
    radial-gradient(ellipse at 80% 20%, rgba(255, 220, 220, 0.5) 0%, transparent 40%),
    radial-gradient(ellipse at 50% 50%, rgba(255, 240, 240, 0.4) 0%, transparent 60%),
    linear-gradient(180deg, 
      #FFF5F5 0%, 
      #FFE4E4 20%, 
      #FFCECE 40%, 
      #FFB8B8 60%, 
      #FFA8A8 80%, 
      #FF9090 100%
    ) !important;
  position: relative;
  overflow: hidden;
}

.floral-garden::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: 
    radial-gradient(circle at 10% 90%, #C41E3A 3px, transparent 3px),
    radial-gradient(circle at 15% 85%, #E74C3C 4px, transparent 4px),
    radial-gradient(circle at 5% 80%, #FFFFFF 3px, transparent 3px),
    radial-gradient(circle at 20% 95%, #FF6B6B 5px, transparent 5px),
    radial-gradient(circle at 90% 10%, #C41E3A 4px, transparent 4px),
    radial-gradient(circle at 85% 15%, #FFFFFF 3px, transparent 3px),
    radial-gradient(circle at 95% 20%, #E74C3C 5px, transparent 5px),
    radial-gradient(circle at 80% 5%, #FF6B6B 3px, transparent 3px),
    radial-gradient(circle at 25% 5%, #FFFFFF 4px, transparent 4px),
    radial-gradient(circle at 75% 95%, #C41E3A 3px, transparent 3px),
    radial-gradient(circle at 70% 90%, #FFFFFF 4px, transparent 4px),
    radial-gradient(circle at 30% 10%, #E74C3C 3px, transparent 3px);
  pointer-events: none;
  z-index: 1;
}

.floral-garden::after {
  content: '🌹';
  position: absolute;
  bottom: 20px;
  left: 20px;
  font-size: 2rem;
  opacity: 0.6;
  animation: flowerSway 3s ease-in-out infinite;
  z-index: 1;
}

@keyframes flowerSway {
  0%, 100% { transform: rotate(-5deg); }
  50% { transform: rotate(5deg); }
}

/* Floating Petals */
.flower-petals {
  position: absolute;
  inset: 0;
  pointer-events: none;
  overflow: hidden;
  z-index: 2;
}

.petal {
  position: absolute;
  width: 15px;
  height: 15px;
  background: linear-gradient(135deg, #FF6B6B 0%, #C41E3A 100%);
  border-radius: 50% 0 50% 50%;
  opacity: 0.7;
  left: calc(var(--i) * 5%);
  top: -20px;
  animation: petalFall 8s linear infinite;
  animation-delay: calc(var(--i) * 0.4s);
}

.petal:nth-child(odd) {
  background: linear-gradient(135deg, #FFFFFF 0%, #FFE4E4 100%);
  width: 12px;
  height: 12px;
}

.petal:nth-child(3n) {
  background: linear-gradient(135deg, #E74C3C 0%, #8B0000 100%);
  width: 18px;
  height: 18px;
}

@keyframes petalFall {
  0% {
    transform: translateY(0) rotate(0deg) translateX(0);
    opacity: 0.7;
  }
  25% {
    transform: translateY(25vh) rotate(90deg) translateX(20px);
  }
  50% {
    transform: translateY(50vh) rotate(180deg) translateX(-20px);
  }
  75% {
    transform: translateY(75vh) rotate(270deg) translateX(15px);
  }
  100% {
    transform: translateY(110vh) rotate(360deg) translateX(-10px);
    opacity: 0;
  }
}

/* ====== Premium Envelope Styles ====== */
.envelope-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  padding: 2rem;
  text-align: center;
  animation: contentFadeIn 0.8s ease-out 0.2s both;
  z-index: 10;
}

.envelope-hint {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: white;
  font-size: 1rem;
  text-shadow: 1px 1px 5px rgba(0, 0, 0, 0.3);
  background: rgba(255,255,255,0.2);
  padding: 0.6rem 1.2rem;
  border-radius: 50px;
  backdrop-filter: blur(5px);
  animation: hintBounce 2s ease-in-out infinite;
}

.hint-icon {
  animation: pointUp 1s ease-in-out infinite;
}

@keyframes pointUp {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

@keyframes hintBounce {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.03); }
}

.envelope-scene {
  position: relative;
  padding: 20px;
}

.envelope-wrapper {
  cursor: pointer;
  perspective: 1500px;
  transform-style: preserve-3d;
}

.envelope {
  position: relative;
  width: 300px;
  height: 220px;
  max-width: 85vw;
  transform-style: preserve-3d;
  transition: transform 0.3s ease;
  overflow: hidden;
}

.envelope.opened {
  overflow: visible;
}

.envelope:hover:not(.opened) {
  transform: scale(1.02) rotateY(-2deg);
}

/* Envelope Back */
.envelope-back {
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, #B83227 0%, #922B21 100%);
  border-radius: 12px;
  z-index: 1;
}

/* Letter */
.letter {
  position: absolute;
  top: 40px;
  left: 25px;
  right: 25px;
  height: 220px;
  z-index: 5;
  transform: translateY(80px);
  transition: transform 0.9s cubic-bezier(0.34, 1.56, 0.64, 1) 0.3s;
}

.envelope.opened .letter {
  z-index: 50;
}

.letter.pulled {
  transform: translateY(-200px);
}

.letter-paper {
  width: 100%;
  height: 100%;
  background: 
    linear-gradient(180deg, #FFFEF9 0%, #FFF8F0 50%, #FFF5E8 100%);
  border-radius: 8px;
  padding: 1rem;
  box-sizing: border-box;
  box-shadow: 
    0 10px 40px rgba(0, 0, 0, 0.2),
    0 0 0 1px rgba(139, 69, 19, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  overflow: hidden;
}

/* Decorative border */
.letter-paper::before {
  content: '';
  position: absolute;
  inset: 8px;
  border: 2px dashed rgba(139, 69, 19, 0.15);
  border-radius: 4px;
  pointer-events: none;
}

/* Paper texture lines */
.letter-paper::after {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(
    0deg,
    transparent 0px,
    transparent 20px,
    rgba(200, 180, 160, 0.08) 20px,
    rgba(200, 180, 160, 0.08) 21px
  );
  pointer-events: none;
}

.letter-header {
  font-size: 1.5rem;
  margin-bottom: 0.3rem;
}

.letter-text {
  color: #6B4423;
  font-size: 0.85rem;
  line-height: 1.6;
  text-align: center;
  white-space: pre-line;
  margin: 0;
  font-style: italic;
  position: relative;
  z-index: 1;
  flex: 1;
}

.letter-footer {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.9rem;
  color: #8B4513;
  margin-top: 0.3rem;
}

/* Envelope Front */
.envelope-front {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 140px;
  background: 
    linear-gradient(180deg, #E74C3C 0%, #C0392B 50%, #A93226 100%);
  border-radius: 0 0 12px 12px;
  z-index: 20;
  box-shadow: 
    0 10px 30px rgba(0, 0, 0, 0.25),
    inset 0 1px 0 rgba(255,255,255,0.15);
  display: flex;
  align-items: center;
  justify-content: center;
}

/* V-shape inner shadow */
.envelope-front::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 50px;
  background: 
    linear-gradient(to right bottom, transparent 49%, rgba(0,0,0,0.08) 50%),
    linear-gradient(to left bottom, transparent 49%, rgba(0,0,0,0.08) 50%);
  background-size: 50% 100%;
  background-position: left, right;
  background-repeat: no-repeat;
}

/* Wax Seal */
.wax-seal {
  width: 55px;
  height: 55px;
  background: 
    radial-gradient(circle at 30% 30%, #C0392B 0%, #8B0000 50%, #5C0000 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 
    0 4px 15px rgba(0, 0, 0, 0.4),
    inset 0 2px 4px rgba(255,255,255,0.2),
    inset 0 -2px 4px rgba(0,0,0,0.3);
  position: relative;
  z-index: 30;
  transition: all 0.5s ease;
}

.wax-seal::before {
  content: '';
  position: absolute;
  inset: 3px;
  border-radius: 50%;
  border: 1px solid rgba(255,255,255,0.1);
}

.seal-heart {
  color: #FFD700;
  font-size: 1.5rem;
  text-shadow: 0 1px 2px rgba(0,0,0,0.3);
  transition: all 0.3s ease;
}

.wax-seal.broken {
  transform: scale(0.8) rotate(15deg);
  opacity: 0;
}

/* Envelope Flap */
.envelope-flap {
  position: absolute;
  top: 80px;
  left: 0;
  right: 0;
  height: 80px;
  background: linear-gradient(0deg, #E74C3C 0%, #EC7063 100%);
  clip-path: polygon(0 100%, 50% 0, 100% 100%);
  transform-origin: bottom center;
  transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  z-index: 25;
}

.flap-inner {
  position: absolute;
  bottom: 0;
  left: 5%;
  right: 5%;
  height: 90%;
  background: linear-gradient(0deg, #D44637 0%, #E74C3C 100%);
  clip-path: polygon(0 100%, 50% 5%, 100% 100%);
}

.envelope.opened .envelope-flap {
  transform: rotateX(-170deg);
  z-index: 2;
}

/* Celebration Hearts */
.celebration {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  pointer-events: none;
}

.celeb-heart {
  position: absolute;
  font-size: 1.5rem;
  animation: celebExplode 1.5s ease-out forwards;
  animation-delay: calc(var(--i) * 0.05s);
}

@keyframes celebExplode {
  0% {
    transform: translate(0, 0) scale(0);
    opacity: 1;
  }
  50% {
    opacity: 1;
  }
  100% {
    transform: 
      translate(
        calc(cos(var(--i) * 30deg) * 120px), 
        calc(sin(var(--i) * 30deg) * 120px - 50px)
      ) 
      scale(1) 
      rotate(calc(var(--i) * 45deg));
    opacity: 0;
  }
}

/* Fallback for browsers without cos/sin */
.celeb-heart:nth-child(1) { --tx: 100px; --ty: -30px; }
.celeb-heart:nth-child(2) { --tx: 70px; --ty: -80px; }
.celeb-heart:nth-child(3) { --tx: 0px; --ty: -100px; }
.celeb-heart:nth-child(4) { --tx: -70px; --ty: -80px; }
.celeb-heart:nth-child(5) { --tx: -100px; --ty: -30px; }
.celeb-heart:nth-child(6) { --tx: -100px; --ty: 30px; }
.celeb-heart:nth-child(7) { --tx: -70px; --ty: 80px; }
.celeb-heart:nth-child(8) { --tx: 0px; --ty: 100px; }
.celeb-heart:nth-child(9) { --tx: 70px; --ty: 80px; }
.celeb-heart:nth-child(10) { --tx: 100px; --ty: 30px; }
.celeb-heart:nth-child(11) { --tx: 50px; --ty: -90px; }
.celeb-heart:nth-child(12) { --tx: -50px; --ty: -90px; }

.celeb-heart {
  animation: celebSimple 1s ease-out forwards;
  animation-delay: calc(var(--i) * 0.04s);
}

@keyframes celebSimple {
  0% {
    transform: translate(0, 0) scale(0);
    opacity: 1;
  }
  100% {
    transform: translate(var(--tx), var(--ty)) scale(1.2);
    opacity: 0;
  }
}

/* Mobile adjustments */
@media (max-width: 600px) {
  .envelope {
    width: 260px;
    height: 190px;
  }
  
  .envelope-front {
    height: 120px;
  }
  
  .envelope-flap {
    top: 70px;
    height: 70px;
  }
  
  .letter {
    height: 190px;
    left: 20px;
    right: 20px;
  }
  
  .letter.pulled {
    transform: translateY(-170px);
  }
  
  .letter-text {
    font-size: 0.8rem;
  }
  
  .wax-seal {
    width: 45px;
    height: 45px;
  }
  
  .seal-heart {
    font-size: 1.2rem;
  }
}
</style>
