<template>
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-100 p-4">
    <h2 class="text-2xl font-semibold mb-6">Register</h2>
    <form @submit.prevent="registerUser" class="bg-white p-6 rounded-md shadow-md w-full max-w-sm">
      <div class="mb-4">
        <label for="username" class="block text-gray-700 mb-2">Username:</label>
        <input
          v-model="username"
          type="text"
          id="username"
          required
          class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
      </div>
      <div class="mb-4">
        <label for="email" class="block text-gray-700 mb-2">Email:</label>
        <input
          v-model="email"
          type="email"
          id="email"
          required
          class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
      </div>
      <div class="mb-4">
        <label for="password" class="block text-gray-700 mb-2">Password:</label>
        <input
          v-model="password"
          type="password"
          id="password"
          required
          class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
      </div>
      <button
        type="submit"
        class="w-full py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition duration-200"
      >
        Register
      </button>
    </form>
    <p v-if="errorMessage" class="mt-4 text-red-500">{{ errorMessage }}</p>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      username: '',
      email: '',
      password: '',
      errorMessage: ''
    }
  },
  methods: {
    async registerUser() {
      try {
        await axios.post(`${import.meta.env.VITE_API_URL}/users/register`, {
          username: this.username,
          email: this.email,
          password: this.password
        })
        this.$router.push('/login')
      } catch (error) {
        this.errorMessage = 'Registration failed'
      }
    }
  }
}
</script>
