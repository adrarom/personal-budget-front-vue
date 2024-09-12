<template>
  <div class="min-h-screen bg-gray-100 p-8">
    <div v-if="isLoading" class="flex justify-center items-center h-screen">
      <div class="animate-spin rounded-full h-32 w-32 border-t-2 border-b-2 border-gray-900"></div>
    </div>
    <div v-else-if="isAuthenticated">
      <div class="max-w-4xl mx-auto bg-white rounded-lg shadow-xl overflow-hidden">
        <div class="p-8">
          <div class="flex items-center justify-between mb-8">
            <div>
              <h1 class="text-3xl font-bold text-gray-900">Savings Dashboard</h1>
              <p class="text-gray-600">Manage your savings using the envelope method</p>
            </div>
            <button class="rounded-full focus:outline-none focus:ring-2 focus:ring-gray-600">
              <svg
                class="h-6 w-6 text-gray-400"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6h16M4 12h16m-7 6h7"
                ></path>
              </svg>
            </button>
          </div>

          <div class="grid gap-6 mb-8 md:grid-cols-2 lg:grid-cols-3">
            <!-- Total Savings Card -->
            <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between mb-2">
                <h2 class="text-lg font-semibold text-gray-700">Total Savings</h2>
                <DollarSign class="h-6 w-6 text-gray-400" />
              </div>
              <p class="text-3xl font-bold text-gray-900">${{ totalSavings.toFixed(2) }}</p>
            </div>

            <!-- Render each envelope -->
            <div
              v-for="envelope in envelopes"
              :key="envelope._id"
              class="bg-white rounded-lg shadow p-6"
            >
              <div class="flex items-center justify-between mb-2">
                <h2 class="text-lg font-semibold text-gray-700">{{ envelope.name }}</h2>
                <component :is="getEnvelopeIcon(envelope.name)" class="h-6 w-6 text-gray-400" />
              </div>
              <p class="text-3xl font-bold text-gray-900">${{ envelope.budget.toFixed(2) }}</p>
              <p class="text-xl text-gray-600">Balance: ${{ envelope.balance.toFixed(2) }}</p>
            </div>
            <div class="bg-white rounded-lg shadow p-6">
              <div class="flex items-center justify-between mb-2">
                <h2 class="text-lg font-semibold text-gray-700">Add New Envelope</h2>
                <Plus class="h-6 w-6 text-gray-400" />
              </div>
              <form @submit.prevent="handleAddNewEnvelope" class="space-y-4">
                <div>
                  <label for="newEnvelopeName" class="block text-sm font-medium text-gray-700"
                    >Name</label
                  >
                  <input
                    id="newEnvelopeName"
                    v-model="newEnvelopeName"
                    type="text"
                    class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-gray-500 focus:border-gray-500 sm:text-sm"
                    placeholder="Enter envelope name"
                  />
                </div>
                <div>
                  <label for="newEnvelopeBudget" class="block text-sm font-medium text-gray-700"
                    >Budget</label
                  >
                  <input
                    id="newEnvelopeBudget"
                    v-model="newEnvelopeBudget"
                    type="number"
                    min="0"
                    step="0.01"
                    class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-gray-500 focus:border-gray-500 sm:text-sm"
                    placeholder="Enter budget amount"
                  />
                </div>
                <button
                  type="submit"
                  class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-gray-600 hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
                  :disabled="isSubmitting"
                >
                  <Plus v-if="!isSubmitting" class="mr-2 h-5 w-5" />
                  <span v-if="isSubmitting" class="mr-2">
                    <svg
                      class="animate-spin h-5 w-5 text-white"
                      xmlns="http://www.w3.org/2000/svg"
                      fill="none"
                      viewBox="0 0 24 24"
                    >
                      <circle
                        class="opacity-25"
                        cx="12"
                        cy="12"
                        r="10"
                        stroke="currentColor"
                        stroke-width="4"
                      ></circle>
                      <path
                        class="opacity-75"
                        fill="currentColor"
                        d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                      ></path>
                    </svg>
                  </span>
                  {{ isSubmittingNewEnvelope ? 'Adding...' : 'Add Envelope' }}
                </button>
              </form>
            </div>
          </div>

          <div class="grid gap-8 md:grid-cols-2">
            <div class="bg-white rounded-lg shadow p-6">
              <h2 class="text-xl font-semibold text-gray-700 mb-4">Savings Distribution</h2>
              <div class="h-64">
                <Pie v-if="chartData" :data="chartData" :options="chartOptions" />
              </div>
            </div>
            <div class="bg-white rounded-lg shadow p-6">
              <h2 class="text-xl font-semibold text-gray-700 mb-4">Add Savings</h2>
              <form @submit.prevent="handleAddSavings" class="space-y-4">
                <div>
                  <label for="envelope" class="block text-sm font-medium text-gray-700"
                    >Envelope</label
                  >
                  <select
                    id="envelope"
                    v-model="selectedEnvelope"
                    class="mt-1 block w-full pl-3 pr-10 py-2 text-base border-gray-300 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
                  >
                    <option value="" disabled>Select envelope</option>
                    <option v-for="env in envelopes" :key="env._id" :value="env.name">
                      {{ env.name }}
                    </option>
                  </select>
                </div>
                <div>
                  <label for="amount" class="block text-sm font-medium text-gray-700">Amount</label>
                  <input
                    id="amount"
                    v-model="newAmount"
                    type="number"
                    min="0"
                    step="0.01"
                    class="mt-1 block w-full border-gray-300 rounded-md shadow-sm focus:ring-gray-500 focus:border-gray-500 sm:text-sm"
                    placeholder="Enter amount"
                  />
                </div>
                <button
                  type="submit"
                  class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-gray-600 hover:bg-gray-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
                  :disabled="isSubmitting"
                >
                  <Plus v-if="!isSubmitting" class="mr-2 h-5 w-5" />
                  <span v-if="isSubmitting" class="mr-2">
                    <svg
                      class="animate-spin h-5 w-5 text-white"
                      xmlns="http://www.w3.org/2000/svg"
                      fill="none"
                      viewBox="0 0 24 24"
                    >
                      <circle
                        class="opacity-25"
                        cx="12"
                        cy="12"
                        r="10"
                        stroke="currentColor"
                        stroke-width="4"
                      ></circle>
                      <path
                        class="opacity-75"
                        fill="currentColor"
                        d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                      ></path>
                    </svg>
                  </span>
                  {{ isSubmitting ? 'Adding...' : 'Add Savings' }}
                </button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="text-center">
      <p class="text-2xl font-bold text-gray-700">
        Please <a href="/login" class="text-blue-600 hover:underline">log in</a> to view the
        dashboard.
      </p>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, onMounted, watch } from 'vue'
import { Pie } from 'vue-chartjs'
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js'
import { DollarSign, ShoppingBag, Car, Home, Plane, Coffee, Plus } from 'lucide-vue-next'
import axios from 'axios'

ChartJS.register(ArcElement, Tooltip, Legend)

interface Envelope {
  _id: string
  name: string
  budget: number
  balance: number
}

const isLoading = ref(true)
const isAuthenticated = ref(false)
const isSubmitting = ref(false)
const isSubmittingNewEnvelope = ref(false)

const COLORS = ['#0088FE', '#00C49F', '#FFBB28', '#FF8042', '#8884D8', '#82CA9D']

const envelopes = ref<Envelope[]>([])

const newAmount = ref('')
const selectedEnvelope = ref('')

const newEnvelopeName = ref('')
const newEnvelopeBudget = ref('')

const totalSavings = computed(() =>
  envelopes.value.reduce((sum, envelope) => sum + envelope.budget, 0)
)

const chartData = computed(() => {
  if (envelopes.value.length === 0) return null
  return {
    labels: envelopes.value.map((env) => env.name),
    datasets: [
      {
        data: envelopes.value.map((env) => env.budget),
        backgroundColor: COLORS.slice(0, envelopes.value.length)
      }
    ]
  }
})

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false
}

const getEnvelopeIcon = (name: string) => {
  const iconMap: { [key: string]: any } = {
    Groceries: ShoppingBag,
    Transport: Car,
    Rent: Home,
    Travel: Plane,
    Entertainment: Coffee
  }
  return iconMap[name] || DollarSign
}

const handleAddSavings = async () => {
  const amount = parseFloat(newAmount.value)
  if (isNaN(amount) || amount <= 0 || !selectedEnvelope.value) return

  isSubmitting.value = true
  console.log(selectedEnvelope.value)
  try {
    const token = localStorage.getItem('token')
    const envelope = envelopes.value.find((env) => env.name === selectedEnvelope.value)
    if (!envelope) throw new Error('Envelope not found')

    await axios.post(
      `${import.meta.env.VITE_API_URL}/transactions`,
      {
        envelopeId: envelope._id,
        amount: -amount,
        description: 'Savings added'
      },
      { headers: { Authorization: `Bearer ${token}` } }
    )
    await fetchEnvelopes()
    newAmount.value = ''
    selectedEnvelope.value = ''
  } catch (error) {
    console.error('Error adding savings:', error)
  } finally {
    isSubmitting.value = false
  }
}

const handleAddNewEnvelope = async () => {
  const name = newEnvelopeName.value
  const budget = parseFloat(newEnvelopeBudget.value)
  if (!name || isNaN(budget) || budget <= 0) return

  isSubmittingNewEnvelope.value = true
  try {
    const token = localStorage.getItem('token')
    await axios.post(
      `${import.meta.env.VITE_API_URL}/envelopes`,
      { name, budget },
      { headers: { Authorization: `Bearer ${token}` } }
    )
    await fetchEnvelopes()
    newEnvelopeName.value = ''
    newEnvelopeBudget.value = ''
  } catch (error) {
    console.error('Error adding new envelope:', error)
  } finally {
    isSubmittingNewEnvelope.value = false
  }
}

const fetchEnvelopes = async () => {
  try {
    const token = localStorage.getItem('token')
    const response = await axios.get(`${import.meta.env.VITE_API_URL}/envelopes`, {
      headers: { Authorization: `Bearer ${token}` }
    })
    envelopes.value = response.data.map((env: Envelope) => ({
      _id: env._id,
      name: env.name,
      budget: env.budget,
      balance: env.balance
    }))
    console.log(envelopes.value)
  } catch (error) {
    console.error('Error fetching envelopes:', error)
    // TODO: Add user-friendly error message
  }
}

onMounted(async () => {
  await new Promise((resolve) => setTimeout(resolve, 1500))
  const token = localStorage.getItem('token')
  isAuthenticated.value = !!token
  isLoading.value = false

  if (isAuthenticated.value) {
    await fetchEnvelopes()
  }
})
watch(envelopes, () => {
  if (chartData.value) {
    ChartJS.getChart('pie-chart')?.update()
  }
})
</script>
