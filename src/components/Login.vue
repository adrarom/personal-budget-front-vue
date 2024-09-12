<template>
  <div class="flex h-screen w-screen bg-gray-200">
    <div class="flex flex-1 items-center justify-center bg-gray-300">
      <div class="flex items-end gap-2">
        <div class="w-24 h-24 bg-orange-500 rounded-full"></div>
        <div class="w-24 h-36 bg-purple-700 rounded-md"></div>
        <div class="w-24 h-30 bg-black rounded-md"></div>
        <div class="w-24 h-24 bg-yellow-400 rounded-md"></div>
      </div>
    </div>
    <div class="flex flex-1 items-center justify-center bg-white p-10">
      <div class="w-full max-w-xs text-center">
        <div class="mb-6">
          <h2 class="text-2xl font-semibold mb-2">Welcome back!</h2>
          <p class="text-gray-600">Please enter your details</p>
        </div>
        <form @submit.prevent="loginUser">
          <div class="mb-4">
            <input
              v-model="email"
              type="email"
              placeholder="Email"
              required
              class="w-full p-2 border border-gray-300 rounded-md"
            />
          </div>
          <div class="relative mb-4">
            <input
              v-model="password"
              type="password"
              placeholder="Password"
              required
              class="w-full p-2 border border-gray-300 rounded-md"
            />
            <span class="absolute right-2 top-1/2 transform -translate-y-1/2 cursor-pointer"
              >👁️</span
            >
          </div>
          <div class="flex justify-between items-center mb-4">
            <label class="flex items-center">
              <input type="checkbox" v-model="rememberMe" class="mr-2" />
              <span>Remember for 30 days</span>
            </label>
            <a href="#" class="text-sm text-blue-500 hover:underline">Forgot password?</a>
          </div>
          <button
            type="submit"
            class="w-full py-2 mb-4 bg-black text-white rounded-md hover:bg-gray-800"
          >
            Log In
          </button>
        </form>
        <div class="mt-4">
          <p>
            Don't have an account? <a href="#" class="text-blue-500 hover:underline">Sign Up</a>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      password: '',
      rememberMe: false
    }
  },
  methods: {
    async loginUser() {
      try {
        console.log('Logging in...')
        console.log(import.meta.env.VITE_API_URL)
        console.log(this.email)
        const response = await fetch(`${import.meta.env.VITE_API_URL}/users/login`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            email: this.email,
            password: this.password
          })
        })
        const data = await response.json()
        console.log(data)
        if (response.status !== 400) {
          const token = data.token
          localStorage.setItem('token', token)
          this.$router.push('/dashboard')
        } else {
          throw new Error('Invalid email or password')
        }
      } catch (error) {
        this.errorMessage = 'Invalid email or password'
      }
    }
  }
}
</script>
