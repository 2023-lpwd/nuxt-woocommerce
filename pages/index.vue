<template>
  <div class="p-index">
    <div class="container">
      <div class="p-index__list || row ">
        <div class="p-index__item || column -size-3" v-for="(product, index) in products" :key="index">
          <Product v-bind="product" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  head: {
    title: 'Store | Page d\'accueil',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      {
        hid: 'description',
        name: 'description',
        content: 'La page d\'accueil du store'
      }
    ],
    link: [{ rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }]
  },

  async asyncData ({ $axios }) {
    const products = await $axios.$get('/wc/v3/products')

    return {
      products
    }
  },

  data () {
    return {
      products: []
    }
  }
}
</script>

<style lang="scss">

</style>
