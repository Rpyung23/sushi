<template>
  <div>
    <DesktopNav />
    <br />
    <div class="text-center" v-if="$store.state.cart.cart.length == 0">
      <v-img class="d-block mx-auto" src="/emptycart.svg" width="500"></v-img>
      <p>ACTUALMENTE NO TIENE PRODUCTOS EN EL CARRITO.</p>
    </div>
    <v-container>
      <div
        style="display: flex; align-content: center; align-items: center"
        v-if="$store.state.cart.cart.length > 0"
      >
        <v-btn
          nuxt
          min-width="150"
          @click="completarPagoPaypal()"
          min-height="45"
          color="primary"
          style="margin-right: 2rem"
          >COMPLETAR PAGO</v-btn
        >
        <PayPal
          :amount="getDinero()"
          currency="USD"
          :client="credentials"
          env="sandbox"
          v-on:payment-authorized="paymentAuthorized"
        v-on:payment-completed="paymentCompleted"
        v-on:payment-cancelled="paymentCancelled"
        >
        </PayPal>
      </div>
      <br />

      <v-row>
        <template v-for="(c, i) in $store.state.cart.cart">
          <v-col :key="`cartItem${i}`">
            <v-card color="surface" flat>
              <v-btn
                @click="$store.commit('cart/RemoveCartItem', i)"
                absolute
                top
                right
                icon
              >
                <v-icon size="18">mdi-delete</v-icon>
              </v-btn>

              <v-row dense>
                <v-col md="3">
                  <v-img
                    class="rounded-lg"
                    height="220"
                    :src="c.product.imagen"
                  ></v-img>
                </v-col>
                <v-col class="pl-5 pt-2" md="9">
                  <h2 class="text-md-h6 font-weight-bold">
                    {{ c.product.name }} x {{ c.quantity }}
                  </h2>
                  <p class="primary--text mt-2">
                    {{ $formatMoney(c.product.price * c.quantity) }}
                  </p>
                  <v-btn
                    @click="$store.commit('cart/IncreaseItemCount', i)"
                    icon
                  >
                    <v-icon size="20">mdi-plus-circle</v-icon>
                  </v-btn>
                  <span class="mx-2">{{ c.quantity }}</span>
                  <v-btn
                    @click="$store.commit('cart/DecreaseItemCount', i)"
                    icon
                  >
                    <v-icon size="20">mdi-minus-circle</v-icon>
                  </v-btn>
                </v-col>
              </v-row>
            </v-card>
          </v-col>
        </template>
      </v-row>
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
import PayPal from "vue-paypal-checkout";
export default {
  components: {
    PayPal,
  },
  data() {
    return {
      credentials: {
        sandbox:
          "AW5M_dedVij9riC3tZgWWL7mY7oXZFbWmZiv3oVcwbpRhzt5AhHp_x9Q1WmVLRLiaxgXgkomfSZJvVPx",
        production:
          "AW5M_dedVij9riC3tZgWWL7mY7oXZFbWmZiv3oVcwbpRhzt5AhHp_x9Q1WmVLRLiaxgXgkomfSZJvVPx",
      },
    };
  },
  methods: {

    async paymentAuthorized(data){
      console.log("AUTORIZADO")
      console.log(data)
    },
    async paymentCompleted(data)
    {
      console.log("COMPLETADO")
      console.log(data)
      if (data.state == "approved") {
          localStorage.setItem("myCart", JSON.stringify([]));

          var datos = await this.$axios.post(
          process.env.baseUrl + "/registerPagoPaypal",
          {
            idPaypal:data.id,
            total: data.transactions[0].amount.total,
            token: this.$cookies.get("token"),
          }
        );

        if(datos.data.status_code != 200)
        {
          alert("NO SE PUEDO REGISTAR EL COMPROBANTE EN MONGO DB")
        }


          this.$swal({
            toast: true,
            text: "PAGO REALIZADO CON EXITO",
            icon: "success",
            timer: 2000,
            timerProgressBar: true,
            showConfirmButton: false,
            position: "top-end",
          });
          this.$store.commit('cart/ClearCart')
          this.$nuxt.refresh();
          this.$router.push("/");
        } else {
          alert("ERROR PAYPAL COMPLETED");
        }
    },
async paymentCancelled(data){
  console.log("CNACELADO")
      console.log(data)
      alert("PAYPAL paymentCancelled")
},
    async completarPagoPaypal() {
      var datos = localStorage.getItem("myCart");
      var body = null;
      var precioTotal = 0;
      var amount = {
        currency: "USD",
        total: "0.00",
      };
      var item = [];

      if (datos.length > 0) {
        var datosJSON = JSON.parse(datos);
        console.log("--------------------------------------------")
        console.log(datosJSON);
        console.log("--------------------------------------------")
        for (var i = 0; i < datosJSON.length; i++) {
          var cantidad = datosJSON[i].quantity;
          precioTotal =
            precioTotal + parseFloat(datosJSON[i].product.price) * cantidad;

          var precioTotalU = parseFloat(datosJSON[i].product.price) * cantidad  

          var obj = {
            name: datosJSON[i].product.name,
            sku: datosJSON[i].product.name,
            price: precioTotalU.toFixed(2),
            currency: "USD",
            quantity: 1,
          }

          item.push(obj);
        }

        amount.total = Number(precioTotal).toFixed(2);

        body = {
          token: this.$cookies.get("token"),
          amount: amount,
          items: item,
        };

        console.log(body);

        var datos = await this.$axios.post(
          process.env.baseUrl + "/PagoPaypal",
          body
        );

        console.log(datos.data);

        if (datos.data.msm == "sale created") {
          localStorage.setItem("myCart", JSON.stringify([]));

          this.$swal({
            toast: true,
            text: "PAGO REALIZADO CON EXITO",
            icon: "success",
            timer: 2000,
            timerProgressBar: true,
            showConfirmButton: false,
            position: "top-end",
          });
          this.$store.commit('cart/ClearCart')
          this.$nuxt.refresh();
          this.$router.push("/");
        } else {
          alert(datos.data.msm);
        }
      } else {
        alert("SIN DATOS STORE");
      }
    },
    getDinero() {

      var datos = localStorage.getItem("myCart")

      var precioTotal = 0

      var amount = {
        currency: "USD",
        total: "0.00",
      }

      var item = []

      if (datos.length > 0) {

        var datosJSON = JSON.parse(datos)

        for (var i = 0; i < datosJSON.length; i++) 
        {
          var cantidad = datosJSON[i].quantity

          precioTotal =
            precioTotal + parseFloat(datosJSON[i].product.price) * cantidad

          var obj = {
            name: datosJSON[i].product.name,
            sku: datosJSON[i].product.name,
            price: precioTotal.toFixed(2),
            currency: "USD",
            quantity: 1
          }

          item.push(obj)
        }

        amount.total = Number(precioTotal).toFixed(2);
        return amount.total
      }

      return 0

    },
  },
};
</script>

<style></style>
