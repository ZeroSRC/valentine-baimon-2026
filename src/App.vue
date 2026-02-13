<script setup>
import { ref, onMounted, onUnmounted, watch, computed } from 'vue'

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
const SECRET_PASSWORD = 'รักตัวเล็กที่สุดในโลก'



// Story pages - แก้ไขข้อมูลตรงนี้ได้เลย!
const storyPages = [
  {
    bgImage: '/Gemini_Generated_Image_ybj2jkybj2jkybj2.png',
    title: 'ครั้งแรกที่ตัวเล็กถ่ายรูปให้พี่ ☕',
    text: 'ครั้งแรกที่พี่ได้ถ่ายรูปให้ตัวเล็กและตัวเล็กก็ถ่าย\nให้พี่พี่มีความสุขมากเลย ถึงแม้จะเป็นอะไรที่พี่ไม่เคยทำ 💕',
    isAnimated: true,
    imageSlot: false,
    image: '/Gemini_Generated_Image_ybj2jkybj2jkybj2.png'
  },
  {
    bgImage: '/beach_dance_art.png',
    title: 'ครั้งแรกที่ได้ไปเที่ยวกับตัวเล็ก 💃🌊',
    text: 'ได้พาตัวเล็กไปเที่ยวทะเลครั้งแรก\nตามที่สัญญาไว้ มีความสุขมากๆเลย 🎵',
    isAnimated: true,
    imageSlot: false,
    image: '/beach_dance_art.png'
  },
  {
    bgImage: '/Gemini_Generated_Image_zgn4tezgn4tezgn4.png',
    title: 'ครั้งแรกที่ได้ไปกินข้าวบนเรือ 💃🌊',
    text: 'อาหารรสชาติเชยๆ วิวก็ไม่สวยเท่าที่คิด\nอย่างเดียวที่ดีคือตัวเล็กสวยตลอดเวลา 🌙',
    isAnimated: true,
    imageSlot: false,
    image: '/Gemini_Generated_Image_zgn4tezgn4tezgn4.png'
  },
  {
    bgImage: '/Gemini_Generated_Image_6v4crk6v4crk6v4c.png',
    title: 'ครั้งแรกที่ดูพระอาทิตย์ขึ้น 🌅',
    text: 'นั่งดูพระอาทิตย์ขึ้นด้วยกัน\nเกือบกลับแบบไม่ได้ดูแล้ว\nแต่สุดท้ายก็ได้ดู ☀️',
    isAnimated: true,
    imageSlot: false,
    image: '/Gemini_Generated_Image_6v4crk6v4crk6v4c.png'
  },
  {
    // Envelope Page
    title: 'จดหมายรัก 💌',
    isEnvelope: true,
    isFloralBg: true,
    letterMessage: 'ถึงตัวเล็กขอบคุณที่เข้ามาในชีวิตพี่นะ ❤️ \nพาพี่ทำอะไรที่พี่ไม่เคยทำไปอีกนานๆ \nถึงแม้ว่าจะเป็นช่วงที่ดีบ้างแย่บ้าง แต่พี่ก็ดีใจที่มีตัวเล็กอยู่ข้างๆ\nพี่รักตัวเล็กที่สุดในโลก\nสุขสันต์วันวาเลนไทน์นะค้าบ'
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
    }, 500)
  }
}

const toggleZoom = () => {
  if (letterPulled.value) {
    isLetterZoomed.value = !isLetterZoomed.value
  }
}

const createHeart = (e) => {
  const heart = document.createElement('div')
  heart.classList.add('click-heart')
  heart.innerHTML = '💖'
  heart.style.left = `${e.clientX}px`
  heart.style.top = `${e.clientY}px`
  document.body.appendChild(heart)
  
  setTimeout(() => {
    heart.remove()
  }, 1000)
}



// --- Typewriter Effect ---
const displayedText = ref('')
let typeInterval = null

const startTyping = (text) => {
  if (!text) return
  clearInterval(typeInterval)
  displayedText.value = ''
  let i = 0
  
  // Typing speed
  typeInterval = setInterval(() => {
    if (i < text.length) {
      displayedText.value += text.charAt(i)
      i++
    } else {
      clearInterval(typeInterval)
    }
  }, 40)
}

watch(currentPage, (newVal) => {
  // If we are in story mode (step === 'story') and page has text
  if (step.value === 'story' && storyPages[newVal] && !storyPages[newVal].isEnvelope) {
    startTyping(storyPages[newVal].text)
  }
})

watch(step, (newVal) => {
  if (newVal === 'story') {
    // Start typing first page text when entering story mode
    setTimeout(() => {
       if (storyPages[0] && !storyPages[0].isEnvelope) {
         startTyping(storyPages[0].text)
       }
    }, 1000)
  }
})



onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
  window.addEventListener('click', createHeart)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
  window.removeEventListener('click', createHeart)
  clearInterval(typeInterval)
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
      <h1 class="title">รักพี่มั้ยยยย ?</h1>
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
      <h1 class="title">พี่ชอบบอกรักตัวเล็กว่ายังไง! 💕</h1>
      <img src="/kitty-kitty-heart.gif" alt="Cute Kitty" class="main-image" />
      
      <div class="password-section">
        <p class="password-hint">ใส่รหัสลับแห่งความรัก 🔐</p>
        <input 
          type="text" 
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
            :class="{ 'floral-garden': page.isFloralBg, 'with-bg-image': page.bgImage }"
            :style="{ background: page.bgGradient }"
          >
            <!-- Background Image -->
            <div 
              v-if="page.bgImage" 
              class="page-bg-image"
              :class="{ 'animate-pan': page.isAnimated }"
              :style="{ backgroundImage: `url(${page.bgImage})` }"
            ></div>
            
            <!-- Dark Overlay for Readability -->
            <div v-if="page.bgImage" class="bg-overlay"></div>

            <!-- Floating Flower Petals for Envelope Page -->
            <div v-if="page.isFloralBg" class="flower-petals">
              <span v-for="i in 20" :key="'petal-'+i" class="petal" :style="{ '--i': i }"></span>
            </div>
            
            <!-- Regular Story Content -->
            <div v-if="!page.isEnvelope" class="story-content" :class="{ 'glass-card': page.bgImage }">
              <div class="page-number-badge">{{ index + 1 }} / {{ storyPages.length }}</div>
              
              <h2 class="story-title">{{ page.title }}</h2>
              
              <!-- Image Slot for User to Paste Their Own Image -->
              <div v-if="page.imageSlot" class="image-slot">
                <div class="slot-placeholder">
                  <span class="slot-icon">🖼️</span>
                  <p class="slot-text">แปะรูปข้อความตรงนี้</p>
                  <p class="slot-hint">(วางไฟล์รูปของคุณที่นี่)</p>
                </div>
              </div>
              
              <!-- Optional: Keep generating standard image if you want, but we rely on BG now -->
              <!-- Optional: Keep generating standard image if you want, but we rely on BG now -->
              <div v-if="page.image" class="story-image-container">
                <div class="photo-frame">
                   <div class="pin">📌</div>
                   <img :src="page.image" :alt="page.title" class="story-image" />
                   <div class="tape-corner"></div>
                </div>
              </div>

              
              <p class="story-text">
                {{ index === currentPage ? displayedText : page.text }}
                <span v-if="index === currentPage && displayedText.length < page.text.length" class="cursor">|</span>
              </p>
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
                    <div class="letter" :class="{ pulled: letterPulled, 'clickable': letterPulled }" @click.stop="toggleZoom">
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
                        <div class="zoom-hint" v-if="letterPulled && !isLetterZoomed">
                          <span>🔍 แตะเพื่ออ่าน</span>
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
              
              <!-- Zoomed Letter Overlay -->
              <Transition name="zoom-fade">
                <div v-if="isLetterZoomed" class="letter-overlay" @click="toggleZoom">
                  <div class="zoomed-letter-paper" @click.stop>
                    <button class="close-btn" @click="toggleZoom">×</button>
                    <div class="letter-header">
                      <span>💌</span>
                    </div>
                    <p class="letter-text expanded">{{ page.letterMessage }}</p>
                    <div class="letter-footer">
                      <span>💕</span>
                      <span>รักนะจุ๊บๆ</span>
                      <span>💕</span>
                    </div>
                  </div>
                </div>
              </Transition>
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
  cursor: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewport='0 0 100 100' style='fill:black;font-size:24px;'><text y='50%'>💘</text></svg>") 16 0, auto;
}

#app {
  width: 100vw;
  height: 100vh;
}

.click-heart {
  position: fixed;
  pointer-events: none;
  font-size: 1.5rem;
  animation: floatHeart 1s ease-out forwards;
  z-index: 9999;
}

@keyframes floatHeart {
  0% { transform: scale(0.5) translate(0, 0); opacity: 1; }
  100% { transform: scale(1.5) translate(0, -50px); opacity: 0; }
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

/* BACKGROUND IMAGE STYLES */
.page-bg-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  z-index: -2;
  transition: transform 10s ease-in-out;
}

.animate-pan {
  animation: bgPan 20s ease-in-out infinite alternate;
}

@keyframes bgPan {
  0% { transform: scale(1.1) translate(0, 0); }
  100% { transform: scale(1.2) translate(-2%, -2%); }
}

.bg-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.35); /* Darken bg for text readability */
  backdrop-filter: blur(2px);
  z-index: -1;
}

.glass-card {
  background: rgba(255, 255, 255, 0.15) !important;
  backdrop-filter: blur(15px) !important;
  border: 1px solid rgba(255, 255, 255, 0.3) !important;
  box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2) !important;
  color: white;
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

.photo-frame {
  background: white;
  padding: 15px 15px 40px 15px;
  border-radius: 4px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  transform: rotate(-3deg);
  transition: all 0.4s ease;
  position: relative;
  max-width: 320px;
}

.photo-frame:hover {
  transform: rotate(0deg) scale(1.05);
  box-shadow: 0 15px 40px rgba(0,0,0,0.4);
  z-index: 10;
}

.story-image {
  display: block;
  width: 100%;
  height: auto;
  border: 1px solid #eee;
  /* Remove old styles */
  border-radius: 0; 
  box-shadow: none;
}

.pin {
  position: absolute;
  top: -15px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 2rem;
  z-index: 5;
  filter: drop-shadow(2px 4px 6px rgba(0,0,0,0.3));
}

.tape-corner {
  position: absolute;
  top: -10px;
  right: -10px;
  width: 80px;
  height: 30px;
  background: rgba(255, 255, 255, 0.4);
  transform: rotate(45deg);
  box-shadow: 0 1px 3px rgba(0,0,0,0.2);
  display: none; /* simple pin is cleaner, but keeping css just in case */
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

/* ====== Typewriter Cursor ====== */
.cursor {
  display: inline-block;
  width: 2px;
  animation: blink 1s infinite;
  color: #fff;
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}



/* ====== Image Slot Styles ====== */
.image-slot {
  width: 100%;
  max-width: 400px;
  min-height: 250px;
  background: rgba(255, 255, 255, 0.25);
  backdrop-filter: blur(10px);
  border: 3px dashed rgba(255, 255, 255, 0.6);
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 2rem;
  transition: all 0.3s ease;
  animation: imageSlotPulse 2s ease-in-out infinite;
}

.image-slot:hover {
  background: rgba(255, 255, 255, 0.35);
  border-color: rgba(255, 255, 255, 0.9);
  transform: scale(1.02);
}

@keyframes imageSlotPulse {
  0%, 100% {
    box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
  }
  50% {
    box-shadow: 0 0 30px rgba(255, 255, 255, 0.5);
  }
}

.slot-placeholder {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.slot-icon {
  font-size: 3rem;
  animation: iconFloat 2s ease-in-out infinite;
}

@keyframes iconFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.slot-text {
  color: white;
  font-size: 1.3rem;
  font-weight: bold;
  margin: 0;
  text-shadow: 2px 2px 8px rgba(0, 0, 0, 0.3);
}

.slot-hint {
  color: rgba(255, 255, 255, 0.9);
  font-size: 0.9rem;
  margin: 0;
  font-style: italic;
  text-shadow: 1px 1px 5px rgba(0, 0, 0, 0.3);
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

/* ====== Standard Premium Envelope ====== */
.envelope-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 2rem;
  padding: 2rem;
  text-align: center;
  animation: contentFadeIn 0.8s ease-out 0.2s both;
  position: relative;
  z-index: 10;
}

.envelope-hint {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: white;
  font-size: 1rem;
  text-shadow: 1px 1px 5px rgba(0, 0, 0, 0.3);
  background: rgba(255,255,255,0.25);
  padding: 0.6rem 1.2rem;
  border-radius: 50px;
  backdrop-filter: blur(5px);
  animation: hintBounce 2s ease-in-out infinite;
  z-index: 50;
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

.envelope-wrapper {
  position: relative;
  width: 300px;
  height: 200px;
  max-width: 85vw;
  perspective: 1500px;
  cursor: pointer;
}

/* Base Envelope Container */
.envelope {
  position: relative;
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
  transition: transform 0.3s ease;
}

.envelope:hover:not(.opened) {
  transform: rotate(-2deg) scale(1.02);
}

/* 1. Envelope Back (Interior) */
.envelope-back {
  position: absolute;
  inset: 0;
  background: #C0392B;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.3);
  z-index: 1;
}

/* 2. Letter - Sits inside */
.letter {
  position: absolute;
  top: 10px;
  left: 15px;
  right: 15px;
  height: 180px;
  background: linear-gradient(180deg, #FFFEF9 0%, #FFF5E8 100%);
  border-radius: 6px;
  z-index: 5;
  transition: transform 0.8s cubic-bezier(0.4, 0, 0.2, 1) 0.4s; /* Slight delay */
  display: flex;
  justify-content: center;
  overflow: hidden;
  box-shadow: 0 -2px 10px rgba(0,0,0,0.1);
}

/* Ensure pulled letter is interactive */
.letter.pulled {
  transform: translateY(-160px) translateZ(100px);
  z-index: 100 !important;
  pointer-events: auto;
  transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1); /* Smooth transition */
}

/* HOVER EFFECT: Zoom / Expand on Hover */
.letter.pulled:hover {
  transform: translateY(-200px) scale(1.5) translateZ(200px);
  z-index: 200 !important;
  box-shadow: 0 20px 50px rgba(0,0,0,0.4);
  /* Allow height to grow to fit text */
  height: auto; 
  overflow: visible;
}

/* Adjust paper content for hover state */
.letter.pulled:hover .letter-paper {
  overflow: visible;
  height: auto;
  min-height: 100%;
  padding-bottom: 2rem; 
}

/* Letter Content */
.letter-paper {
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, #FFFEF9 0%, #FFF5E8 100%);
  border-radius: 8px;
  padding: 1rem;
  box-sizing: border-box;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  overflow: hidden;
  transition: all 0.4s ease;
}

/* Hide fade mask on hover to read better */
.letter.pulled:hover .letter-paper::after {
  opacity: 0;
  display: none; /* Completely remove it to avoid blocking clicks/selection */
}

/* Expand text on hover */
.letter.pulled:hover .letter-text {
  -webkit-line-clamp: unset; /* Show all lines */
  overflow: visible;
  height: auto;
  font-size: 0.9rem; /* Increase font size slightly */
}

/* ... rest of existing CSS ... */
.letter-paper::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 40px;
  background: linear-gradient(to bottom, rgba(255,255,255,0), #FFF5E8);
  pointer-events: none;
  z-index: 5;
  transition: opacity 0.3s;
}

.letter-paper::before {
  content: '';
  position: absolute;
  inset: 6px;
  border: 1px dashed rgba(139, 69, 19, 0.2);
  border-radius: 4px;
  pointer-events: none;
}

.letter-text {
  color: #6B4423;
  font-size: 0.8rem;
  line-height: 1.5;
  text-align: center;
  white-space: pre-wrap;
  margin: 0;
  font-style: italic;
  position: relative;
  z-index: 2;
  width: 100%;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 6;
  -webkit-box-orient: vertical;
  transition: all 0.3s ease;
}

.zoom-hint {
  /* Hide hint on hover since we are zooming */
  transition: opacity 0.3s;
}
.letter.pulled:hover .zoom-hint {
  opacity: 0;
}

/* Zoom Overlay Styles */
.letter.clickable {
  cursor: zoom-in;
}

.zoom-hint {
  position: absolute;
  bottom: 5px;
  left: 0;
  right: 0;
  text-align: center;
  font-size: 0.75rem;
  font-weight: bold;
  color: #d35400;
  text-shadow: 0 0 2px rgba(255,255,255,0.8);
  animation: pulse 2s infinite;
  z-index: 10;
}

/* ... existing overlay styles ... */

.zoomed-letter-paper {
  background: linear-gradient(180deg, #FFFEFA 0%, #FFF8F0 100%);
  width: 90%;
  max-width: 500px;
  max-height: 85vh; /* Limit height */
  overflow-y: auto; /* Allow scrolling */
  padding: 2.5rem 2rem;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  transform-origin: center;
  animation: zoomPop 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

/* Custom Scrollbar for zoomed letter */
.zoomed-letter-paper::-webkit-scrollbar {
  width: 6px;
}
.zoomed-letter-paper::-webkit-scrollbar-track {
  background: transparent;
}
.zoomed-letter-paper::-webkit-scrollbar-thumb {
  background: rgba(139, 69, 19, 0.2);
  border-radius: 10px;
}

.letter-text.expanded {
  font-size: 1.1rem;
  line-height: 1.8;
  white-space: pre-wrap;
  color: #5D4037;
  overflow: visible;
  display: block;
  -webkit-line-clamp: unset;
}

.letter-decoration, .letter-header, .letter-footer {
  font-size: 1.2rem;
  display: flex;
  gap: 5px;
}

/* 3. Envelope Front (Pocket) */
.envelope-front {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, #E74C3C 0%, #CB4335 100%);
  border-radius: 12px;
  z-index: 10;
  /* Deep V Cut */
  clip-path: polygon(0 0, 50% 55%, 100% 0, 100% 100%, 0 100%);
  /* Add a pseudo-element for depth/shadow if needed, but clip-path crops it. 
     Instead, usage solid color or gradient is safer. */
}

/* 4. Envelope Flap (Lid) */
.envelope-flap {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 110px; /* Extends past half-way */
  background: linear-gradient(0deg, #C0392B 0%, #D98880 100%);
  /* Triangle pointing DOWN */
  clip-path: polygon(0 0, 50% 100%, 100% 0);
  transform-origin: top center;
  transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1), z-index 0.6s step-end;
  z-index: 20;
}

/* Open Animation */
.envelope.opened .envelope-flap {
  transform: rotateX(180deg);
  z-index: 1; /* Move behind back when open */
}

/* 5. Wax Seal - Attached to Flap Tip */
.wax-seal {
  position: absolute;
  bottom: 10px; /* Near bottom tip of flap */
  left: 50%;
  transform: translateX(-50%);
  width: 50px;
  height: 50px;
  background: radial-gradient(circle at 35% 35%, #922B21 0%, #641E16 100%);
  border-radius: 50%;
  box-shadow: 0 4px 10px rgba(0,0,0,0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 25;
}

.seal-heart {
  font-size: 1.4rem;
  color: #D4AC0D; /* Gold */
  text-shadow: 0 1px 2px rgba(0,0,0,0.5);
}

.envelope.opened .wax-seal {
  opacity: 0;
  transition: opacity 0.3s;
}

/* Celebrations */
.celebration {
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 60;
}

.celeb-heart {
  position: absolute;
  font-size: 1.5rem;
  opacity: 0;
}

/* Add burst logic identical to previous */
/* Animation Keyframes - Simple Burst */
.celeb-heart {
  animation: celebBurst 1s ease-out forwards;
  animation-delay: calc(var(--i) * 0.05s);
}

@keyframes celebBurst {
  0% { transform: translate(0, 0) scale(0); opacity: 1; }
  100% {
    transform: translate(var(--tx), var(--ty)) scale(1.2) rotate(45deg);
    opacity: 0;
  }
}

/* Fallback for browsers */
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

@media (max-width: 600px) {
  .envelope-wrapper { width: 280px; height: 180px; }
  .envelope-flap { height: 100px; clip-path: polygon(0 0, 50% 100%, 100% 0); }
  .letter { height: 160px; }
  .letter.pulled { transform: translateY(-130px); }
  .envelope-front { clip-path: polygon(0 0, 50% 50%, 100% 0, 100% 100%, 0 100%); }
}

/* Zoom Overlay Styles */
.letter.clickable {
  cursor: zoom-in;
}

.zoom-hint {
  position: absolute;
  bottom: 5px;
  left: 0;
  right: 0;
  text-align: center;
  font-size: 0.7rem;
  color: rgba(139, 69, 19, 0.5);
  animation: pulse 2s infinite;
}

.letter-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(5px);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.zoomed-letter-paper {
  background: linear-gradient(180deg, #FFFEFA 0%, #FFF8F0 100%);
  width: 100%;
  max-width: 500px;
  min-height: 300px;
  padding: 2.5rem 2rem;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  transform-origin: center;
  animation: zoomPop 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.zoomed-letter-paper::before {
  content: '';
  position: absolute;
  inset: 12px;
  border: 2px dashed rgba(139, 69, 19, 0.2);
  border-radius: 8px;
  pointer-events: none;
}

.letter-text.expanded {
  font-size: 1.2rem;
  line-height: 1.8;
  white-space: pre-wrap;
  color: #5D4037;
}

.close-btn {
  position: absolute;
  top: 10px;
  right: 15px;
  background: none;
  border: none;
  font-size: 2rem;
  color: #8B4513;
  cursor: pointer;
  opacity: 0.6;
  transition: opacity 0.3s;
  z-index: 10;
}

.close-btn:hover {
  opacity: 1;
}

/* Zoom Transitions */
.zoom-fade-enter-active,
.zoom-fade-leave-active {
  transition: opacity 0.3s ease;
}

.zoom-fade-enter-from,
.zoom-fade-leave-to {
  opacity: 0;
}

@keyframes zoomPop {
  0% { transform: scale(0.8); opacity: 0; }
  100% { transform: scale(1); opacity: 1; }
}
</style>
