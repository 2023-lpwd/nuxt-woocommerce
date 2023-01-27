<template>
  <div class="header">
    <div class="header__container | container">
      <p class="header__title"><RouterLink class="header__link" to="/">titre du site</RouterLink></p>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      categories: []
    }
  },

  computed: {
    cartCount () {
      if (!this.$store.state.cart) return
      return this.$store.state.cart.reduce((total, product) => {
        total += product.quantity
        return total
      }, 0)
    }
  }
}
</script>

<style lang="scss">
.header {
  padding: 2rem;
  background-color: grey;

  &__container {
    display: flex;
    flex-flow: row wrap;
    align-items: center;
  }

  &__title {
    display: inline-block;
    font-size: 20px;
    color: white;
    font-weight: 700;
  }

  &__navigation {
    display: flex;
    flex-flow: row wrap;
    padding-left: 50px;
  }

  &__navigation-item {
    &:not(:first-of-type) {
      margin-left: 20px;
    }
  }

  &__link {
    font-weight: 700;
    color: white;
    text-decoration: none;
  }

  &__cart {
    font-size: 20px;
    color: white;
    margin-left: auto;
  }
}
</style>
