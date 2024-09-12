<template>
  <form @submit.prevent="addTransaction" class="bg-white p-6 rounded-md shadow-md w-full max-w-sm">
    <div class="mb-4">
      <label for="description" class="block text-gray-700 mb-2">Description:</label>
      <input
        v-model="description"
        type="text"
        id="description"
        required
        class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
      />
    </div>
    <div class="mb-4">
      <label for="amount" class="block text-gray-700 mb-2">Amount:</label>
      <input
        v-model="amount"
        type="number"
        id="amount"
        required
        class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
      />
    </div>
    <button
      type="submit"
      class="w-full py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition duration-200"
    >
      Add Transaction
    </button>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  props: ['envelopeId'],
  data() {
    return {
      description: '',
      amount: 0
    }
  },
  methods: {
    async addTransaction() {
      const token = localStorage.getItem('token')
      try {
        await axios.post(
          `${import.meta.env.VITE_API_URL}/envelopes/${this.envelopeId}/transactions`,
          {
            description: this.description,
            amount: this.amount
          },
          { headers: { Authorization: `Bearer ${token}` } }
        )
        this.$emit('transactionAdded')
        this.description = ''
        this.amount = 0
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
