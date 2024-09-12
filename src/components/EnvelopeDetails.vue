<template>
  <div class="p-6 bg-white rounded-lg shadow-md">
    <h2 class="text-2xl font-semibold mb-4">{{ envelope.name }}</h2>
    <p class="text-lg mb-6">Balance: {{ envelope.balance }}</p>
    <ul class="space-y-2">
      <li
        v-for="transaction in envelope.transactions"
        :key="transaction._id"
        class="p-4 bg-gray-100 rounded-md shadow-sm"
      >
        <span class="font-medium">{{ transaction.description }}</span> -
        <span class="text-blue-500">{{ transaction.amount }}</span>
      </li>
    </ul>
    <TransactionForm @transactionAdded="fetchEnvelope" :envelopeId="envelope._id" />
  </div>
</template>

<script>
import axios from 'axios'
import TransactionForm from './TransactionForm.vue'

export default {
  components: { TransactionForm },
  data() {
    return {
      envelope: null
    }
  },
  async mounted() {
    this.fetchEnvelope()
  },
  methods: {
    async fetchEnvelope() {
      const token = localStorage.getItem('token')
      const response = await axios.get(
        `${import.meta.env.VITE_API_URL}/envelopes/${this.$route.params.id}`,
        {
          headers: { Authorization: `Bearer ${token}` }
        }
      )
      this.envelope = response.data
    }
  }
}
</script>
