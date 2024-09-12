<template>
  <div class="dashboard p-6 bg-gray-100 min-h-screen">
    <h2 class="text-2xl font-semibold mb-4">Your Envelopes</h2>
    <div v-if="envelopes.length === 0" class="text-gray-600">You have no envelopes.</div>
    <ul class="space-y-2">
      <li
        v-for="envelope in envelopes"
        :key="envelope._id"
        class="bg-white p-4 rounded shadow hover:shadow-md transition"
      >
        <router-link :to="`/envelope/${envelope._id}`" class="text-blue-600 hover:underline">
          {{ envelope.name }} - ${{ envelope.balance }}
        </router-link>
      </li>
    </ul>
    <button
      @click="createEnvelope"
      class="mt-6 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition"
    >
      Add New Envelope
    </button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      envelopes: []
    }
  },
  async mounted() {
    try {
      const token = localStorage.getItem('token')
      const response = await axios.get(`${import.meta.env.VITE_API_URL}/envelopes`, {
        headers: { Authorization: `Bearer ${token}` }
      })
      this.envelopes = response.data
    } catch (error) {
      console.error(error)
    }
  },
  methods: {
    async createEnvelope() {
      const name = prompt('Enter envelope name')
      const budget = parseFloat(prompt('Enter budget'))
      const token = localStorage.getItem('token')

      try {
        await axios.post(
          `${import.meta.env.VITE_API_URL}/envelopes`,
          { name, budget },
          { headers: { Authorization: `Bearer ${token}` } }
        )
        this.$router.go()
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
