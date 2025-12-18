<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch } from 'vue'
import { useNav } from '@slidev/client'

const { currentSlideNo } = useNav()

const taxiPosition = ref(-250)
let animationId: number | null = null

// Audio context
let audioContext: AudioContext | null = null

// NYC Taxi Horn - classic honk sound
const playHorn = () => {
  try {
    if (!audioContext) {
      audioContext = new (window.AudioContext || (window as any).webkitAudioContext)()
    }
    
    const now = audioContext.currentTime
    
    // Classic taxi horn - two tones (like a car horn)
    const createHornTone = (freq: number, start: number, dur: number) => {
      const osc = audioContext!.createOscillator()
      const gain = audioContext!.createGain()
      const filter = audioContext!.createBiquadFilter()
      
      // Sawtooth gives that brassy horn quality
      osc.type = 'sawtooth'
      osc.frequency.setValueAtTime(freq, start)
      
      // Low-pass filter to soften the harsh edges
      filter.type = 'lowpass'
      filter.frequency.setValueAtTime(1200, start)
      filter.Q.setValueAtTime(1, start)
      
      osc.connect(filter)
      filter.connect(gain)
      gain.connect(audioContext!.destination)
      
      // Horn envelope - quick attack, sustain, quick release
      gain.gain.setValueAtTime(0, start)
      gain.gain.linearRampToValueAtTime(0.08, start + 0.02)
      gain.gain.setValueAtTime(0.08, start + dur - 0.05)
      gain.gain.linearRampToValueAtTime(0, start + dur)
      
      osc.start(start)
      osc.stop(start + dur)
    }
    
    // Classic car horn: two quick beeps
    createHornTone(340, now, 0.15)          // First beep (F4)
    createHornTone(280, now + 0.18, 0.22)   // Second beep (C#4/Db4)
    
  } catch (e) {
    // Audio blocked
  }
}

const animateTaxi = (playSound = false) => {
  // Cancel any existing animation
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
  
  taxiPosition.value = -250
  
  // Play taxi horn only on slide change
  if (playSound) {
    setTimeout(playHorn, 100)
  }
  
  // Continuous loop animation: 8 seconds per pass
  const duration = 8000
  const startTime = performance.now()
  const startPos = -250
  const endPos = window.innerWidth + 250
  
  const animate = (currentTime: number) => {
    const elapsed = currentTime - startTime
    const progress = (elapsed % duration) / duration
    
    // Linear movement for continuous loop
    taxiPosition.value = startPos + (endPos - startPos) * progress
    
    // Reset position when it goes off screen
    if (progress >= 0.99) {
      taxiPosition.value = startPos
    }
    
    animationId = requestAnimationFrame(animate)
  }
  
  animationId = requestAnimationFrame(animate)
}

watch(currentSlideNo, () => {
  animateTaxi(true)
})

onMounted(() => {
  const initAudio = () => {
    if (!audioContext) {
      audioContext = new (window.AudioContext || (window as any).webkitAudioContext)()
    }
    document.removeEventListener('click', initAudio)
    document.removeEventListener('keydown', initAudio)
  }
  document.addEventListener('click', initAudio)
  document.addEventListener('keydown', initAudio)
  
  setTimeout(() => animateTaxi(false), 600)
})

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
  if (audioContext) {
    audioContext.close()
    audioContext = null
  }
})
</script>

<template>
  <!-- Road - Always visible -->
  <div class="road-container">
    <div class="curb-top"></div>
    <div class="road">
      <div class="lane-marker"></div>
    </div>
    <div class="sidewalk"></div>
  </div>
  
  <!-- Buildings - Always visible -->
  <div class="buildings">
    <div class="bldg" style="left: 5%; height: 70px; width: 45px;"></div>
    <div class="bldg" style="left: 12%; height: 110px; width: 35px;"></div>
    <div class="bldg" style="left: 20%; height: 85px; width: 50px;"></div>
    <div class="bldg" style="left: 30%; height: 130px; width: 30px;"></div>
    <div class="bldg" style="left: 40%; height: 75px; width: 55px;"></div>
    <div class="bldg" style="left: 52%; height: 100px; width: 40px;"></div>
    <div class="bldg" style="left: 62%; height: 120px; width: 32px;"></div>
    <div class="bldg" style="left: 72%; height: 65px; width: 48px;"></div>
    <div class="bldg" style="left: 82%; height: 95px; width: 38px;"></div>
    <div class="bldg" style="left: 92%; height: 80px; width: 42px;"></div>
  </div>
  
  <!-- Taxi - Always visible, continuously moving -->
  <div 
    class="taxi-container"
    :style="{ transform: `translateX(${taxiPosition}px)` }"
  >
    <div class="taxi">
      <!-- Shadow -->
      <div class="shadow"></div>
      
      <!-- Body -->
      <div class="body"></div>
      <div class="cabin"></div>
      
      <!-- Roof sign -->
      <div class="roof-sign">
        <span>TAXI</span>
      </div>
      
      <!-- Windows -->
      <div class="window-rear"></div>
      <div class="window-front"></div>
      
      <!-- Bumpers -->
      <div class="bumper-front"></div>
      <div class="bumper-rear"></div>
      
      <!-- Lights -->
      <div class="headlight"></div>
      <div class="taillight"></div>
      
      <!-- Wheels -->
      <div class="wheel front">
        <div class="rim"></div>
      </div>
      <div class="wheel rear">
        <div class="rim"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* ============ ROAD ============ */
.road-container {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100;
  pointer-events: none;
}

.curb-top {
  height: 4px;
  background: #4a4a4a;
}

.road {
  height: 42px;
  background: #2d2d2d;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.lane-marker {
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  height: 3px;
  transform: translateY(-50%);
  background: repeating-linear-gradient(
    90deg,
    #f4d03f 0px,
    #f4d03f 40px,
    transparent 40px,
    transparent 70px
  );
  opacity: 0.9;
}

.sidewalk {
  height: 8px;
  background: linear-gradient(180deg, #555 0%, #444 100%);
}

/* ============ BUILDINGS ============ */
.buildings {
  position: fixed;
  bottom: 54px;
  left: 0;
  right: 0;
  height: 140px;
  z-index: 50;
  pointer-events: none;
}

.bldg {
  position: absolute;
  bottom: 0;
  background: linear-gradient(180deg, #2a2a3a 0%, #1a1a25 100%);
  opacity: 0.12;
  border-radius: 2px 2px 0 0;
}

/* ============ TAXI ============ */
.taxi-container {
  position: fixed;
  bottom: 14px;
  left: 0;
  z-index: 150;
  pointer-events: none;
  will-change: transform;
}

.taxi {
  position: relative;
  width: 160px;
  height: 65px;
}

/* Gentle bounce */
.taxi {
  animation: gentleBounce 0.4s ease-in-out infinite;
}

@keyframes gentleBounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-1.5px); }
}

/* Shadow under car */
.shadow {
  position: absolute;
  bottom: -2px;
  left: 20px;
  right: 20px;
  height: 6px;
  background: radial-gradient(ellipse, rgba(0,0,0,0.3) 0%, transparent 70%);
  border-radius: 50%;
}

/* Main body */
.body {
  position: absolute;
  bottom: 10px;
  left: 12px;
  right: 12px;
  height: 22px;
  background: linear-gradient(180deg, #ffd93d 0%, #f4c430 50%, #d4a800 100%);
  border-radius: 4px;
}

/* Cabin */
.cabin {
  position: absolute;
  bottom: 30px;
  left: 35px;
  width: 80px;
  height: 25px;
  background: linear-gradient(180deg, #ffe066 0%, #ffd93d 100%);
  border-radius: 8px 8px 0 0;
}

/* Roof sign */
.roof-sign {
  position: absolute;
  bottom: 53px;
  left: 50%;
  transform: translateX(-50%);
  background: #ffffff;
  padding: 2px 8px;
  border-radius: 3px;
  box-shadow: 0 0 6px rgba(255,255,255,0.5);
}

.roof-sign span {
  font-size: 8px;
  font-weight: 700;
  color: #1a1a1a;
  letter-spacing: 1px;
  font-family: Arial, sans-serif;
}

/* Windows */
.window-rear {
  position: absolute;
  bottom: 33px;
  left: 40px;
  width: 32px;
  height: 18px;
  background: linear-gradient(180deg, #a8d4ff 0%, #6bb8ff 100%);
  border-radius: 5px 2px 0 0;
  opacity: 0.9;
}

.window-front {
  position: absolute;
  bottom: 33px;
  left: 78px;
  width: 32px;
  height: 18px;
  background: linear-gradient(180deg, #a8d4ff 0%, #6bb8ff 100%);
  border-radius: 2px 5px 0 0;
  opacity: 0.9;
}

/* Bumpers */
.bumper-front {
  position: absolute;
  bottom: 12px;
  right: 6px;
  width: 10px;
  height: 16px;
  background: #888;
  border-radius: 0 3px 3px 0;
}

.bumper-rear {
  position: absolute;
  bottom: 12px;
  left: 6px;
  width: 10px;
  height: 16px;
  background: #888;
  border-radius: 3px 0 0 3px;
}

/* Lights */
.headlight {
  position: absolute;
  bottom: 18px;
  right: 10px;
  width: 6px;
  height: 8px;
  background: #fffbe6;
  border-radius: 2px;
  box-shadow: 0 0 8px rgba(255,251,230,0.8);
}

.taillight {
  position: absolute;
  bottom: 18px;
  left: 10px;
  width: 5px;
  height: 7px;
  background: #ff4444;
  border-radius: 2px;
  box-shadow: 0 0 4px rgba(255,68,68,0.6);
}

/* Wheels */
.wheel {
  position: absolute;
  bottom: 0;
  width: 24px;
  height: 24px;
  background: #1a1a1a;
  border-radius: 50%;
  border: 3px solid #333;
}

.wheel.front {
  right: 25px;
  animation: wheelRoll 0.6s linear infinite;
}

.wheel.rear {
  left: 25px;
  animation: wheelRoll 0.6s linear infinite;
}

@keyframes wheelRoll {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.rim {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 10px;
  height: 10px;
  background: radial-gradient(circle, #aaa 0%, #666 100%);
  border-radius: 50%;
  transform: translate(-50%, -50%);
}

/* Fade transition */
.taxi-fade-enter-active {
  transition: opacity 0.3s ease;
}

.taxi-fade-leave-active {
  transition: opacity 0.2s ease;
}

.taxi-fade-enter-from,
.taxi-fade-leave-to {
  opacity: 0;
}
</style>
