<template>
  <div
    class="flex min-h-full flex-1 flex-col justify-center py-12 sm:px-6 lg:px-8 bg-gray-50"
  >
    <div class="sm:mx-auto sm:w-full sm:max-w-md">
      <h2
        class="mt-6 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900"
      >
        {{ isSignUp ? 'Create your account' : 'Sign in' }}
      </h2>
    </div>
    <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-[480px]">
      <form class="space-y-6" @submit.prevent="authenticate">
        <div>
          <label
            for="email"
            class="block text-sm font-medium leading-6 text-gray-900"
            >Email address</label
          >
          <div class="mt-2">
            <input
              id="email"
              type="email"
              placeholder="Email"
              v-model="email"
              autocomplete="email"
              required="true"
              class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
        </div>

        <div>
          <label
            for="password"
            class="block text-sm font-medium leading-6 text-gray-900"
            >Password</label
          >
          <div class="mt-2">
            <input
              id="password"
              type="password"
              name="password"
              placeholder="Password"
              v-model="password"
              :autocomplete="isSignUp ? 'new-password' : 'current-password'"
              required="true"
              class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            />
          </div>
        </div>

        <div>
          <button
            type="submit"
            class="flex w-full justify-center rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
          >
            <span v-if="isSignUp"> Create Your Account </span>
            <span v-else> Login </span>
          </button>
        </div>
      </form>
    </div>
  </div>
  <div class="bg-white px-6 py-4 shadow sm:rounded-lg sm:px-12">
    <div class="text-sm leading-6">
      <a
        @click="toggleIsSignUp"
        class="font-semibold text-indigo-600 hover:text-indigo-500"
      >
        {{ isSignUp ? 'Have an account? ' : 'Need an account? ' }} Click
        here.</a
      >
    </div>
  </div>
</template>
<script setup>
const isSignUp = ref(false)
const email = ref('')
const password = ref('')
const client = useSupabaseClient()

const toggleIsSignUp = () => (isSignUp.value = !isSignUp.value)

const authenticate = async () => {
  const payload = {
    email: email.value,
    password: password.value,
    options: {
      redirectTo: 'http://localhost:3000/account',
    },
  }
  console.log('auth w/email', { payload, isSignUp: isSignUp.value })
  const { data, error } = isSignUp
    ? await client.auth.signUp(payload)
    : await client.auth.signInWithPassword(payload)
  console.log('data', data)
  console.log('error', error)
}

const user = useSupabaseUser()
onMounted(() => {
  watchEffect(() => {
    if (user.value) {
      navigateTo('/account')
    }
  })
})
</script>
