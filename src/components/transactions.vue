<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Transactions</h2>
    <ul>
      <li v-for="(transaction, index) in transactions">
        {{transaction.date}}: {{transaction.from}} -> {{transaction.to}} {{transaction.amount}} {{transaction.currency}}
        <br>{{transaction.balance}}
      </li>
    </ul>

  </div>
</template>

<script>
  export default {
    name: 'Transactions',
    data() {
      return {
        msg: '',
        collective1: {
          initialBalance: 0,
          name: 'webpack'

        }
        ,
        transactions: [
          {
            type: 'donation',
            from: 'user1',
            to: 'collective1',
            amount: 100,
            currency: 'usd',
            date: (new Date() - 1000),
            fees: {
              opencollective: {
                percentage: 0.05
              },
              fiscalSponsor: {
                percentage: 0.05
              },
              stripe: {
                fixed: 0.30,
                percentage: 0.029
              }
            }
          },
          {
            type: 'expense',
            from: 'collective1',
            to: 'user2',
            amount: -50,
            currency: 'usd',
            date: +new Date(),
            fees: {
              paypal: {
                fixed: 0.30,
                percentage: 0.029
              }
            }
          },
        ],
        balance: []
      }
    },

    methods: {
      getBalance: function() {

        console.log('???')

        return this.transactions.map((value, index) => {
          const currentBalance = index === 0 ? this.collective1.initialBalance : this.transactions[index - 1].balance;

          console.log(`###transaction ${value.date} `)

          let amount = Object.keys(value.fees).reduce((prev, fee) => {

            console.log(`before fee ${fee} `, prev, 'fixed', value.fees[fee].fixed,  'percentage', value.fees[fee].percentage)
            if (value.fees[fee].percentage) {
              prev -= prev*value.fees[fee].percentage;
            }

            if (value.fees[fee].fixed) {
              prev -= value.fees[fee].fixed;
            }

            return prev;

          }, value.amount);

          value.balance = amount;

          return value;

        });

      }
    },
    beforeMount() {
      this.getBalance()
    }

  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: block;
    margin: 10px 200px;
    text-align: left;
  }

  a {
    color: #42b983;
  }
</style>
