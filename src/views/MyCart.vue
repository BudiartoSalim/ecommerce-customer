<template>
  <div class="row">
    <div class="col-1">
    </div>
    <h1 class="col-6">My Cart</h1>
    <button class="col-3 btn btn-primary btn-lg" @click="goToHistory()">History</button>
    <table class="table">
      <thead>
        <tr class="row">
          <th class="col-3"></th>
          <th scope="col" class="col-3" style="word-wrap: break-word">Name</th>
          <th scope="col" class="col-1" style="word-wrap: break-word">
            Amount
          </th>
          <th scope="col" class="col-2">Price</th>
          <th scope="col" class="col-3"></th>
        </tr>
      </thead>
      <tbody v-if="fetchedContent.length > 0">
        <CartContent
          v-for="content in fetchedContent"
          :key="content.id"
          :content="content"
        >
        </CartContent>
        <tr class="row">
          <th class="col-3"></th>
          <th scope="col" class="col-4" style="word-wrap: break-word">
            Total Price:
          </th>
          <th scope="col" class="col-3">
            {{
              new Intl.NumberFormat("de-DE", {
                style: "currency",
                currency: "IDR",
              }).format(totalPrice)
            }}
          </th>
          <th scope="col" class="col-1" style="word-wrap: break-word">
            <button class="btn btn-lg btn-success" @click="checkout()">Checkout</button>
          </th>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import cmsApi from '../api/cms-api'
import CartContent from '../components/CartContent.vue'
export default {
  components: { CartContent },
  computed: {
    totalPrice () {
      let output = 0
      this.fetchedContent.forEach((el) => {
        output += el.Product.price * el.amount
      })
      return output
    },
    fetchedContent () {
      return this.$store.state.cartContent
    }
  },
  methods: {
    goToHistory () {
      this.$router.push('/history')
    },
    checkout () {
      cmsApi({
        method: 'PATCH',
        url: '/cart/checkout',
        headers: { access_token: localStorage.getItem('access_token') }
      })
        .then(result => {
          this.$router.push('/')
        })
        .catch((err) => {
          this.$store.commit('UPDATE_ERROR_MESSAGE', err.response.data.message)
        })
    }
  },
  created () {
    this.$store.dispatch('fetchCartContent')
  }
}
</script>

<style>
</style>
