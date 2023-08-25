<template>
  <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-[480px]">
    <div class="text-xl text-center">Sign in using one of these services</div>
    <div class="relative flex justify-center text-sm font-medium leading-6">
      <span class="bg-white px-6 text-gray-900"
        >We will create a Koor account if you do not have one.</span
      >
    </div>
    <div class="mt-6 grid grid-cols-2 gap-4">
      <a
        href="#"
        @click="() => signInWithOAuth('github')"
        class="flex w-full items-center justify-center gap-3 rounded-md bg-[#24292F] px-3 py-1.5 text-white focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#24292F]"
      >
        <svg
          class="h-5 w-5"
          aria-hidden="true"
          fill="currentColor"
          viewBox="0 0 20 20"
        >
          <path
            fill-rule="evenodd"
            d="M10 0C4.477 0 0 4.484 0 10.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0110 4.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.203 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.942.359.31.678.921.678 1.856 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0020 10.017C20 4.484 15.522 0 10 0z"
            clip-rule="evenodd"
          />
        </svg>
        <span class="text-sm font-semibold leading-6">GitHub</span>
      </a>
      <a
        href="#"
        @click="() => signInWithOAuth('github')"
        class="flex w-full items-center justify-center gap-3 rounded-md bg-[#24292F] px-3 py-1.5 text-white focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-[#24292F]"
      >
        <svg
          class="h-5 w-5"
          aria-hidden="true"
          fill="currentColor"
          viewBox="0 0 20 20"
        >
          <path
            fill-rule="evenodd"
            d="M10 0C4.477 0 0 4.484 0 10.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0110 4.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.203 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.942.359.31.678.921.678 1.856 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0020 10.017C20 4.484 15.522 0 10 0z"
            clip-rule="evenodd"
          />
        </svg>
        <span class="text-sm font-semibold leading-6">GitHub</span>
      </a>
    </div>
  </div>
  <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-[480px]">
    <div class="text-xl text-center">Or use your Koor account</div>
    <div class="text-sm text-center leading-8">
      <a
        @click="toggleIsSignUp"
        class="font-semibold text-indigo-600 hover:text-indigo-500"
      >
        <span v-if="isSignUp"> Already have an account? Click here. </span>
        <span v-else> Need an account? Click here. </span>
      </a>
    </div>
    <form
      @submit.prevent="() => (isSignUp ? signUp() : login())"
      class="flex flex-col gap-2"
    >
      <div>
        <span v-if="isSignUp">Create Your Account</span>
        <span v-else="isSignUp">Sign In</span>
      </div>
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
            class="block w-full rounded-md border-0 py-1.5 text-slate-600 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-slate-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
          />
        </div>
      </div>
      <div>
        <label
          for="password"
          class="block mt-2 text-sm font-medium leading-6 text-slate-600"
          >Password</label
        >
        <div class="mt-2">
          <input
            id="password"
            :type="showPassword ? 'text' : 'password'"
            name="password"
            placeholder="Password"
            v-model="password"
            :autocomplete="isSignUp ? 'new-password' : 'current-password'"
            required="true"
            class="block w-full rounded-md border-0 py-1.5 text-slate-800 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-slate-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
          />
        </div>
        <div class="mt-1">
          <input
            type="checkbox"
            @click="showPassword = !showPassword"
            class="sm:text-sm"
          /><span class="text-sm font-medium text-slate-600">
            Show Password
          </span>
        </div>
      </div>
      <button type="submit" class="mt-5 p-2 text-white bg-green-500 rounded">
        <span v-if="isSignUp"> Create Account </span>
        <span v-else> Sign In </span>
      </button>
    </form>
  </div>
</template>

<script setup>
const isSignUp = ref(false)
const showPassword = ref(false)
const email = ref('')
const password = ref('')
const client = useSupabaseClient()

const toggleIsSignUp = () => (isSignUp.value = !isSignUp.value)

const signUp = async () => {
  const { data, error } = await client.auth.signUp({
    email: email.value,
    password: password.value,
  })
  console.log('data', data)
  console.log('error', error)
}

const login = async () => {
  const { data, error } = await client.auth.signInWithPassword({
    email: email.value,
    password: password.value,
    options: {
      redirectTo: '/account',
    },
  })
  console.log('data', data)
  console.log('error', error)
}

async function signInWithOAuth(provider) {
  console.log('login / sign up with OAuth', provider)
  const { data, error } = await client.auth.signInWithOAuth({
    provider: provider,
    options: {
      redirectTo: '/account',
    },
  })
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
