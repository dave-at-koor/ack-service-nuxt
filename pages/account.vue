<template>
  <div v-if="loggedIn">
    <h1>Create a Koor Account</h1>
    <p>
      Show your interest in Koor by creating a free account. Your account comes
      with benefits.
    </p>
    <ul>
      <li>
        Use your account to register for a free trial of Koor Data Control
        Center.
      </li>
      <li>Subscribe to Pro for your production data storage nodes.</li>
      <li>Ask questions and get help with an existing storage cluster.</li>
      <li>Set up your profile and preferences.</li>
    </ul>
    <button @click="loggedIn = false">Log Out</button>
  </div>
  <RegistrationForm v-if="!loggedIn" @success="handleLoggedIn" />
</template>

<script setup>
const loggedIn = ref(false)

const handleLoggedIn = () => (loggedIn.value = true)
</script>

<style scoped></style>
