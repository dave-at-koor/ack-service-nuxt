<template>
  <div class="space-y-10 divide-y divide-gray-900/10">
    <div class="grid grid-cols-1 gap-x-8 gap-y-8 md:grid-cols-3">
      <div class="px-4 sm:px-0">
        <h2 class="text-base font-semibold leading-7 text-gray-900">
          Product Licenses
        </h2>
        <p class="mt-1 text-sm leading-6 text-gray-600">
          See your license subscriptions, and register for more. Keep an eye on
          your renewal dates.
        </p>
      </div>

      <div
        class="bg-white shadow-sm ring-1 ring-gray-900/5 sm:rounded-xl md:col-span-2"
      >
        <div class="py-4 text-center">
          <div v-if="hasKoorPro && hasKoorTrial">
            You have everything. Thanks for being a great customer.
          </div>
          <a
            v-if="!hasKoorTrial"
            @click="startFreeTrial"
            class="rounded-md bg-indigo-600 mx-3 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white"
            >Start Free Trial</a
          >
          <a
            v-if="!hasKoorPro"
            @click="subscribeToPro"
            class="rounded-md bg-emerald-500 mx-3 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-emerald-400 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-white"
            >Subscribe to Pro</a
          >
        </div>
      </div>
    </div>

    <div class="py-8">
      <ul role="list" class="grid grid-cols-1 gap-x-6 gap-y-8 lg:grid-cols-2">
        <li
          v-for="license in licenses"
          :key="license.license_key"
          class="overflow-hidden rounded-xl border border-gray-200"
        >
          <div
            class="flex items-center gap-x-4 border-b border-gray-900/5 bg-gray-50 p-6"
          >
            <XMarkIcon
              v-if="calcStatus(license) === 'Expired'"
              class="h-12 w-12 text-red-900"
              aria-hidden="true"
            />
            <CheckBadgeIcon
              v-else
              class="h-12 w-12 text-green-900"
              aria-hidden="true"
            />
            <div class="text-sm font-medium leading-6 text-gray-900">
              {{ license.products.name }}
            </div>
          </div>
          <dl
            class="-my-3 divide-y divide-gray-100 px-6 py-4 text-sm leading-6"
          >
            <div class="flex justify-between gap-x-4 py-3">
              <dt class="text-gray-500">Description:</dt>
              <dd class="text-gray-700 text-right">
                {{ license.products.description }}
              </dd>
            </div>
            <div class="flex justify-between gap-x-4 py-3">
              <dt class="text-gray-500">License Key:</dt>
              <dd class="text-gray-700 text-right">
                {{ license.license_key }}
              </dd>
            </div>
            <div class="flex justify-between gap-x-4 py-3">
              <dt class="text-gray-500">Storage Nodes:</dt>
              <dd class="text-gray-700">
                {{ license.nodes }}
              </dd>
            </div>
            <div class="flex justify-between gap-x-4 py-3">
              <dt class="text-gray-500">Renewal Date:</dt>
              <dd class="text-gray-700">
                <time :datetime="license.expires_on">{{
                  license.expires_on
                }}</time>
                <div
                  :class="[
                    statusStyleLookup[calcStatus(license)],
                    'rounded-md py-1 px-2 text-xs font-medium ring-1 ring-inset',
                  ]"
                >
                  {{ calcStatus(license) }}
                </div>
              </dd>
            </div>
          </dl>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { CheckBadgeIcon, XMarkIcon } from '@heroicons/vue/20/solid'

const props = defineProps(['customerId'])
const nodes = ref(4)
const licenses = ref([])

const client = useSupabaseClient()
const user = useSupabaseUser()

// let { data } = await client
//   .from('licenses')
//   .select(
//     license_key,
//     nodes,
//     created_at,
//     expires_on,
//     products(name, description, product_key)
//   )
//   .eq('created_by', user.value.id)

// FIXME: use calculated to cache
const calcStatus = (license) => {
  if (license) {
    const now = new Date().getTime()
    const expiration = new Date(license.expires_on).getTime()
    console.log('compare dates', now, expiration)

    if (now <= expiration) {
      return 'Active'
    } else {
      return 'Expired'
    }
  } else {
    return 'no license'
  }
}

let { data: licenseData, error } = await client.from('licenses').select(`
    license_key,
    nodes,
    created_at,
    expires_on,
    products(name, description, product_key)
  `)
licenses.value = licenseData
console.log('licenses', JSON.stringify(licenseData))

const statusStyleLookup = {
  Active: 'text-green-700 bg-green-50 ring-green-600/20',
  Expired: 'text-red-700 bg-red-50 ring-red-600/10',
}

// for reference
// const licenses = [
//   {
//     license_key: 'abc123-09876',
//     nodes: 4,
//     created_at: '2023-09-07T16:58:08.17586+00:00',
//     expires_on: '2024-09-07',
//     products: {
//       name: 'Koor Trial',
//       description: 'For running Koor on up to 4 data nodes. Limited support.',
//       product_key: 'KOOR_TRIAL',
//     },
//   },
// ]

const hasKoorTrial = computed(() => {
  return licenses.value.find(
    (license) => license.products.product_key === 'KOOR_TRIAL'
  )
})
const hasKoorPro = computed(() => {
  return licenses.value.find(
    (license) => license.products.product_key === 'KOOR_PRO'
  )
})

const startFreeTrial = async () => {
  console.log('Starting free trial if not already going')
  await createLicense('KOOR_TRIAL', 4)
}

const subscribeToPro = async () => {
  // FIXME: ask for number of nodes - or let Koor employee update once payment is received
  console.log('Gather number of nodes, and notify Koor team to follow up')
  await createLicense('KOOR_PRO', 4)
}

// FIXME: this should be in a database function, safe from hackers
const createLicense = async (productKey, nodes) => {
  // find customer ID
  let { data: customer, error2 } = await client
    .from('customers')
    .select('id')
    .eq('created_by', user.value.id)
    .single()
  console.log('customer', customer)
  const customerId = customer.id

  // find product ID
  let { data: product, error1 } = await client
    .from('products')
    .select('id')
    .eq('product_key', productKey)
    .single()
  console.log('product', product)
  const productId = product.id

  const licenseKey = genLicenseKey(20)
  const expiresOn = new Date()
  expiresOn.setFullYear(expiresOn.getFullYear() + 1)

  const { data, error } = await client
    .from('licenses')
    .insert([
      {
        customer_id: customerId,
        product_id: productId,
        license_key: licenseKey,
        nodes: nodes,
        expires_on: expiresOn,
        created_by: user.value.id,
      },
    ])
    .select()
}

function genLicenseKey(length) {
  let result = ''
  const characters =
    'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
  const charactersLength = characters.length
  let counter = 0
  while (counter < length) {
    result += characters.charAt(Math.floor(Math.random() * charactersLength))
    counter += 1
  }
  return result
}
</script>
