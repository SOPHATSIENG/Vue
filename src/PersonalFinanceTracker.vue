<template>
  <div class="min-h-screen bg-stone-100 text-slate-900 p-4 md:p-8">
    <div class="max-w-3xl mx-auto space-y-6">

      <!-- Header -->
      <div class="text-center space-y-1">
        <h1 class="text-3xl font-bold tracking-tight text-slate-950"> Finance Tracker</h1>
        <p class="text-slate-500 text-sm">Track income, expenses & stay on budget</p>
      </div>

      <!-- Notification Log -->
      <div v-if="notificationLog.length" class="space-y-2">
        <div
          v-for="(msg, i) in notificationLog"
          :key="i"
          class="flex items-center justify-between bg-amber-50 border border-amber-200 text-amber-800 text-sm px-4 py-2 rounded-xl shadow-sm"
        >
          <span>{{ msg }}</span>
          <button @click="notificationLog.splice(i, 1)" class="text-amber-600 hover:text-slate-950 ml-4">✕</button>
        </div>
      </div>

      <!-- Summary Cards -->
      <div class="grid grid-cols-3 gap-4">
        <div class="bg-white border border-slate-200 rounded-2xl p-4 text-center shadow-sm">
          <p class="text-emerald-700 text-xs font-semibold uppercase tracking-widest mb-1">Income</p>
          <p class="text-2xl font-bold text-slate-950">${{ totalIncome.toFixed(2) }}</p>
        </div>
        <div class="bg-white border border-slate-200 rounded-2xl p-4 text-center shadow-sm">
          <p class="text-rose-700 text-xs font-semibold uppercase tracking-widest mb-1">Expenses</p>
          <p class="text-2xl font-bold text-slate-950">${{ totalExpenses.toFixed(2) }}</p>
        </div>
        <div
          :class="balance >= 0
            ? 'bg-white border-slate-200'
            : 'bg-rose-50 border-rose-200'"
          class="border rounded-2xl p-4 text-center shadow-sm"
        >
          <p :class="balance >= 0 ? 'text-slate-500' : 'text-rose-700'" class="text-xs font-semibold uppercase tracking-widest mb-1">Balance</p>
          <p :class="balance >= 0 ? 'text-slate-950' : 'text-rose-700'" class="text-2xl font-bold">
            {{ balance >= 0 ? '+' : '' }}${{ balance.toFixed(2) }}
          </p>
        </div>
      </div>

      <!-- Budget Progress -->
      <div class="bg-white border border-slate-200 rounded-2xl p-5 space-y-3 shadow-sm">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <span class="text-slate-700 text-sm font-medium">Budget Limit</span>
            <div class="flex items-center bg-slate-100 border border-slate-200 rounded-lg overflow-hidden">
              <span class="text-slate-500 text-sm px-2">$</span>
              <input
                v-model.number="budgetLimit"
                type="number"
                class="bg-transparent text-slate-950 text-sm w-24 py-1 pr-2 outline-none"
                min="0"
              />
            </div>
          </div>
          <span
            :class="{
              'bg-emerald-50 text-emerald-700 border-emerald-200': !isOverBudget && expensePercentage <= 75,
              'bg-amber-50 text-amber-700 border-amber-200': !isOverBudget && expensePercentage > 75,
              'bg-rose-50 text-rose-700 border-rose-200': isOverBudget
            }"
            class="text-xs font-semibold px-3 py-1 rounded-full border"
          >
            {{ budgetStatus }}
          </span>
        </div>

        <!-- Progress Bar -->
        <div class="w-full bg-slate-200 rounded-full h-3 overflow-hidden">
          <div
            :style="{ width: expensePercentage + '%' }"
            :class="{
              'bg-emerald-600': !isOverBudget && expensePercentage <= 75,
              'bg-amber-500': !isOverBudget && expensePercentage > 75,
              'bg-rose-600': isOverBudget
            }"
            class="h-3 rounded-full transition-all duration-500"
          ></div>
        </div>
        <p class="text-slate-500 text-xs text-right">{{ expensePercentage }}% of budget used</p>
      </div>

      <!-- Add Transaction -->
      <div class="bg-white border border-slate-200 rounded-2xl p-5 space-y-4 shadow-sm">
        <h2 class="text-slate-700 font-semibold text-sm uppercase tracking-wider">Add Transaction</h2>
        <div class="grid grid-cols-1 md:grid-cols-4 gap-3">
          <input
            v-model="form.desc"
            placeholder="Description"
            class="md:col-span-2 bg-white border border-slate-300 text-slate-950 placeholder-slate-400 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-blue-700 focus:ring-2 focus:ring-blue-700/10 transition"
          />
          <input
            v-model.number="form.amount"
            type="number"
            placeholder="Amount"
            min="0"
            class="bg-white border border-slate-300 text-slate-950 placeholder-slate-400 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-blue-700 focus:ring-2 focus:ring-blue-700/10 transition"
          />
          <select
            v-model="form.type"
            class="bg-white border border-slate-300 text-slate-950 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-blue-700 focus:ring-2 focus:ring-blue-700/10 transition"
          >
            <option value="income">Income</option>
            <option value="expense">Expense</option>
          </select>
        </div>
        <button
          @click="handleAdd"
          class="w-full bg-blue-800 hover:bg-blue-700 active:scale-95 text-white font-semibold py-2.5 rounded-xl text-sm transition-all duration-150 shadow-sm"
        >
          + Add Transaction
        </button>
      </div>

      <!-- Filter Tabs + List -->
      <div class="bg-white border border-slate-200 rounded-2xl p-5 space-y-4 shadow-sm">
        <div class="flex items-center justify-between">
          <div class="flex gap-2">
            <button
              v-for="f in ['all', 'income', 'expense']"
              :key="f"
              @click="filterType = f"
              :class="filterType === f
                ? 'bg-blue-800 text-white border-blue-800'
                : 'bg-white text-slate-600 border-slate-300 hover:text-slate-950 hover:border-slate-400'"
              class="border text-xs font-medium px-4 py-1.5 rounded-full capitalize transition"
            >
              {{ f }}
            </button>
          </div>
          <button
            v-if="transactions.length"
            @click="clearAll"
            class="text-xs text-rose-700 hover:text-rose-900 transition"
          >
            Clear All
          </button>
        </div>

        <!-- Transaction List -->
        <ul v-if="filteredTransactions.length" class="space-y-2">
          <li
            v-for="tx in filteredTransactions"
            :key="tx.id"
            :class="tx.type === 'income'
              ? 'border-l-4 border-emerald-600 bg-emerald-50'
              : 'border-l-4 border-rose-600 bg-rose-50'"
            class="flex items-center justify-between rounded-r-xl px-4 py-3"
          >
            <div>
              <p class="text-slate-950 text-sm font-medium">{{ tx.desc }}</p>
              <p class="text-slate-500 text-xs">{{ tx.date }}</p>
            </div>
            <div class="flex items-center gap-3">
              <span
                :class="tx.type === 'income' ? 'text-emerald-700' : 'text-rose-700'"
                class="font-bold text-sm"
              >
                {{ tx.type === 'income' ? '+' : '-' }}${{ tx.amount.toFixed(2) }}
              </span>
              <button
                @click="deleteTransaction(tx.id)"
                class="text-slate-400 hover:text-rose-700 text-lg leading-none transition"
              >✕</button>
            </div>
          </li>
        </ul>

        <div v-else class="text-center text-slate-500 text-sm py-6">
          No transactions yet. Add one above!
        </div>
      </div>

      <!-- Category Summary -->
      <div v-if="Object.keys(categorySummary).length" class="bg-white border border-slate-200 rounded-2xl p-5 space-y-3 shadow-sm">
        <h2 class="text-slate-700 font-semibold text-sm uppercase tracking-wider">Category Summary</h2>
        <div
          v-for="(total, cat) in categorySummary"
          :key="cat"
          class="flex items-center justify-between py-2 border-b border-slate-200 last:border-0"
        >
          <span class="text-slate-600 text-sm">{{ cat }}</span>
          <span class="text-slate-950 font-semibold text-sm">${{ total.toFixed(2) }}</span>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

// ── Types ──────────────────────────────────────────────────
interface Transaction {
  id: number
  desc: string
  amount: number
  type: 'income' | 'expense'
  date: string
}

// ── 1. Reactive State ──────────────────────────────────────
const transactions   = ref<Transaction[]>([])
const filterType     = ref<'all' | 'income' | 'expense'>('all')
const budgetLimit    = ref<number>(100)
const notificationLog = ref<string[]>([])

const form = ref({ desc: '', amount: null as number | null, type: 'expense' as 'income' | 'expense' })

// ── 2. Methods ─────────────────────────────────────────────
function addTransaction(desc: string, amount: number, type: 'income' | 'expense') {
  transactions.value.push({
    id: Date.now(),
    desc,
    amount: Number(amount),
    type,
    date: new Date().toLocaleDateString()
  })
}

function handleAdd() {
  if (!form.value.desc || !form.value.amount) return
  addTransaction(form.value.desc, form.value.amount, form.value.type)
  form.value = { desc: '', amount: null, type: 'expense' }
}

function deleteTransaction(id: number) {
  transactions.value = transactions.value.filter(t => t.id !== id)
}

function clearAll() {
  transactions.value = []
  notificationLog.value = []
}

// ── 3. Computed Properties (all 7) ─────────────────────────
const filteredTransactions = computed(() => {
  if (filterType.value === 'all') return transactions.value
  return transactions.value.filter(t => t.type === filterType.value)
})

const totalIncome = computed(() =>
  transactions.value.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0)
)

const totalExpenses = computed(() =>
  transactions.value.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0)
)

const balance = computed(() => totalIncome.value - totalExpenses.value)

const isOverBudget = computed(() => totalExpenses.value > budgetLimit.value)

const expensePercentage = computed(() => {
  if (budgetLimit.value === 0) return 100
  return Math.min(Math.round((totalExpenses.value / budgetLimit.value) * 100), 100)
})

const categorySummary = computed(() =>
  transactions.value.reduce((acc: Record<string, number>, t) => {
    const key = t.desc || (t.type === 'income' ? 'Income' : 'Other')
    acc[key] = (acc[key] || 0) + t.amount
    return acc
  }, {})
)

const budgetStatus = computed(() => {
  if (isOverBudget.value)           return '🔴 Over budget!'
  if (expensePercentage.value > 75) return '🟡 Nearing limit'
  return '🟢 On track'
})

// ── 4. Watchers ────────────────────────────────────────────
watch(transactions, (val) => {
  localStorage.setItem('vue-finance-transactions', JSON.stringify(val))
}, { deep: true })

watch(balance, (val) => {
  if (val < 0) notificationLog.value.push(`⚠️ Balance is negative: $${val.toFixed(2)}`)
})

watch(isOverBudget, (over) => {
  if (over) notificationLog.value.push(`🚨 Budget exceeded! Spent $${totalExpenses.value.toFixed(2)}`)
})

onMounted(() => {
  const saved = localStorage.getItem('vue-finance-transactions')
  if (saved) transactions.value = JSON.parse(saved)
})
</script>
