<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-md-6" style="letter-spacing: .4px; text-align: left;">
          PAYEE<br>
          <span style="font-size: 40px; font-weight: 600; line-height: 1;">
            Po Rith
          </span>
        </div>
        <div class="col-md-6" style="text-align: left;">
          <span style="letter-spacing: .4px;">AMOUNT</span><br>
          <span style="font-size: 40px; font-weight: 600; line-height: 1.1;">
            $19.99
          </span>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row" style="margin-top: 24px;">
        <div class="col-md-6" style="text-align: left;">
          <span style="letter-spacing: .4px;">PAYER</span><br>
          <span style="font-size: 40px; font-weight: 600; line-height: 1.1;">
            Name<br>
            Address<br>
            City, State (Zipcode)<br>
          </span>
        </div>
        <div class="col-md-6" style="text-align: left;">
          <span style="letter-spacing: .4px;">CARD INFO</span><br>
          <span style="font-size: 40px; font-weight: 600;">
            <div id="card-number"></div>
            <div id="card-expiry"></div>
            <div id="card-cvc"></div>
          </span>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row" style="margin-top: 24px;">
        <div class="col-md-6" style="text-align: left;">
          <!-- <span style="letter-spacing: .4px;">COMPLETE</span><br> -->
          <!-- <span style="font-size: 40px; font-weight: 600; line-height: 1.1;">
            $10.00
          </span> -->
          STEP 1<br>
          <div class="pay-btn" @click="requestStripeToken" style="display: inline-block; letter-spacing: .4px; padding: 8px 22px; color: #FFF; margin-top: 8px; background-color: #4990E2; font-weight: 600;">
            <!-- PAY NOW -->
            GET TOKEN
          </div>
        </div>
        <div class="col-md-6" style="text-align: left;">
          TOKEN<br>
          {{ token }}
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row" style="margin-top: 4px;">
        <hr>
        <div class="col-md-6" style="text-align: left;">
          <!-- <span style="letter-spacing: .4px;">COMPLETE</span><br> -->
          <!-- <span style="font-size: 40px; font-weight: 600; line-height: 1.1;">
            $10.00
          </span> -->
          STEP 2<br>
          <div class="pay-btn" @click="requestCreateCharge" style="display: inline-block; letter-spacing: .4px; padding: 8px 22px; color: #FFF; margin-top: 8px; background-color: #4990E2; font-weight: 600;">
            CREATE CHARGE
          </div>
        </div>
        <div class="col-md-6" style="text-align: left;">
          STATUS<br>
          {{ charge }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'app',
  computed: {
    stripe: function () { return window.Stripe(this.stripeKey); }
  },
  mounted: function () {
    var stripe = this.stripe
    var elements = stripe.elements()
    var style = {
      base: {
        lineHeight: '44px',
        fontWeight: '600',
        color: '#2c3e50',
        fontFamily: '"Helvetica Neue", "Helvetica", Arial, sans-serif',
        fontSmoothing: 'antialiased',
        fontSize: '40px',
        '::placeholder': {
          color: '#2c3e50',
        },
      }
    };
    var cardCvc = elements.create('cardCvc', {
      style: style,
      placeholder: "CVC"
    })
    cardCvc.mount('#card-cvc')
    var cardNumber = elements.create('cardNumber', {
      style: style,
      placeholder: "Card number"
    })
    cardNumber.mount('#card-number')
    var cardExpiry = elements.create('cardExpiry', {
      style: style,
      placeholder: "Expiry"
    })
    cardExpiry.mount('#card-expiry')
    this.cardNumber = cardNumber
  },
  data: () => ({
    card: null,
    cardNumber: null,
    token: null,
    charge: null,
    stripeKey: 'pk_test_HefkMN6uLAn39798BFOL7LN1',
  }),
  methods: {
    requestStripeToken: function () {
      var self = this
      var stripe = this.stripe
      console.log(this.cardNumber)
      stripe.createToken(this.cardNumber).then(function(result) {
         if (result.error) {
           console.log("Error")
           self.token = result.error.message
         } else {
           self.token = result.token.id
           // console.log("Success")
           console.log(result.token)
         }
       });
    },
    requestCreateCharge: function () {
      let self = this
      let data = {
        token: self.token,
        amount: 1999
      }

      if (self.token) {
        axios.post('https://us-central1-testing-time-server.cloudfunctions.net/date', data)
        .then(function (response) {
          self.charge = response.data
          console.log(response.data)
        })
        .catch(function (error) {
          console.log('error');
        });
      } else {
        alert('Create a token first.')
      }
    }
  }
}
</script>

<style>
#app {
  font-family: "Helvetica Neue", "Helvetica", Arial, sans-serif;
  font-size: 14px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 52px;
}

body {
  background-color: #F8F8F8;
}

.pay-btn:hover {
  background-color: #3D68B6 !important;
}

.pay-btn:active {
  background-color: #85D330 !important;
}

</style>
