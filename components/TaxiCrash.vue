<script setup>
import { ref, onMounted } from 'vue'

const animationPhase = ref('waiting') // waiting, driving, impact, cracked, reveal
const isAnimating = ref(false)

// Audio context for sound effects
let audioCtx = null

const initAudio = async () => {
  if (!audioCtx) {
    audioCtx = new (window.AudioContext || window.webkitAudioContext)()
  }
  // Resume audio context if it's suspended (browser autoplay policy)
  if (audioCtx.state === 'suspended') {
    await audioCtx.resume()
  }
  return audioCtx
}

// Skid/screech sound effect - intense tire screech
const playSkidSound = async () => {
  const ctx = await initAudio()
  const duration = 1.5
  
  // Create noise buffer for screech
  const bufferSize = ctx.sampleRate * duration
  const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate)
  const data = buffer.getChannelData(0)
  
  for (let i = 0; i < bufferSize; i++) {
    const t = i / ctx.sampleRate
    // Multiple frequencies for more realistic tire screech
    const freq1 = 1200 + Math.sin(t * 80) * 600
    const freq2 = 800 + Math.sin(t * 40) * 300
    const freq3 = 2000 + Math.sin(t * 120) * 800
    
    // Intensity varies - starts strong, slight dip, then builds up again
    const envelope = Math.sin(t / duration * Math.PI) * 0.7 + 0.3
    
    const noise = Math.random() * 2 - 1
    data[i] = noise * (
      Math.sin(t * freq1) * 0.4 + 
      Math.sin(t * freq2) * 0.3 + 
      Math.sin(t * freq3) * 0.2
    ) * envelope * 0.6
  }
  
  const source = ctx.createBufferSource()
  source.buffer = buffer
  
  // Bandpass filter for that tire screech frequency
  const filter = ctx.createBiquadFilter()
  filter.type = 'bandpass'
  filter.frequency.value = 2500
  filter.Q.value = 3
  
  const gain = ctx.createGain()
  gain.gain.value = 1.0
  
  source.connect(filter)
  filter.connect(gain)
  gain.connect(ctx.destination)
  source.start()
}

// Realistic NYC taxi horn - electromagnetic horn simulation
const playHornSound = async () => {
  const ctx = await initAudio()
  const duration = 1.0
  const now = ctx.currentTime
  
  // Real car horns use electromagnetic vibrating diaphragms
  // This creates a buzzy, brassy sound with lots of harmonics
  
  // Create the horn sound using buffer with realistic waveform
  const sampleRate = ctx.sampleRate
  const bufferSize = sampleRate * duration
  const buffer = ctx.createBuffer(1, bufferSize, sampleRate)
  const data = buffer.getChannelData(0)
  
  // Fundamental frequencies (real car horns are around 300-500Hz)
  const f1 = 370  // Primary note
  const f2 = 415  // Secondary note (minor third up - classic dual-tone horn)
  
  for (let i = 0; i < bufferSize; i++) {
    const t = i / sampleRate
    
    // Envelope - quick attack, sustain, quick release
    let envelope = 1.0
    if (t < 0.02) {
      envelope = t / 0.02  // 20ms attack
    } else if (t > duration - 0.05) {
      envelope = (duration - t) / 0.05  // 50ms release
    }
    
    // Slight pitch bend at start (horn spinning up)
    const bendFactor = t < 0.05 ? 0.95 + (t / 0.05) * 0.05 : 1.0
    
    // Generate buzzy horn tone with lots of harmonics (like a real electromagnetic horn)
    let sample = 0
    
    // Fundamental + harmonics for first tone
    for (let h = 1; h <= 8; h++) {
      const harmAmp = 1.0 / (h * 0.8)  // Harmonics don't fall off too fast
      sample += Math.sin(2 * Math.PI * f1 * bendFactor * h * t) * harmAmp
    }
    
    // Second tone + harmonics
    for (let h = 1; h <= 8; h++) {
      const harmAmp = 1.0 / (h * 0.8)
      sample += Math.sin(2 * Math.PI * f2 * bendFactor * h * t) * harmAmp
    }
    
    // Add some "buzz" - the electromagnetic vibration
    const buzz = Math.sin(2 * Math.PI * 120 * t) * 0.15
    
    // Add slight noise for air/resonance
    const noise = (Math.random() * 2 - 1) * 0.03
    
    // Soft clip for that compressed horn sound
    sample = Math.tanh((sample * 0.3 + buzz + noise) * 2)
    
    data[i] = sample * envelope * 0.6
  }
  
  const source = ctx.createBufferSource()
  source.buffer = buffer
  
  // Bandpass filter to shape the horn resonance
  const filter = ctx.createBiquadFilter()
  filter.type = 'bandpass'
  filter.frequency.value = 800
  filter.Q.value = 0.7
  
  // Add some room reverb feel
  const convolver = ctx.createGain()  // Simple gain instead of convolver
  convolver.gain.value = 1.0
  
  source.connect(filter)
  filter.connect(convolver)
  convolver.connect(ctx.destination)
  
  source.start(now)
}

// Crash/impact sound
const playCrashSound = async () => {
  const ctx = await initAudio()
  const duration = 0.8
  
  // Create noise buffer for crash
  const bufferSize = ctx.sampleRate * duration
  const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate)
  const data = buffer.getChannelData(0)
  
  for (let i = 0; i < bufferSize; i++) {
    const t = i / ctx.sampleRate
    // Explosion-like noise with quick decay
    const decay = Math.exp(-t * 8)
    data[i] = (Math.random() * 2 - 1) * decay
  }
  
  const source = ctx.createBufferSource()
  source.buffer = buffer
  
  // Low-pass filter for bass impact
  const filter = ctx.createBiquadFilter()
  filter.type = 'lowpass'
  filter.frequency.value = 800
  
  const gain = ctx.createGain()
  gain.gain.value = 0.8
  
  source.connect(filter)
  filter.connect(gain)
  gain.connect(ctx.destination)
  source.start()
  
  // Add glass shatter sound
  setTimeout(async () => {
    const shatterBuffer = ctx.createBuffer(1, ctx.sampleRate * 0.5, ctx.sampleRate)
    const shatterData = shatterBuffer.getChannelData(0)
    for (let i = 0; i < shatterBuffer.length; i++) {
      const t = i / ctx.sampleRate
      shatterData[i] = (Math.random() * 2 - 1) * Math.exp(-t * 15) * 0.5
    }
    const shatterSource = ctx.createBufferSource()
    shatterSource.buffer = shatterBuffer
    const highpass = ctx.createBiquadFilter()
    highpass.type = 'highpass'
    highpass.frequency.value = 3000
    const shatterGain = ctx.createGain()
    shatterGain.gain.value = 0.6
    shatterSource.connect(highpass)
    highpass.connect(shatterGain)
    shatterGain.connect(ctx.destination)
    shatterSource.start()
  }, 100)
}

const startAnimation = async () => {
  if (isAnimating.value) return
  isAnimating.value = true
  
  // Initialize audio context on user gesture
  await initAudio()
  
  animationPhase.value = 'driving'
  
  // Play intense skid sound immediately
  playSkidSound()
  
  // One long angry horn honk
  setTimeout(playHornSound, 200)
  
  // Taxi drives toward screen - fast approach
  setTimeout(() => {
    animationPhase.value = 'impact'
    playCrashSound()
  }, 1200)
  
  // Screen cracks appear
  setTimeout(() => {
    animationPhase.value = 'cracked'
  }, 1400)
  
  // Screen shatters and reveals thank you
  setTimeout(() => {
    animationPhase.value = 'reveal'
  }, 3200)
}

// Don't auto-start - wait for user click to enable audio
onMounted(() => {
  // Animation will start when user clicks
})
</script>

<template>
  <div class="taxi-crash-container" @click="startAnimation">
    <!-- Taxi approaching - 3D perspective -->
    <div 
      v-if="animationPhase === 'waiting' || animationPhase === 'driving'" 
      class="taxi-center-wrapper"
    >
      <div 
        class="taxi-3d-wrapper"
        :class="{ 'driving': animationPhase === 'driving' }"
      >
        <div class="taxi-front">
          <!-- Front of taxi (hood view) -->
          <div class="hood">
          <div class="hood-top"></div>
          <div class="windshield">
            <div class="windshield-reflection"></div>
          </div>
          <div class="hood-body">
            <div class="grill">
              <div class="grill-bar"></div>
              <div class="grill-bar"></div>
              <div class="grill-bar"></div>
            </div>
          </div>
          <div class="headlight left">
            <div class="headlight-glow"></div>
          </div>
          <div class="headlight right">
            <div class="headlight-glow"></div>
          </div>
          <div class="bumper">
            <div class="license-plate">NYC TAXI</div>
          </div>
          <div class="taxi-roof-sign">TAXI</div>
        </div>
      </div>
      </div>
    </div>

    <!-- Impact Flash -->
    <div v-if="animationPhase === 'impact'" class="impact-flash"></div>

    <!-- Screen Shake + Crack Overlay -->
    <div 
      v-if="animationPhase === 'cracked' || animationPhase === 'reveal'" 
      class="crack-overlay"
      :class="{ 'shattering': animationPhase === 'reveal' }"
    >
      <!-- Cracked glass effect using SVG -->
      <svg class="crack-svg" viewBox="0 0 100 100" preserveAspectRatio="none">
        <!-- Central impact crater -->
        <circle cx="50" cy="50" r="5" class="impact-crater"/>
        <circle cx="50" cy="50" r="12" class="impact-ring ring1"/>
        <circle cx="50" cy="50" r="20" class="impact-ring ring2"/>
        
        <!-- Major radial cracks -->
        <path d="M50 50 L50 5" class="crack major c1"/>
        <path d="M50 50 L85 15" class="crack major c2"/>
        <path d="M50 50 L95 50" class="crack major c3"/>
        <path d="M50 50 L85 85" class="crack major c4"/>
        <path d="M50 50 L50 95" class="crack major c5"/>
        <path d="M50 50 L15 85" class="crack major c6"/>
        <path d="M50 50 L5 50" class="crack major c7"/>
        <path d="M50 50 L15 15" class="crack major c8"/>
        
        <!-- Secondary diagonal cracks -->
        <path d="M50 50 L70 10" class="crack minor c9"/>
        <path d="M50 50 L30 8" class="crack minor c10"/>
        <path d="M50 50 L92 30" class="crack minor c11"/>
        <path d="M50 50 L93 70" class="crack minor c12"/>
        <path d="M50 50 L70 92" class="crack minor c13"/>
        <path d="M50 50 L30 93" class="crack minor c14"/>
        <path d="M50 50 L8 70" class="crack minor c15"/>
        <path d="M50 50 L7 30" class="crack minor c16"/>
        
        <!-- Branching cracks from main lines -->
        <path d="M50 20 L40 12" class="crack branch c17"/>
        <path d="M50 20 L60 14" class="crack branch c18"/>
        <path d="M70 35 L80 28" class="crack branch c19"/>
        <path d="M80 50 L88 42" class="crack branch c20"/>
        <path d="M80 50 L90 58" class="crack branch c21"/>
        <path d="M70 70 L78 80" class="crack branch c22"/>
        <path d="M50 80 L42 90" class="crack branch c23"/>
        <path d="M50 80 L58 92" class="crack branch c24"/>
        <path d="M30 70 L20 78" class="crack branch c25"/>
        <path d="M20 50 L10 42" class="crack branch c26"/>
        <path d="M20 50 L12 58" class="crack branch c27"/>
        <path d="M30 30 L22 20" class="crack branch c28"/>
      </svg>
      
      <!-- Glass shards that fall away -->
      <div class="shards" v-if="animationPhase === 'reveal'">
        <div class="shard s1"></div>
        <div class="shard s2"></div>
        <div class="shard s3"></div>
        <div class="shard s4"></div>
        <div class="shard s5"></div>
        <div class="shard s6"></div>
        <div class="shard s7"></div>
        <div class="shard s8"></div>
        <div class="shard s9"></div>
        <div class="shard s10"></div>
        <div class="shard s11"></div>
        <div class="shard s12"></div>
      </div>
    </div>

    <!-- Thank You Reveal -->
    <div 
      v-if="animationPhase === 'reveal'" 
      class="thank-you-container"
    >
      <div class="thank-you-content">
        <h1 class="thank-you-title">🚕 THANK YOU!</h1>
        <h2 class="subtitle">For Riding Along</h2>
        <div class="questions-box">
          <div class="questions-text">Any Questions?</div>
          <div class="emoji-row">🗽 🚖 🌃 📊 🗽</div>
        </div>
        <div class="team-credit">Team 4 • Data Engineering Demo</div>
      </div>
    </div>

    <!-- Click to start hint -->
    <div v-if="animationPhase === 'waiting'" class="start-hint">
      🔊 Click anywhere to start (sound on!)
    </div>
  </div>
</template>

<style scoped>
.taxi-crash-container {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: 100;
  background: linear-gradient(180deg, #0a0a1a 0%, #1a1a3a 40%, #2a2a4a 100%);
  cursor: pointer;
  perspective: 1200px;
  perspective-origin: center center;
  display: flex;
  align-items: center;
  justify-content: center;
}

.start-hint {
  position: absolute;
  bottom: 80px;
  left: 50%;
  transform: translateX(-50%);
  color: rgba(255, 215, 0, 0.7);
  font-size: 1.5rem;
  animation: pulse 2s ease-in-out infinite;
  z-index: 10;
}

@keyframes pulse {
  0%, 100% { opacity: 0.4; transform: translateX(-50%) scale(1); }
  50% { opacity: 1; transform: translateX(-50%) scale(1.05); }
}

/* Taxi Centering Wrapper - this handles the centering */
.taxi-center-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  pointer-events: none;
}

/* 3D Taxi Wrapper - this handles only the scale animation */
.taxi-3d-wrapper {
  transform: scale(0.2);
  transition: transform 1.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.taxi-3d-wrapper.driving {
  transform: scale(6);
}

/* Front view of taxi */
.taxi-front {
  width: 200px;
  height: 160px;
  position: relative;
}

.hood {
  position: relative;
  width: 100%;
  height: 100%;
}

.hood-top {
  position: absolute;
  top: 0;
  left: 10%;
  width: 80%;
  height: 35%;
  background: linear-gradient(180deg, #FFD700 0%, #E6B800 50%, #CC9900 100%);
  border-radius: 15px 15px 0 0;
  box-shadow: 
    inset 0 10px 20px rgba(255, 255, 255, 0.3),
    0 -5px 30px rgba(255, 215, 0, 0.3);
}

.windshield {
  position: absolute;
  top: 8%;
  left: 18%;
  width: 64%;
  height: 22%;
  background: linear-gradient(180deg, #1a3a5c 0%, #2a5a8c 50%, #3a7abc 100%);
  border-radius: 8px 8px 0 0;
  overflow: hidden;
  border: 3px solid #333;
}

.windshield-reflection {
  position: absolute;
  top: 0;
  left: -50%;
  width: 50%;
  height: 100%;
  background: linear-gradient(100deg, transparent 0%, rgba(255,255,255,0.4) 50%, transparent 100%);
  animation: reflect 3s ease-in-out infinite;
}

@keyframes reflect {
  0%, 100% { left: -50%; }
  50% { left: 100%; }
}

.hood-body {
  position: absolute;
  top: 32%;
  left: 5%;
  width: 90%;
  height: 35%;
  background: linear-gradient(180deg, #FFD700 0%, #E6B800 30%, #B8860B 100%);
  border-radius: 5px;
  box-shadow: 
    inset 0 5px 15px rgba(255, 255, 255, 0.2),
    0 10px 30px rgba(0, 0, 0, 0.3);
}

.grill {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 50%;
  height: 40%;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  background: #111;
  border-radius: 3px;
  padding: 5px;
}

.grill-bar {
  width: 100%;
  height: 20%;
  background: linear-gradient(90deg, #666 0%, #888 50%, #666 100%);
  border-radius: 2px;
}

.headlight {
  position: absolute;
  top: 38%;
  width: 22%;
  height: 18%;
  background: radial-gradient(ellipse at center, #fff 0%, #ffffaa 40%, #ffcc00 70%, #996600 100%);
  border-radius: 50%;
  box-shadow: 
    0 0 30px #fff,
    0 0 60px #FFD700,
    0 0 100px #FFA500;
  animation: headlightPulse 0.5s ease-in-out infinite alternate;
}

.headlight.left { left: 5%; }
.headlight.right { right: 5%; }

.headlight-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 200%;
  height: 200%;
  background: radial-gradient(ellipse at center, rgba(255,255,255,0.5) 0%, transparent 70%);
}

@keyframes headlightPulse {
  from { box-shadow: 0 0 30px #fff, 0 0 60px #FFD700, 0 0 100px #FFA500; }
  to { box-shadow: 0 0 40px #fff, 0 0 80px #FFD700, 0 0 120px #FFA500; }
}

.bumper {
  position: absolute;
  top: 65%;
  left: 3%;
  width: 94%;
  height: 15%;
  background: linear-gradient(180deg, #333 0%, #222 50%, #111 100%);
  border-radius: 0 0 10px 10px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.license-plate {
  background: linear-gradient(180deg, #FFD700 0%, #FFA500 100%);
  color: #000;
  padding: 4px 15px;
  border-radius: 3px;
  font-weight: bold;
  font-size: 12px;
  letter-spacing: 1px;
  border: 2px solid #333;
}

.taxi-roof-sign {
  position: absolute;
  top: -15px;
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(180deg, #FFD700 0%, #FFA500 100%);
  color: #000;
  font-size: 14px;
  font-weight: bold;
  padding: 5px 20px;
  border-radius: 8px;
  box-shadow: 
    0 0 20px rgba(255, 215, 0, 0.8),
    0 0 40px rgba(255, 165, 0, 0.5);
  letter-spacing: 2px;
}

/* Impact Flash */
.impact-flash {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: white;
  z-index: 300;
  animation: flashBang 0.2s ease-out forwards;
}

@keyframes flashBang {
  0% { opacity: 1; }
  100% { opacity: 0; }
}

/* Crack Overlay */
.crack-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 200;
  background: rgba(0, 0, 0, 0.1);
  animation: screenShake 0.6s ease-out;
}

.crack-overlay.shattering {
  animation: shatterOut 1.2s ease-out forwards;
}

@keyframes screenShake {
  0%, 100% { transform: translate(0, 0); }
  10% { transform: translate(-15px, -15px); }
  20% { transform: translate(15px, 10px); }
  30% { transform: translate(-10px, 15px); }
  40% { transform: translate(10px, -15px); }
  50% { transform: translate(-15px, 5px); }
  60% { transform: translate(15px, -5px); }
  70% { transform: translate(-5px, 15px); }
  80% { transform: translate(5px, -10px); }
  90% { transform: translate(-10px, 10px); }
}

@keyframes shatterOut {
  0% { opacity: 1; transform: scale(1); }
  30% { opacity: 0.9; }
  100% { opacity: 0; transform: scale(1.3); }
}

.crack-svg {
  width: 100%;
  height: 100%;
}

.impact-crater {
  fill: rgba(255, 255, 255, 0.4);
  stroke: rgba(255, 255, 255, 0.9);
  stroke-width: 0.8;
  animation: craterAppear 0.1s ease-out forwards;
}

.impact-ring {
  fill: none;
  stroke: rgba(255, 255, 255, 0.5);
  stroke-width: 0.5;
}

.ring1 { animation: ringExpand 0.3s ease-out forwards; }
.ring2 { animation: ringExpand 0.5s ease-out 0.1s forwards; opacity: 0; }

@keyframes craterAppear {
  from { r: 0; opacity: 0; }
  to { r: 5; opacity: 1; }
}

@keyframes ringExpand {
  from { r: 5; opacity: 0; stroke-width: 2; }
  to { opacity: 0.3; stroke-width: 0.3; }
}

/* Crack Lines */
.crack {
  fill: none;
  stroke: rgba(255, 255, 255, 0.95);
  stroke-linecap: round;
  stroke-dasharray: 200;
  stroke-dashoffset: 200;
  filter: drop-shadow(0 0 3px rgba(255, 255, 255, 0.8));
}

.crack.major {
  stroke-width: 0.6;
  animation: drawCrack 0.3s ease-out forwards;
}

.crack.minor {
  stroke-width: 0.4;
  animation: drawCrack 0.3s ease-out forwards;
}

.crack.branch {
  stroke-width: 0.25;
  stroke: rgba(255, 255, 255, 0.7);
  animation: drawCrack 0.2s ease-out forwards;
}

/* Stagger the crack animations */
.c1 { animation-delay: 0s; }
.c2 { animation-delay: 0.02s; }
.c3 { animation-delay: 0.04s; }
.c4 { animation-delay: 0.06s; }
.c5 { animation-delay: 0.08s; }
.c6 { animation-delay: 0.1s; }
.c7 { animation-delay: 0.12s; }
.c8 { animation-delay: 0.14s; }
.c9 { animation-delay: 0.08s; }
.c10 { animation-delay: 0.1s; }
.c11 { animation-delay: 0.12s; }
.c12 { animation-delay: 0.14s; }
.c13 { animation-delay: 0.16s; }
.c14 { animation-delay: 0.18s; }
.c15 { animation-delay: 0.2s; }
.c16 { animation-delay: 0.22s; }
.c17 { animation-delay: 0.25s; }
.c18 { animation-delay: 0.27s; }
.c19 { animation-delay: 0.29s; }
.c20 { animation-delay: 0.31s; }
.c21 { animation-delay: 0.33s; }
.c22 { animation-delay: 0.35s; }
.c23 { animation-delay: 0.37s; }
.c24 { animation-delay: 0.39s; }
.c25 { animation-delay: 0.41s; }
.c26 { animation-delay: 0.43s; }
.c27 { animation-delay: 0.45s; }
.c28 { animation-delay: 0.47s; }

@keyframes drawCrack {
  to { stroke-dashoffset: 0; }
}

/* Glass Shards */
.shards {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.shard {
  position: absolute;
  background: linear-gradient(
    135deg, 
    rgba(255,255,255,0.5) 0%, 
    rgba(200,220,255,0.3) 50%,
    rgba(255,255,255,0.1) 100%
  );
  border: 1px solid rgba(255,255,255,0.3);
  animation: shardFall 2s cubic-bezier(0.4, 0, 0.2, 1) forwards;
}

/* Position shards around center */
.s1 { top: 35%; left: 42%; width: 60px; height: 80px; clip-path: polygon(20% 0%, 100% 20%, 80% 100%, 0% 70%); animation-delay: 0s; }
.s2 { top: 30%; left: 52%; width: 70px; height: 70px; clip-path: polygon(50% 0%, 100% 40%, 70% 100%, 10% 80%); animation-delay: 0.05s; }
.s3 { top: 40%; left: 55%; width: 55px; height: 75px; clip-path: polygon(30% 0%, 100% 30%, 90% 100%, 0% 60%); animation-delay: 0.1s; }
.s4 { top: 48%; left: 40%; width: 80px; height: 60px; clip-path: polygon(10% 20%, 90% 0%, 100% 80%, 20% 100%); animation-delay: 0.15s; }
.s5 { top: 45%; left: 48%; width: 65px; height: 85px; clip-path: polygon(40% 0%, 100% 25%, 60% 100%, 0% 75%); animation-delay: 0.08s; }
.s6 { top: 38%; left: 38%; width: 50px; height: 65px; clip-path: polygon(0% 30%, 70% 0%, 100% 60%, 40% 100%); animation-delay: 0.12s; }
.s7 { top: 50%; left: 54%; width: 45px; height: 55px; clip-path: polygon(25% 0%, 100% 35%, 75% 100%, 0% 65%); animation-delay: 0.18s; }
.s8 { top: 42%; left: 46%; width: 75px; height: 50px; clip-path: polygon(15% 0%, 85% 20%, 100% 90%, 0% 100%); animation-delay: 0.22s; }
.s9 { top: 33%; left: 48%; width: 40px; height: 60px; clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%); animation-delay: 0.25s; }
.s10 { top: 52%; left: 44%; width: 55px; height: 45px; clip-path: polygon(0% 20%, 80% 0%, 100% 70%, 30% 100%); animation-delay: 0.28s; }
.s11 { top: 36%; left: 56%; width: 35px; height: 50px; clip-path: polygon(40% 0%, 100% 40%, 60% 100%, 0% 60%); animation-delay: 0.3s; }
.s12 { top: 55%; left: 50%; width: 50px; height: 40px; clip-path: polygon(20% 0%, 90% 30%, 80% 100%, 10% 70%); animation-delay: 0.32s; }

@keyframes shardFall {
  0% {
    opacity: 1;
    transform: translateY(0) rotate(0deg) scale(1);
  }
  100% {
    opacity: 0;
    transform: translateY(120vh) rotate(720deg) scale(0.5);
  }
}

/* Thank You Reveal */
.thank-you-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 50;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
  animation: revealFadeIn 1.2s ease-out 0.3s both;
}

@keyframes revealFadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.thank-you-content {
  text-align: center;
  animation: contentSlideUp 0.8s ease-out 0.5s both;
}

@keyframes contentSlideUp {
  from { 
    opacity: 0; 
    transform: translateY(50px) scale(0.9); 
  }
  to { 
    opacity: 1; 
    transform: translateY(0) scale(1); 
  }
}

.thank-you-title {
  font-size: 5rem;
  font-weight: bold;
  background: linear-gradient(135deg, #FFD700 0%, #FFA500 50%, #FF8C00 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 0.5rem;
  text-shadow: none;
  animation: titlePop 0.6s ease-out 0.8s both;
}

@keyframes titlePop {
  0% { transform: scale(0.5); opacity: 0; }
  70% { transform: scale(1.1); }
  100% { transform: scale(1); opacity: 1; }
}

.subtitle {
  font-size: 2.5rem;
  color: #FFD700;
  margin-bottom: 2.5rem;
  animation: subtitleSlide 0.6s ease-out 1s both;
}

@keyframes subtitleSlide {
  from { transform: translateY(30px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

.questions-box {
  padding: 2.5rem 4rem;
  background: rgba(255, 215, 0, 0.1);
  border: 3px solid #FFD700;
  border-radius: 1.5rem;
  display: inline-block;
  margin-bottom: 2rem;
  animation: boxBounce 0.6s ease-out 1.2s both;
  box-shadow: 
    0 0 30px rgba(255, 215, 0, 0.2),
    inset 0 0 30px rgba(255, 215, 0, 0.05);
}

@keyframes boxBounce {
  0% { transform: scale(0.8); opacity: 0; }
  60% { transform: scale(1.05); }
  100% { transform: scale(1); opacity: 1; }
}

.questions-text {
  font-size: 2.5rem;
  color: white;
  margin-bottom: 1rem;
  font-weight: bold;
}

.emoji-row {
  font-size: 3rem;
  letter-spacing: 1rem;
}

.team-credit {
  color: rgba(255, 255, 255, 0.6);
  font-size: 1.4rem;
  margin-top: 2.5rem;
  animation: fadeInUp 0.6s ease-out 1.4s both;
}

@keyframes fadeInUp {
  from { transform: translateY(20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}
</style>
