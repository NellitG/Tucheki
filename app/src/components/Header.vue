<script setup>
import { ref, onMounted } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const chartData = ref({
  labels: ['Daily', 'Weekly', 'Monthly'],
  datasets: [
    {
      label: 'Sales in KES',
      data: [0, 0, 0],
      backgroundColor: ['#34d399', '#3b82f6', '#fbbf24'],
      borderRadius: 10,
      barThickness: 50,
    },
  ],
})

const chartOptions = ref({
  responsive: true,
  plugins: {
    legend: {
      display: false,
    },
  },
  scales: {
    y: {
      ticks: {
        color: '#6b7280', // Gray-600
        font: { size: 14 },
      },
      beginAtZero: true,
    },
    x: {
      ticks: {
        color: '#6b7280',
        font: { size: 14 },
      },
    },
  },
})

async function fetchSalesSummary() {
  try {
    const response = await fetch('http://127.0.0.1:8000/api/sales-summary/', {
      headers: {
        Authorization: `Bearer ${localStorage.getItem('access_token')}`,
      },
    })
    const data = await response.json()
    chartData.value.datasets[0].data = [
      data.daily_sales,
      data.weekly_sales,
      data.monthly_sales,
    ]
  } catch (err) {
    console.error('Error fetching sales summary:', err)
  }
}

onMounted(() => {
  fetchSalesSummary()
})
</script>

<template>
  <div class="bg-white p-6 rounded-2xl shadow-lg w-full max-w-4xl mx-auto mt-10">
    <h2 class="text-3xl font-semibold text-gray-800 mb-6 flex items-center gap-2">
      📈 Sales Summary
    </h2>

    <div class="h-72">
      <Bar :data="chartData" :options="chartOptions" />
    </div>

    <div class="mt-6 grid grid-cols-1 sm:grid-cols-3 gap-4 text-center text-gray-700">
      <div class="bg-green-100 rounded-xl py-4 shadow-sm">
        <p class="text-sm text-green-800">Today’s Sales</p>
        <p class="text-2xl font-bold text-green-600">KES {{ chartData.datasets[0].data[0] }}</p>
      </div>
      <div class="bg-blue-100 rounded-xl py-4 shadow-sm">
        <p class="text-sm text-blue-800">This Week’s Sales</p>
        <p class="text-2xl font-bold text-blue-600">KES {{ chartData.datasets[0].data[1] }}</p>
      </div>
      <div class="bg-yellow-100 rounded-xl py-4 shadow-sm">
        <p class="text-sm text-yellow-800">This Month’s Sales</p>
        <p class="text-2xl font-bold text-yellow-600">KES {{ chartData.datasets[0].data[2] }}</p>
      </div>
    </div>
  </div>
</template>
