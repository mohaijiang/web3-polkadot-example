<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <div>
      <h2>web3</h2>
      <div>
        <button @click="hookWeb3"> web3连接 </button>
        <button @click="transform"> 调用转账 </button>
      </div>
    </div>
    <div>
      <h2>polkadot</h2>
      <div>
        <button @click="connectPolkadot"> polkadot 连接 </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>

import { defineProps, ref } from 'vue'
import { ABI } from './contract'
import Web3 from 'web3'
import { ApiPromise, WsProvider } from '@polkadot/api'

defineProps({
  msg: {
    type: String,
    required: true
  }
})

const connected = ref(false)

const hookWeb3 = function () {
  if (window.ethereum) {
    window.ethereum.request({ method: 'eth_requestAccounts' })
      .then(() => {
        connected.value = true
      })
  } else {
    alert('请安装MateMask')
  }
}

const transform = async function () {
  const web3 = new Web3(window.ethereum)
  const accounts = await web3.eth.getAccounts()
  console.log(accounts)
  const contract = new web3.eth.Contract(ABI, '0x58DC15156C520cB4d18Df8807419c1989B05c960')

  contract.methods.burn(1, '5Ck8UKvwPkx6ALYib5gZCQu95se6VgDMEvohfQS6gvQ4LtqQ').send({
    from: accounts[0]
  }).on('transactionHash', function (hash) {
    console.log('transactionHash:', hash)
  }).on('confirmation', function (confirmationNumber, receipt) {
    console.log('confirmation:', confirmationNumber)
  }).on('receipt', function (receipt) {
    // receipt example
    console.log('receipt:', receipt)
  }).on('error', function (error) {
    console.log('error:', error)
  })
}

const connectPolkadot = function () {
  const wsProvider = new WsProvider('wss://polkadot.authright-test.newtouch.com')
  const api = await ApiPromise.create({ provider: wsProvider })
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
