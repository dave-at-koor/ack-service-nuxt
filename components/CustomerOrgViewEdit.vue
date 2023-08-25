<template>
  <div class="grid grid-cols-1 gap-x-8 gap-y-8 pt-10 md:grid-cols-3">
    <div class="px-4 sm:px-0">
      <h2 class="text-base font-semibold leading-7 text-gray-900">
        Organizational Information
      </h2>
      <p class="mt-1 text-sm leading-6 text-gray-600">
        We need a little information about the license holder. That might be an
        individual (such as you), a company, an institution, or an organziation.
      </p>
    </div>

    <form
      class="bg-white shadow-sm ring-1 ring-gray-900/5 sm:rounded-xl md:col-span-2"
    >
      <div class="px-4 py-6 sm:p-8">
        <div class="grid max-w-2xl grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6">
          <div class="sm:col-span-full">
            <label
              for="customerName"
              class="block text-sm font-medium leading-6 text-gray-900"
              >Organization Name</label
            >
            <div v-if="!isEdit">{{ customerName }}</div>
            <div v-else class="mt-2">
              <input
                id="customerName"
                name="customerName"
                v-model="customerName"
                autocomplete="organization"
                class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
              />
            </div>
          </div>

          <div class="sm:col-span-4">
            <label
              for="website"
              class="block text-sm font-medium leading-6 text-gray-900"
              >Website</label
            >
            <div v-if="!isEdit">{{ website }}</div>
            <div v-else class="mt-2">
              <div
                class="flex rounded-md shadow-sm ring-1 ring-inset ring-gray-300 focus-within:ring-2 focus-within:ring-inset focus-within:ring-indigo-600 sm:max-w-md"
              >
                <span
                  class="flex select-none items-center pl-3 text-gray-500 sm:text-sm"
                  >https://</span
                >
                <input
                  type="text"
                  name="website"
                  id="website"
                  v-model="website"
                  class="block flex-1 border-0 bg-transparent py-1.5 pl-1 text-gray-900 placeholder:text-gray-400 focus:ring-0 sm:text-sm sm:leading-6"
                  placeholder="www.example.com"
                />
              </div>
            </div>
          </div>

          <div class="col-span-full">
            <label
              for="about"
              class="block text-sm font-medium leading-6 text-gray-900"
              >About</label
            >
            <div v-if="!isEdit">{{ aboutOrg }}</div>
            <div v-else class="mt-2">
              <textarea
                id="about"
                name="about"
                v-model="aboutOrg"
                rows="3"
                class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
              />
            </div>
            <p class="mt-3 text-sm leading-6 text-gray-600">
              Write a few sentences about this organization.
            </p>
          </div>
        </div>
      </div>
      <div
        class="flex items-center justify-end gap-x-6 border-t border-gray-900/10 px-4 py-4 sm:px-8"
      >
        <button
          v-if="!isEdit"
          type="button"
          @click="editOrg"
          class="text-sm font-semibold leading-6 text-gray-900"
        >
          <PencilSquareIcon
            class="h-12 w-12 text-gray-900"
            aria-hidden="true"
          />
        </button>
        <button
          v-if="isEdit"
          @click="cancelEdit"
          type="button"
          class="text-sm font-semibold leading-6 text-gray-900"
        >
          Cancel
        </button>
        <button
          v-if="isEdit"
          @click="registerOrg"
          type="submit"
          class="rounded-md bg-emerald-500 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-emerald-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-emerald-500"
        >
          Save
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { PencilSquareIcon } from '@heroicons/vue/24/solid'

const loading = ref(true)

const isCustomerRegistered = ref(true)

const isEdit = ref(false)
const editOrg = () => {
  isEdit.value = true
}
const cancelEdit = () => {
  isEdit.value = false
}

const showMessage = ref(false)
const message = ref('')

const orgId = ref(-1)
const customerName = ref('') // name of legal license holder
const website = ref('')
const aboutOrg = ref('')

loading.value = true

const client = useSupabaseClient()
const user = useSupabaseUser()

let { data } = await client
  .from('customers')
  .select()
  .eq('created_by', user.value.id)
  .single()

if (data) {
  console.log('customer:', data)
  orgId.value = data.id
  customerName.value = data.name
  website.value = data.website
  aboutOrg.value = data.about

  isCustomerRegistered.value = true
} else {
  isCustomerRegistered.value = false
}

loading.value = false

async function registerOrg() {
  // create customer record
  console.log('logged in user', user.value.id)
  const input = {
    created_by: user.value.id,
    name: customerName.value,
    website: website.value,
    about: aboutOrg.value,
  }
  console.log('registration data:', input)
  let result1 = await client.from('customers').insert(input).select()
  console.log('created customer record', JSON.stringify(result1.data))
  const licenseHolder = result1.data[0]
  console.log('license holder', licenseHolder)
  console.log('license holder ID', licenseHolder.id)

  // connect user to customer record
  if (licenseHolder) {
    const result2 = await client
      .from('profiles')
      .update({ customer_id: licenseHolder.id })
      .eq('id', user.value.id)
      .select()
    console.log('result2', JSON.stringify(result2))
    console.log('update profile', JSON.stringify(result2.data))
  } else {
    console.error('customer record was not returned')
  }
}

async function updateOrg() {
  // try {
  //   console.log('implement updates to org info')
  //   loading.value = true
  //   const user = useSupabaseUser()
  //   const updates = {
  //     id: orgId.value,
  //     name: customerName.value,
  //     website: website.value,
  //     about: aboutOrg.value,
  //     primary_contact_id: user.value.id,
  //     updated_at: new Date(),
  //   }
  //   let { error } = await client.from('customer').upsert(updates, {
  //     returning: 'minimal', // Don't return the value after inserting
  //   })
  //   if (error) throw error
  //   message.value = 'Changes were saved. Nice.'
  //   showMessage.value = true
  // } catch (error) {
  //   console.log(error)
  //   message.value = 'Trouble saving changes. Sorry about that.'
  //   showMessage.value = true
  // } finally {
  //   loading.value = false
  // }
}
</script>
