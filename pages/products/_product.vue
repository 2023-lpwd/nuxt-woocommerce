<template>
  <div class="product-view">
    <div class="container">
      <div class="row">
        <div class="product-view__gallery | column -size-6">
          <ProductGallery :images="displayedProduct.images" />
        </div>
        <div class="product-view__content | column -size-6">
          <h1 class="product-view__name">{{ displayedProduct.name }}</h1>
          <span class="product-view__sku">{{ displayedProduct.sku }}</span>
          <p class="product-view__price">{{ displayedProduct.price }}€</p>
          <div v-if="colorAttribute" class="product-view__attribute">
            <div v-for="(option, index) in colorAttribute.options" class="product-view__option" @click="changeColor(option)">{{ option }}</div>
          </div>
          <div class="product-view__description">
            <div v-if="displayedProduct.dimensions" class="product-view__dimensions">
              <p class="product-view__subtitle">Dimensions du produit :</p>
              <ul>
                <li class="product-view__dimensions-item">Longueur : {{ displayedProduct.dimensions.length }}cm</li>
                <li class="product-view__dimensions-item">Largeur : {{ displayedProduct.dimensions.width }}cm</li>
                <li class="product-view__dimensions-item">Hauteur : {{ displayedProduct.dimensions.height }}cm</li>
              </ul>
            </div>
            <p class="product-view__subtitle">Description du produit :</p>
            <div class="product-view__description-content" v-html="displayedProduct.short_description" />
          </div>
          <div class="product-view__actions">
            <div class="product-view__add-to-cart" @click="addToCart">
              <MyButton>Ajouter au panier</MyButton>
            </div>
            <div class="product-view__quantity">
              <div class="product-view__quantity-button" @click="updateQuantity('decrease')">-</div>
              <div class="product-view__quantity-value">{{ quantity }}</div>
              <div class="product-view__quantity-button" @click="updateQuantity('increase')">+</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  head () {
    return {
      title: this.product.name + ' | Store',
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' },
        {
          hid: 'description',
          name: 'description',
          content: this.product.description
        }
      ],
      link: [{ rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }]
    }
  },

  async asyncData ({ $axios, route }) {
    const variations = []
    const [product] = await $axios.$get('/wc/v3/products?slug=' + route.params.product)
    if (product.type === 'variable') {
      // Loop through all variation ids
      for await (const id of product.variations) {
        const response = await $axios.get('/wc/v3/products/' + id)
        variations.push(response.data)
      }
    }

    return {
      product,
      variations
    }
  },

  data () {
    return {
      product: {},
      variations: [],
      activeColor: null,
      quantity: 1
    }
  },

  computed: {
    colorAttribute () {
      if (!this.product.attributes) return
      return this.product.attributes.find(attribute => attribute.name === 'Couleur')
    },
    displayedProduct () {
      if (!this.activeColor) return this.product
      // Filter variations array to find if there's a match with selected attributes (color)
      const [variation] = this.variations.filter((variation) => {
        return variation.attributes.find(attribute => attribute.option === this.activeColor)
      })
      return variation || this.product
    }
  },

  methods: {
    // Add product to cart
    addToCart () {
      this.$store.commit('add', { product: this.displayedProduct, quantity: this.quantity })
    },

    // Update wanted quantity before adding product to cart
    updateQuantity (action) {
      if (action === 'increase') {
        this.quantity++
      } else if (action === 'decrease' && this.quantity > 1) {
        this.quantity--
      }
    },

    // Update activeColor value
    changeColor (color) {
      this.activeColor = color
    }
  }
};
</script>

<style lang="scss">
.product-view {

  &__content {
    padding-top: 50px;
    padding-bottom: 50px;
  }

  &__name {
    margin: 0;
  }

  &__sku {
    display: inline-block;
    margin: 5px 0 0 0;
  }

  &__price {
    font-size: 25px;
    font-weight: 700;
    color: $secondary-color
  }

  &__subtitle {
    font-size: 20px;
    font-weight: 700;
    color: $secondary-color;
  }

  &__actions {
    display: flex;
    flex-flow: row wrap;
    align-items: center;
  }

  &__quantity {
    display: flex;
    flex-flow: row wrap;
    align-items: center;
  }

  &__quantity-button {
    padding: 5px 10px;
    background-color: black;
    color: white;
    line-height: 1;
    font-size: 22px;
    font-weight: 700;
    border: 1px solid black;
    cursor: pointer;

    &:hover {
      background-color: white;
      color: black;
    }
  }

  &__quantity-value {
    line-height: 1.2;
    font-size: 18px;
    padding: 5px 20px;
    border-top: 1px solid black;
    border-bottom: 1px solid black;
  }

}
</style>
