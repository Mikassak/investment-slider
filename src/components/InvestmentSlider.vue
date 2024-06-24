<template>
  <div class="investment-slider">
    <div class="slider-section">
      <div class="slider-header">
        <h3>Koľko plánujete investovať?</h3>
        <input
          type="number"
          v-model="investmentAmount"
          @input="updateAmount"
          min="3000"
          max="200000"
        />
      </div>
      <div class="slider-container">
        <div class="slider-wrapper">
          <input
            type="range"
            v-model="investmentAmount"
            @input="updateAmount"
            min="3000"
            max="200000"
            step="1000"
          />
          <div class="slider-bubble" :style="{ left: bubblePosition + '%' }">
            {{ investmentAmount.toLocaleString() }} €
          </div>
        </div>
        <div class="slider-scale">
          <span
            v-for="mark in investmentMarks"
            :key="mark"
            :style="{ left: `${((mark - 3000) / (200000 - 3000)) * 100}%` }"
          >
            {{ mark.toLocaleString() }}
          </span>
        </div>
      </div>
    </div>

    <div class="slider-section">
      <div class="slider-header">
        <h3>Na akú dobu?</h3>
        <select v-model="investmentPeriod">
          <option v-for="period in periods" :key="period.value" :value="period.value">
            {{ period.label }}
          </option>
        </select>
      </div>
      <div class="slider-container">
        <div class="slider-wrapper">
          <input
            type="range"
            v-model="investmentPeriod"
            @input="updatePeriod"
            min="3"
            max="5"
            step="1"
          />
          <div class="slider-bubble" :style="{ left: periodBubblePosition + '%' }">
            {{ periodInterest }} %
          </div>
        </div>
        <div class="slider-scale">
          <span
            v-for="period in periods"
            :key="period.value"
            :style="{ left: `${((period.value - 3) / 2) * 100}%` }"
          >
            {{ period.value }} {{ period.value < 5 ? 'roky' : 'rokov' }}
          </span>
        </div>
      </div>
    </div>

    <div class="profit-section">
      <h2>Vaša investícia</h2>
      <p>
        Pri investícii <strong>{{ investmentAmount.toLocaleString() }} €</strong> na
        <strong>{{ investmentPeriod }} {{ investmentPeriod < 5 ? 'roky' : 'rokov' }}</strong> so
        zhodnotením <strong>{{ periodInterest }} % ročne</strong> získate celkový výnos
        <span class="total-profit">{{ totalProfit.toLocaleString() }} €</span>.
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Period {
  value: number
  label: string
  interest: number
}

const investmentAmount = ref(3000)
const investmentPeriod = ref(5)

const periods: Period[] = [
  { value: 3, label: '3 roky | 5.55 % ročne', interest: 5.55 },
  { value: 4, label: '4 roky | 6.00 % ročne', interest: 6.0 },
  { value: 5, label: '5 rokov | 6.55 % ročne', interest: 6.55 }
]

const investmentMarks = [
  3000, 20000, 40000, 60000, 80000, 100000, 120000, 140000, 160000, 180000, 200000
]

const periodInterest = computed(() => {
  const period = periods.find((p) => p.value === investmentPeriod.value)
  return period ? period.interest : 0
})

const bubblePosition = computed(() => {
  return ((investmentAmount.value - 3000) / (200000 - 3000)) * 100
})

const periodBubblePosition = computed(() => {
  return ((investmentPeriod.value - 3) / 2) * 100
})

const totalProfit = computed(() => {
  const interestRate = periodInterest.value / 100
  return investmentAmount.value * interestRate * investmentPeriod.value
})

const updateAmount = (event: Event) => {
  investmentAmount.value = parseInt((event.target as HTMLInputElement).value)
}

const updatePeriod = (event: Event) => {
  investmentPeriod.value = parseInt((event.target as HTMLInputElement).value)
}
</script>

<style scoped>
.investment-slider {
  padding: 20px;
}

.slider-section {
  margin-bottom: 40px;
}

.slider-header {
  display: flex;
  align-items: center;
}

.slider-section label {
  display: block;
  margin-bottom: 10px;
}

.slider-container {
  position: relative;
}

.slider-wrapper {
  position: relative;
  width: 100%;
  margin-top: 35px;
}

.slider-section input[type='number'],
.slider-section select {
  width: 100%;
  max-width: 250px;
  padding: 10px;
  margin-bottom: 10px;
  margin-left: 20px;
  box-sizing: border-box;
}

.slider-section input[type='range'] {
  width: 100%;
  -webkit-appearance: none;
  appearance: none;
  background: transparent;
  position: relative;
  z-index: 2;
}

.slider-section input[type='range']::-webkit-slider-runnable-track {
  width: 100%;
  height: 8px;
  cursor: pointer;
  box-shadow: none;
  background: transparent;
  border: none;
}

.slider-section input[type='range']::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #800000;
  cursor: pointer;
  margin-top: -8px; /* Offset for the thumb */
  position: relative;
  z-index: 3;
}

.slider-section input[type='range']::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 0;
  width: 100%;
  height: 8px;
  background-color: #ccc;
  z-index: 1;
  transform: translateY(-50%);
}

.slider-section input[type='range']::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 0;
  width: calc((100% - 24px) * (var(--value, 0) / 100));
  height: 8px;
  background-color: #800000;
  z-index: 2;
  transform: translateY(-50%);
}

.slider-section input[type='range']::-moz-range-track {
  width: 100%;
  height: 8px;
  cursor: pointer;
  box-shadow: none;
  background: transparent;
  border: none;
}

.slider-section input[type='range']::-moz-range-thumb {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #800000;
  cursor: pointer;
  border: none;
}

.slider-section input[type='range']::-ms-track {
  width: 100%;
  height: 8px;
  cursor: pointer;
  background: transparent;
  border-color: transparent;
  color: transparent;
}

.slider-section input[type='range']::-ms-fill-lower {
  background: transparent;
}

.slider-section input[type='range']::-ms-fill-upper {
  background: transparent;
}

.slider-section input[type='range']::-ms-thumb {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #800000;
  cursor: pointer;
  border: none;
  margin-top: -8px; /* Offset for the thumb */
}

.slider-bubble {
  position: absolute;
  top: -40px;
  transform: translateX(-50%);
  background-color: #f8d7da;
  color: #333;
  padding: 5px 10px;
  border: 2px solid #e2aeb1;
  border-radius: 5px;
  white-space: nowrap;
  font-size: 14px;
  z-index: 4;
}

.slider-scale {
  position: relative;
  height: 20px;
  margin-top: 10px;
}

.slider-scale span {
  position: absolute;
  transform: translateX(-50%);
  font-size: 0.8em;
  color: #800000;
}

.profit-section {
  margin-top: 20px;
}

.total-profit {
  font-weight: bold;
  color: #800000;
  font-size: x-large;
}

.profit-section h4 {
  margin-bottom: 10px;
}

.profit-section p {
  font-size: 1.2em;
  color: #800000;
}
</style>
