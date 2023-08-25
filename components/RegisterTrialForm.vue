<template>
  <div>
    <form @submit="register">
      <label for="licensee"
        >License holder (name of legal entity)
        <input
          type="text"
          name="licensee"
          placeholder="licensee"
          autocomplete="organization"
      /></label>
      <label for="product">
        Product
        <select name="product" v-model="product" id="product">
          <option value=".">-- select --</option>
          <option value="koor-trial">Koor Free Trial</option>
          <option value="koor-pro">Koor Pro</option>
          <option value="koor-enterprise">Koor Enterprise</option>
        </select>
      </label>
      <div v-if="promptForNodes">
        <label for="nodes"
          >Storage Nodes
          <input
            type="number"
            name="nodes"
            id="nodes"
            v-model="nodes"
            placeholder="Nodes"
        /></label>
      </div>
    </form>
  </div>
  <div>
    <div>License Subscriptions</div>
    <div class="card">
      <div>Koor Pro License</div>
      <div>Annual subscription</div>
      <div>Key: 12345-234-23-2345-2342 (copy icon)</div>
      <div>Storage Nodes: 4</div>
      <div>Status: active</div>
      <div>Renewal on: 15-July-2023</div>
    </div>
    <div class="card">
      <div>Koor Free Trial</div>
      <div>Data Control Center v0.9-beta</div>
      <div>Key: 12345-234-23-2345-2342 (copy icon)</div>
      <div>Storage Nodes: 4</div>
      <div>Status: active</div>
      <div>Renewal on: unlimited</div>
    </div>
  </div>
</template>

<script setup>
const product = ref('.')
const nodes = ref(4) // 4 is the minimum

const promptForNodes = computed(() => {
  selectedProduct = product.value
  return selectedProduct === 'koor-pro' || selectedProduct === 'koor-enterprise'
})

const register = () => {}
</script>

<style scoped></style>
