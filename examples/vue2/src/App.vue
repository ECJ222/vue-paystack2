<template>
    <VuePaystack :paystack-key="paystackKey" :firstname="firstname" :lastname="lastname" :email="email" :amount="amount"  :reference="reference" :callback="onSuccess" :close="onClose" :embed="false">
      Button
    </VuePaystack>
</template>

<script>
import VuePaystack from "vue-paystack2"

export default {
  name: 'App',
  components: {
    VuePaystack
  },
  data () {
    return { 
      paystackKey: 'pk_test_xxxxxxxxxxxxxxxxxxx',
      email: 'test@example.com',
      firstname: 'test',
      lastname: 'test',
      amount: 1000,
    }
  },
  computed: {
    reference () {
      return Math.random().toString(36).substring(2, 10)
    }
  },
  methods: {
    onSuccess (e) {
      console.log(e)
    },
    onClose (e) {
      console.log(e)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
