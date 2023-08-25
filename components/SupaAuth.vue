<template>
  <div class="">
    <div>
      <h2>Sign in or register to access your account information.</h2>
      <p>If you already have an account, you can sign in here.</p>
      <p>You can also register to create an account. It's free and easy.</p>
    </div>
    <div class="flex items-start">
      <div class="col">
        <AuthWithOAuth />
      </div>
      <div class="col">
        <AuthWithEmailPwd />
      </div>
    </div>
  </div>
</template>

<script setup>
const user = useSupabaseUser()
onMounted(() => {
  watchEffect(() => {
    if (user.value) {
      navigateTo('/account')
    }
  })
})
</script>
