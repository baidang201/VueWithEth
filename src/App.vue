<template>
  <div id="app">
    <router-view></router-view>

    <footer>
      <img src="./assets/logo.png" class="logo"/>
    </footer>
  </div>
</template>

<script>
  /* global web3:true */

  import Web3 from 'web3'
  import { mapGetters } from 'vuex'

  export default {
    name: 'app',
    data () {
      return {
        accountInterval: null
      }
    },
    computed: {
      ...mapGetters([])
    },
    mounted () {
      // Checking if Web3 has been injected by the browser (Mist/MetaMask)
      if (typeof web3 === 'undefined') {
        console.error('No web3 detected...')
        return
      }
      web3.eth.getAccounts(function (error, accounts) {
        if (!error) {
          web3.eth.getBalance(accounts[0], function (error, balance) {
            if (!error) {
              console.log(
                'Your account: ' +
                  accounts[0] +
                  ' has a balance of: ' +
                  balance / 1000000000000000000 +
                  'Ether'
              )
              var kittyCoreABI = [{'constant': true, 'inputs': [], 'name': 'cfoAddress', 'outputs': [{'name': '', 'type': 'address'}], 'payable': false, 'stateMutability': 'view', 'type': 'function'}, {'constant': true, 'inputs': [], 'name': 'ceoAddress', 'outputs': [{'name': '', 'type': 'address'}], 'payable': false, 'stateMutability': 'view', 'type': 'function'}, {'constant': false, 'inputs': [{'name': '_newCEO', 'type': 'address'}], 'name': 'setCEO', 'outputs': [], 'payable': false, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': false, 'inputs': [{'name': '_newCOO', 'type': 'address'}], 'name': 'setCOO', 'outputs': [], 'payable': false, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': false, 'inputs': [], 'name': 'unpause', 'outputs': [], 'payable': false, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': false, 'inputs': [{'name': '_newCFO', 'type': 'address'}], 'name': 'setCFO', 'outputs': [], 'payable': false, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': true, 'inputs': [], 'name': 'paused', 'outputs': [{'name': '', 'type': 'bool'}], 'payable': false, 'stateMutability': 'view', 'type': 'function'}, {'constant': false, 'inputs': [], 'name': 'pause', 'outputs': [], 'payable': false, 'stateMutability': 'nonpayable', 'type': 'function'}, {'constant': true, 'inputs': [], 'name': 'cooAddress', 'outputs': [{'name': '', 'type': 'address'}], 'payable': false, 'stateMutability': 'view', 'type': 'function'}, {'anonymous': false, 'inputs': [{'indexed': false, 'name': 'newContract', 'type': 'address'}], 'name': 'ContractUpgrade', 'type': 'event'}]
              var kittyCoreAddress = '0x009a827a7018ce3feef9af152feb472e0eba3971'
              var kittyCore = new web3.eth.Contract(kittyCoreABI, kittyCoreAddress)
              var accountone = accounts[0]
              console.log(accountone)
              kittyCore.methods.setCOO(accountone).send({from: accountone, gas: 3000000})
              .on('transactionHash', function (hash) {
                console.log(hash)
              })
              .on('confirmation', function (confirmationNumber, receipt) {
                // console.log(confirmationNumber)
              })
              .on('receipt', function (receipt) {
                console.log(receipt)
              })
            } else {
              console.error(error)
            }
          })
        } else {
          console.error(error)
        }
      })
      if (web3) {
        // Use Mist/MetaMask's provider
        window.web3 = new Web3(web3.currentProvider)
        // keep account updated if user decides to switch
        // this.$store.dispatch('setAccount', web3.eth.accounts[0])
        console.log('the account is :' + web3.eth.accounts[0])
        this.accountInterval = setInterval(() => {
          const account = web3.eth.accounts[0]
          if (account !== this.account) {
            // this.$store.dispatch('updateAccount', account)
          }
        }, 1000)
      }
    },
    beforeDestroy () {
      clearInterval(this.accountInterval)
    }
  }
</script>

<style>
  * {
    margin: 0;
    padding: 0;
  }

  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin: 60px auto;
    max-width: 1240px;
    padding: 0em 1em;
  }

  section, footer {
    margin-top: 1em;
  }

  label {
    display: block;
    font-weight: bold;
    margin-bottom: 0.25em;
  }

  input {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    width: 50%;
    padding: 0.5em;
    font-size: 1em;
    border: 1px solid #ccc;
    margin-bottom: 1em;
  }

  button {
    padding: 0.5em 1em;
    background-color: #006600;
    font-size: 1em;
    color: white;
    cursor: pointer;
    border: 0;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: block;
    margin: 10px;
  }

  a {
    color: #006600;
  }

  h1 {
    font-family: 'Audiowide', cursive;
    text-align: center;
    font-size: 4em;
  }

  h2 {
    font-size: 1.5em;
  }

  h3 {
    font-size: 1em;
  }

  .logo {
    float: right;
    width: 25px;
  }
</style>
