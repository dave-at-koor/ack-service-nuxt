<template>
  <div class="space-y-10 divide-y divide-gray-900/10">
    <div class="grid grid-cols-1 gap-x-8 gap-y-8 md:grid-cols-3">
      <div class="px-4 sm:px-0">
        <h2 class="text-base font-semibold leading-7 text-gray-900">
          Personal Information
        </h2>
        <p class="mt-1 text-sm leading-6 text-gray-600">
          We want to know a little about you as an individual to support you
          better.
        </p>
        <p class="mt-3 text-sm leading-6 text-gray-600">
          We do not share this information with anyone.
        </p>
      </div>

      <form
        class="bg-white shadow-sm ring-1 ring-gray-900/5 sm:rounded-xl md:col-span-2"
      >
        <div class="px-4 py-6 sm:p-8">
          <div
            class="grid max-w-2xl grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6"
          >
            <div class="sm:col-span-full">
              <label
                for="email"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Email address</label
              >
              <div v-if="user" class="mt-2">
                {{ user.email }}
              </div>
            </div>

            <div class="sm:col-span-4">
              <label
                for="screen-name"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Screen Name (an alias or nickname)</label
              >
              <div v-if="!isEditProfile">{{ screenName }}</div>
              <div v-if="isEditProfile" class="mt-2">
                <input
                  type="text"
                  name="screen-name"
                  id="screen-name"
                  v-model="screenName"
                  autocomplete="nickname"
                  class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                />
              </div>
            </div>

            <div class="sm:col-span-3">
              <label
                for="first-name"
                class="block text-sm font-medium leading-6 text-gray-900"
                >First / Given name</label
              >
              <div v-if="!isEditProfile">{{ givenName }}</div>
              <div v-if="isEditProfile" class="mt-2">
                <input
                  type="text"
                  name="first-name"
                  id="first-name"
                  v-model="givenName"
                  autocomplete="given-name"
                  class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                />
              </div>
            </div>

            <div class="sm:col-span-3">
              <label
                for="last-name"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Last / Family name</label
              >
              <div v-if="!isEditProfile">{{ familyName }}</div>
              <div v-if="isEditProfile" class="mt-2">
                <input
                  type="text"
                  name="last-name"
                  id="last-name"
                  v-model="familyName"
                  autocomplete="family-name"
                  class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                />
              </div>
            </div>

            <div class="sm:col-span-3">
              <label
                for="organization-title"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Job Title or Position</label
              >
              <div v-if="!isEditProfile">{{ organizationTitle }}</div>
              <div v-if="isEditProfile" class="mt-2">
                <input
                  type="text"
                  name="organization-title"
                  id="organization-title"
                  v-model="organizationTitle"
                  autocomplete="organization-title"
                  class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                />
              </div>
            </div>
          </div>
        </div>
        <div
          class="flex items-center justify-end gap-x-6 border-t border-gray-900/10 px-4 py-4 sm:px-8"
        >
          <button
            v-if="!isEditProfile"
            type="button"
            @click="editProfile"
            class="text-sm font-semibold leading-6 text-gray-900"
          >
            <PencilSquareIcon
              class="h-12 w-12 text-gray-900"
              aria-hidden="true"
            />
          </button>
          <button
            v-if="isEditProfile"
            type="button"
            @click="cancelEditProfile"
            class="text-sm font-semibold leading-6 text-gray-900"
          >
            Cancel
          </button>
          <button
            v-if="isEditProfile"
            type="submit"
            @click="updateProfile"
            class="rounded-md bg-emerald-500 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-emerald-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-emerald-500"
          >
            Save
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { PencilSquareIcon } from '@heroicons/vue/24/solid'

const loading = ref(true)

const isEditProfile = ref(false)
const editProfile = () => {
  isEditProfile.value = true
}
const cancelEditProfile = () => {
  isEditProfile.value = false
}

const showMessage = ref(false)
const message = ref('')

const screenName = ref('')
const givenName = ref('')
const familyName = ref('')
const organizationTitle = ref('')

loading.value = true

const client = useSupabaseClient()
const user = useSupabaseUser()

let { data } = await client
  .from('profiles')
  .select()
  .eq('id', user.value.id)
  .single()

if (data) {
  console.log('profile:', data)
  screenName.value = data.screen_name
  givenName.value = data.given_name
  familyName.value = data.family_name
  organizationTitle.value = data.job_title
}

loading.value = false

async function updateProfile() {
  try {
    loading.value = true
    const user = useSupabaseUser()
    const updates = {
      id: user.value.id,
      screen_name: screenName.value,
      given_name: givenName.value,
      family_name: familyName.value,
      full_name: givenName.value + ' ' + familyName.value,
      job_title: organizationTitle.value,
      updated_at: new Date(),
    }
    let { error } = await client.from('profiles').upsert(updates, {
      returning: 'minimal', // Don't return the value after inserting
    })
    if (error) throw error
    message.value = 'Changes were saved. Nice.'
    showMessage.value = true
  } catch (error) {
    console.log(error)
    message.value = 'Trouble saving changes. Sorry about that.'
    showMessage.value = true
  } finally {
    loading.value = false
  }
}
</script>
