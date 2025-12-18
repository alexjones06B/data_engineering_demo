---
theme: default
title: NYC Taxi Data Analysis
info: |
  ## New York City Taxi Data Analysis
  Data Engineering Demo by Team 4
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-left
mdc: true
---

# 🚕 NYC TAXI DATA

## Data Analysis Journey

**Team 4** • Data Engineering Demo

<div class="pt-12">
  <span class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space to Start Your Journey 🗽
  </span>
</div>

<TaxiAnimation />

<style>
h1 {
  background: linear-gradient(135deg, #FFD700 0%, #FFA500 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-size: 4rem !important;
  font-weight: bold;
  text-shadow: none;
}
h2 {
  color: #FFD700;
}
</style>

---

# 🚖 NEW YORK CITY TAXIS

## Data Analysis by Team 4

<div class="grid grid-cols-3 gap-8 pt-8">
  <div class="text-center p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
    <div class="text-4xl mb-4">📊</div>
    <div class="text-xl font-bold text-yellow-400">Analytics</div>
  </div>
  <div class="text-center p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
    <div class="text-4xl mb-4">🗃️</div>
    <div class="text-xl font-bold text-yellow-400">Big Data</div>
  </div>
  <div class="text-center p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
    <div class="text-4xl mb-4">🔍</div>
    <div class="text-xl font-bold text-yellow-400">Insights</div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 {
  color: #FFD700;
}
</style>

---

# 📁 THE DATA

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="space-y-4">
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-yellow-400 text-2xl font-bold">4 CSV Files</div>
      <div class="text-gray-400">Multiple data sources</div>
    </div>
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-yellow-400 text-2xl font-bold">Multiple Dates</div>
      <div class="text-gray-400">Time-series data coverage</div>
    </div>
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-yellow-400 text-2xl font-bold">12 Million Rows</div>
      <div class="text-gray-400">Per CSV file!</div>
    </div>
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-yellow-400 text-2xl font-bold">Unnormalized</div>
      <div class="text-gray-400">Raw, unclean data</div>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">📊</div>
      <div>Insert Data Volume Chart Here</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# 🏅 MEDALLION STRUCTURE

We utilized medallion structure to ensure our data was usable

<div class="flex items-center justify-center gap-8 mt-12">
  <div class="text-center">
    <div class="w-32 h-32 rounded-full flex items-center justify-center mx-auto mb-4 text-5xl" style="background: linear-gradient(145deg, #CD7F32, #8B4513);">
      🥉
    </div>
    <div class="text-orange-400 text-2xl font-bold">BRONZE</div>
    <div class="text-gray-400">Raw Data</div>
  </div>
  <div class="text-yellow-400 text-4xl">→</div>
  <div class="text-center">
    <div class="w-32 h-32 rounded-full flex items-center justify-center mx-auto mb-4 text-5xl" style="background: linear-gradient(145deg, #C0C0C0, #808080);">
      🥈
    </div>
    <div class="text-gray-300 text-2xl font-bold">SILVER</div>
    <div class="text-gray-400">Cleaned Data</div>
  </div>
  <div class="text-yellow-400 text-4xl">→</div>
  <div class="text-center">
    <div class="w-32 h-32 rounded-full flex items-center justify-center mx-auto mb-4 text-5xl" style="background: linear-gradient(145deg, #FFD700, #FFA500);">
      🥇
    </div>
    <div class="text-yellow-400 text-2xl font-bold">GOLD</div>
    <div class="text-gray-400">Analytics Ready</div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# 🥉 BRONZE LAYER

<div class="grid grid-cols-2 gap-8 mt-6">
  <div class="space-y-4">
    <div class="p-5 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-2">📥 Data Ingestion</div>
      <div class="text-gray-300">We took all the data and put it into one large table.</div>
    </div>
    <div class="p-5 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-2">⚠️ Size Limitation</div>
      <div class="text-gray-300">Due to the sheer size of having all 4 files, we were only able to add the <span class="text-yellow-400 font-bold">January 2015</span> dataset.</div>
    </div>
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r flex items-center gap-4">
      <div class="text-3xl">📊</div>
      <div>
        <div class="text-yellow-400 text-xl font-bold">~12 Million Records</div>
        <div class="text-gray-400">Single month of data</div>
      </div>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center h-full">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">📈</div>
      <div>Insert Bronze Layer Stats Chart</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# 🥈 SILVER LAYER

<div class="grid grid-cols-2 gap-8 mt-6">
  <div class="space-y-4">
    <div class="p-4 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-2">🧹 Data Cleaning</div>
      <div class="text-gray-300">We cleaned our data and ensured its quality.</div>
    </div>
    <div class="p-4 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-2">⭐ Star Schema</div>
      <div class="text-gray-300">We used a star schema to organize our data.</div>
    </div>
    <div class="p-4 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-2">📋 Table Separation</div>
      <div class="text-gray-300">We split data into separate tables:</div>
      <ul class="text-gray-400 mt-2 ml-4 list-none">
        <li>• Vendors</li>
        <li>• Rate Codes</li>
        <li>• Payments</li>
      </ul>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">🔗</div>
      <div>Insert Star Schema Diagram</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# ⚠️ ANOMALIES IN DATA

**Extreme outliers meant that we had to remove them**

<div class="grid grid-cols-2 gap-8 mt-6">
  <div class="space-y-4">
    <div class="p-5 bg-red-500 bg-opacity-10 rounded-lg border border-red-500">
      <div class="text-red-400 text-xl font-bold mb-2">🚨 Extreme Outlier</div>
      <div class="text-gray-300">Biggest trip distance value:</div>
      <div class="text-yellow-400 text-3xl font-bold mt-2">15,420,004.5 miles</div>
      <div class="text-gray-400 mt-2">Supposedly completed in only <span class="text-red-400 font-bold">half an hour!</span></div>
    </div>
    <div class="p-5 bg-orange-500 bg-opacity-10 rounded-lg border border-orange-500">
      <div class="text-orange-400 text-xl font-bold mb-2">📍 Location Errors</div>
      <div class="text-gray-300">We found latitudes and longitudes equal to <span class="text-orange-400 font-bold">zero</span></div>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">📊</div>
      <div>Insert Outlier Distribution Chart</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# 📏 TRIP DISTANCES

<div class="grid grid-cols-2 gap-8 mt-6">
  <div class="space-y-3">
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-gray-300">Initial Standard Deviation</div>
      <div class="text-yellow-400 text-2xl font-bold">9,000 miles</div>
      <div class="text-gray-500 text-sm">Extremely skewed!</div>
    </div>
    <div class="p-4 border-l-4 border-yellow-500 bg-gray-800 rounded-r">
      <div class="text-gray-300">Average Trip Distance</div>
      <div class="text-yellow-400 text-2xl font-bold">2.6 miles</div>
    </div>
    <div class="p-4 border-l-4 border-red-500 bg-gray-800 rounded-r">
      <div class="text-gray-300">Maximum Recorded</div>
      <div class="text-red-400 text-2xl font-bold">15 million miles</div>
    </div>
    <div class="p-4 bg-green-500 bg-opacity-10 rounded-lg border border-green-500">
      <div class="text-green-400 text-xl font-bold mb-2">✅ Solution</div>
      <div class="text-gray-300">Recalculated S.D excluding trips over 29,000 miles (3σ)</div>
      <div class="text-green-400 text-xl font-bold mt-2">New S.D: 4 miles</div>
      <div class="text-gray-400 text-sm mt-1">Deleted anything over 4σ from the mean</div>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">📊</div>
      <div>Insert Trip Distance Histogram</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---

# 🗺️ LATITUDES & LONGITUDES

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="space-y-6">
    <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500">
      <div class="text-yellow-400 text-xl font-bold mb-3">📍 Missing Location Data</div>
      <div class="text-gray-300">Many latitudes and longitudes were empty</div>
      <div class="text-yellow-400 text-4xl font-bold mt-4">~2%</div>
      <div class="text-gray-400">of the whole dataset</div>
    </div>
    <div class="p-6 bg-green-500 bg-opacity-10 rounded-lg border border-green-500">
      <div class="text-green-400 text-xl font-bold mb-3">💡 Our Approach</div>
      <div class="text-gray-300">We <span class="text-green-400 font-bold">did not delete</span> these records.</div>
      <div class="text-gray-400 mt-2">Instead, we simply excluded them from any searches or queries which involved location data.</div>
    </div>
  </div>
  <div class="p-6 bg-yellow-500 bg-opacity-10 rounded-lg border border-yellow-500 border-dashed flex items-center justify-center">
    <div class="text-center text-gray-500">
      <div class="text-4xl mb-2">🗽</div>
      <div>Insert NYC Heatmap / Location Chart</div>
    </div>
  </div>
</div>

<TaxiAnimation />

<style>
h1 { color: #FFD700; }
</style>

---
layout: none
---

<TaxiCrash />
