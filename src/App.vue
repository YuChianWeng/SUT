<template>
  <div class="app-shell">
    <header class="top-bar">
      <button class="icon-btn">☰</button>
      <div class="month-pill">MO</div>
      <div class="month-title">Mar 2026 ▾</div>
      <button class="icon-btn">🔔</button>
      <button class="icon-btn">📅</button>
    </header>

    <main class="content-layout">
      <section class="left-panel">
        <section class="summary">
          <div>
            <p class="label expense">Expense</p>
            <h2>$4,491</h2>
          </div>
          <div>
            <p class="label income">Income</p>
            <h2>$0</h2>
          </div>
        </section>

        <section class="ring-card">
          <div class="ring">
            <div class="ring-inner">
              <p>Balance</p>
              <h3>$-4,491</h3>
            </div>
          </div>
        </section>

        <section class="list-card">
          <div class="list-title">
            <span>Mon, Mar 9, 2026</span>
            <strong>$-797</strong>
          </div>
          <ul>
            <li><span>Breakfast</span><strong>$-671</strong></li>
            <li><span>Lunch</span><strong>$-126</strong></li>
          </ul>
        </section>
      </section>

      <section class="record-panel">
        <div class="switcher">
          <button :class="['switch-btn', mode === 'expense' && 'active']" @click="mode = 'expense'">Expense</button>
          <button :class="['switch-btn', mode === 'income' && 'active']" @click="mode = 'income'">Income</button>
        </div>

        <div class="category-grid">
          <button v-for="item in categories" :key="item" class="cat-item">{{ item }}</button>
        </div>

        <div class="input-row">
          <div class="currency">TWD</div>
          <input v-model="amountText" placeholder="$0" />
          <input v-model="note" class="note" placeholder="Tap here to write" />
        </div>

        <div class="quick-tags">
          <span v-for="tag in ['Breakfast', 'Workout']" :key="tag">{{ tag }}</span>
        </div>

        <div class="keypad">
          <button v-for="key in keys" :key="key" :class="['key', keyClass(key)]" @click="pressKey(key)">{{ key }}</button>
        </div>
      </section>
    </main>

    <button class="fab">＋</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const mode = ref('expense')
const amountText = ref('0')
const note = ref('')
const categories = [
  'Add', 'Breakfast', 'Lunch', 'Dinner',
  'Drinks', 'Snack', 'Alcohol', 'Traffic',
  'Shopping', 'Entertainment', 'Housing', 'Rent'
]

const keys = ['7', '8', '9', '÷', 'AC', '4', '5', '6', '×', '←', '1', '2', '3', '+', 'OK', '00', '0', '.', '-', '=']

const keyClass = (key) => {
  if (['÷', '×', '+', '-', '='].includes(key)) return 'op'
  if (key === 'OK') return 'ok'
  if (key === 'AC') return 'ac'
  return ''
}

const pressKey = (key) => {
  if (key === 'AC') {
    amountText.value = '0'
    return
  }
  if (key === '←') {
    amountText.value = amountText.value.slice(0, -1) || '0'
    return
  }
  if (key === 'OK') return
  if (['÷', '×', '+', '-', '='].includes(key)) return

  if (amountText.value === '0' && key !== '.') {
    amountText.value = key
    return
  }
  amountText.value += key
}
</script>
