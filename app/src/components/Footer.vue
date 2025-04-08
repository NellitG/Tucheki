<template>
  <div class="mt-10 max-w-5xl mx-auto bg-green-50 p-10 shadow-lg rounded-xl print:p-0 print:rounded-none print:shadow-none print:bg-white">
    <!-- Header -->
    <div class="text-center mb-10">
      <h1 class="text-3xl font-bold text-gray-800">🧾 Grocery Sales Report</h1>
      <p class="text-gray-500">Detailed Report with Custom Date Range</p>
      <p class="text-sm mt-2 text-gray-400">{{ today }}</p>
    </div>

    <!-- Filter -->
    <div class="flex flex-col md:flex-row items-center justify-between gap-4 mb-6 print:hidden">
      <div class="flex gap-2">
        <label class="text-sm font-medium">Start Date:</label>
        <input type="date" v-model="startDate" class="border px-2 py-1 rounded" />
      </div>
      <div class="flex gap-2">
        <label class="text-sm font-medium">End Date:</label>
        <input type="date" v-model="endDate" class="border px-2 py-1 rounded" />
      </div>
      <button
        @click="fetchSalesData"
        class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 transition"
      >
        Generate Report
      </button>
    </div>

    <!-- Summary Section -->
    <div class="grid md:grid-cols-3 gap-4 mb-10">
      <div class="border p-4 rounded-md">
        <h2 class="text-xl font-semibold text-green-700">🟢 Daily</h2>
        <p class="text-2xl mt-2 font-bold text-gray-700">KES {{ report.daily_sales }}</p>
      </div>
      <div class="border p-4 rounded-md">
        <h2 class="text-xl font-semibold text-blue-700">🔵 Weekly</h2>
        <p class="text-2xl mt-2 font-bold text-gray-700">KES {{ report.weekly_sales }}</p>
      </div>
      <div class="border p-4 rounded-md">
        <h2 class="text-xl font-semibold text-yellow-700">🟡 Monthly</h2>
        <p class="text-2xl mt-2 font-bold text-gray-700">KES {{ report.monthly_sales }}</p>
      </div>
    </div>

    <!-- Monthly Breakdown Table -->
    <div class="mt-8">
      <h3 class="text-xl font-semibold text-gray-800 mb-4">📅 Monthly Sales Breakdown</h3>
      <table class="w-full table-auto border-collapse border border-gray-300">
        <thead class="bg-gray-100">
          <tr>
            <th class="border border-gray-300 p-2 text-left">Date</th>
            <th class="border border-gray-300 p-2 text-left">Items Sold (KG)</th>
            <th class="border border-gray-300 p-2 text-left">Total Sales (KES)</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in report.breakdown" :key="item.date">
            <td class="border p-2">{{ item.date }}</td>
            <td class="border p-2">{{ item.kgs_sold }}</td>
            <td class="border p-2">KES {{ item.total }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Print Button -->
    <div class="mt-10 text-right print:hidden">
      <button
        @click="window.print()"
        class="bg-indigo-600 text-white px-6 py-2 rounded-lg hover:bg-indigo-700 transition"
      >
        Print Report
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const today = new Date().toLocaleDateString()
const startDate = ref('')
const endDate = ref('')
const report = ref({
  daily_sales: 0,
  weekly_sales: 0,
  monthly_sales: 0,
  breakdown: []
})

const fetchSalesData = async () => {
  try {
    let url = 'http://127.0.0.1:8000/api/sales-summary/'
    if (startDate.value && endDate.value) {
      url += `?start=${startDate.value}&end=${endDate.value}`
    }

    const res = await fetch(url, {
      headers: {
        Authorization: `Bearer ${localStorage.getItem('access_token')}`,
      },
    })
    const data = await res.json()
    report.value = data
  } catch (error) {
    console.error('Failed to fetch report:', error)
  }
}
</script>

<style>
@media print {
  button,
  input,
  select {
    display: none !important;
  }

  .print\:hidden {
    display: none !important;
  }
}
</style>
