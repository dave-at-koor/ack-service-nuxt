<template>
  <div
    class="flex min-h-full flex-1 flex-col justify-center py-12 sm:px-6 lg:px-8"
  >
    <SupaSignInUp v-if="!user" />
    <div v-else>You are logged in. Super!</div>
  </div>
</template>

<script setup lang="ts">
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
