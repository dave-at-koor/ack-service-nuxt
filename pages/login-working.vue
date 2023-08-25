<template>
  <div>
    <form
      @submit.prevent="() => (isSignUp ? signUp() : login())"
      class="flex flex-col gap-2"
    >
      <label
        >Email:
        <input
          type="email"
          name="email"
          placeholder="Email"
          v-model="email"
          autocomplete="email"
          class="p-2 bg-gray-600 rounded"
      /></label>
      <input
        type="password"
        name="password"
        placeholder="Password"
        v-model="password"
        :autocomplete="isSignUp ? 'new-password' : 'current-password'"
        class="p-2 bg-gray-600 rounded"
      />
      <button type="submit" class="p-2 text-white bg-green-500 rounded">
        <span v-if="isSignUp"> Sign Up </span>
        <span v-else> Login </span>
      </button>
    </form>
    <button
      @click="isSignUp = !isSignUp"
      class="w-full mt-8 text-sm text-center underline text-slate-300"
    >
      <span v-if="isSignUp"> Have an account? Log in instead </span>
      <span v-else> Create a new account </span>
    </button>
  </div>
</template>

<script setup lang="ts">
const isSignUp = ref(false)
const email = ref('')
const password = ref('')
const client = useSupabaseClient()

const signUp = async () => {
  const { data, error } = await client.auth.signUp({
    email: email.value,
    password: password.value,
  })
  console.log('data', data)
  console.log('user', data.user)
  console.log('session', data.session)
  console.log('error', error)
}

const login = async () => {
  const { data, error } = await client.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  })
  console.log('data', data)
  console.log('user', data.user)
  console.log('session', data.session)
  console.log('error', error)
}

async function signInWithGitHub() {
  const { data, error } = await client.auth.signInWithOAuth({
    provider: 'github',
    options: {
      redirectTo: 'http://localhost:3000/account',
    },
  })
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

<style scoped></style>
