<template>
  <div class="project-page">
    <section class="project-header pt-5">
      <div class="container mx-auto relative">
        <Navbar></Navbar>
      </div>
    </section>
    <section class="container project-container mx-auto -mt-56">
      <div class="flex mt-3">
        <div class="w-3/4 mr-6">
          <div class="bg-white p-3 mb-3 border border-gray-400 rounded-20">
            <figure class="item-image">
              <img :src="defaultImage" alt="" class="rounded-20 w-full" />
            </figure>
          </div>
          <div class="flex -mx-2">
            <div
              v-for="image in data.images"
              :key="image.image_url"
              class="relative w-1/4 bg-white m-2 p-2 border border-gray-400 rounded-20"
            >
              <figure class="item-thumbnail">
                <img
                  :src="`${$axios.defaults.baseURL}/${image.image_url}`"
                  @click="
                    changeDefaultImage(
                      `${$axios.defaults.baseURL}/${image.image_url}`
                    )
                  "
                  alt=""
                  class="rounded-20 w-full"
                />
              </figure>
            </div>
          </div>
        </div>
        <div class="w-1/4">
          <div
            class="bg-white w-full p-5 border border-gray-400 rounded-20 sticky"
            style="top: 15px"
          >
            <h3>Project Leader:</h3>

            <div class="flex mt-3">
              <div class="w-1/4">
                <img
                  :src="`${$axios.defaults.baseURL}/${data.user.image_url}`"
                  alt=""
                  class="w-full inline-block rounded-full"
                />
              </div>
              <div class="w-3/4 ml-5 mt-1">
                <div class="font-semibold text-xl text-gray-800">
                  {{ data.user.name }}
                </div>
                <div class="font-light text-md text-gray-400">
                  {{ data.backer_count }} backer
                </div>
              </div>
            </div>

            <h4 class="mt-5 font-semibold">What will you get:</h4>
            <ul class="list-check mt-3">
              <li v-for="perk in data.perks" :key="perk">{{ perk }}</li>
            </ul>
            <template v-if="this.$auth.loggedIn">
              <input
                type="number"
                class="border border-gray-500 block w-full px-6 py-3 mt-4 rounded-full text-gray-800 transition duration-300 ease-in-out focus:outline-none focus:shadow-outline"
                placeholder="Amount in Rp"
                v-model.number="transaction.amount"
                @keyup.enter="fund"
              />
              <button
                @click="fund"
                class="text-center mt-3 button-cta block w-full bg-orange-button hover:bg-green-button text-white font-medium px-6 py-3 text-md rounded-full"
              >
                Fund Now
              </button>
            </template>
            <template v-else>
              <NuxtLink
                :to="{ name: 'login' }"
                class="text-center mt-3 button-cta block w-full bg-orange-button hover:bg-green-button text-white font-medium px-6 py-3 text-md rounded-full"
              >
                Sign In to Fund
              </NuxtLink>
            </template>
          </div>
        </div>
      </div>
    </section>
    <section class="container mx-auto pt-8">
      <div class="flex justify-between items-center">
        <div class="w-full md:w-3/4 mr-6">
          <h2 class="text-4xl text-gray-900 mb-2 font-medium">
            {{ data.name }}
          </h2>
          <p class="font-light text-xl mb-5">
            {{ data.short_description }}
          </p>

          <div class="relative progress-bar">
            <div
              class="overflow-hidden mb-4 text-xs flex rounded-full bg-gray-200 h-6"
            >
              <div
                :style="
                  'width: ' +
                  (data.current_amount / data.goal_amount) * 100 +
                  '%'
                "
                class="shadow-none flex flex-col text-center whitespace-nowrap text-white justify-center bg-purple-progress progress-striped"
              ></div>
            </div>
          </div>
          <div class="flex progress-info mb-6">
            <div class="text-2xl">
              {{ (data.current_amount / data.goal_amount) * 100 }}%
            </div>
            <div class="ml-auto font-semibold text-2xl">
              Rp{{ new Intl.NumberFormat().format(data.goal_amount) }}
            </div>
          </div>

          <p class="font-light text-xl mb-5">
            {{ data.description }}
          </p>
        </div>
        <div class="w-1/4 hidden md:block"></div>
      </div>
    </section>
    <div class="cta-clip -mt-20"></div>
    <CallToAction></CallToAction>
    <Footer></Footer>
  </div>
</template>

<script>
export default {
  async asyncData({ params, $axios }) {
    let { data } = await $axios.$get(`/api/v1/campaigns/${params.id}`)
    return { data }
  },
  data() {
    return {
      defaultImage: '',
      transaction: {
        amount: 0,
        campaign_id: Number.parseInt(this.$route.params.id),
      },
    }
  },
  methods: {
    changeDefaultImage(url) {
      this.defaultImage = url
    },
    async fund() {
      try {
        let { data } = await this.$axios.$post(
          '/api/v1/transactions',
          this.transaction
        )
        window.location = data.payment_url
      } catch (error) {
        console.log(error)
      }
    },
  },
  mounted() {
    this.defaultImage = `${this.$axios.defaults.baseURL}/${this.data.image_url}`
  },
}
</script>

<style></style>
