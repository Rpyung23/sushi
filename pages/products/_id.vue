<template>
  <div>
    <DesktopNav />
    <v-container v-if="product">
      <v-row justify="center">
        <v-col cols="11" md="7">
          <h2 class="text-center text-md-h4 font-weight-bold">
            {{ product.name }}
          </h2>
          <div class="mt-2 text-center">
            <v-rating
              readonly
              half-increments
              class="mb-2"
              color="yellow darken-2"
              background-color="grey lighten-1"
              :value="product.ratings == undefined ? 4 : product.ratings"
              dense
              size="20"
            ></v-rating>
            <v-chip
              small
              label
              outlined
              class="mr-1"
              v-for="(t, i) in product.tags"
              :key="`prod${product.id}-${i}`"
            >
              {{ t }}
            </v-chip>
          </div>
          <br />
          <v-img
            width="100%"
            class="el rounded-lg"
            height="50vh"
            :src="product.imagen"
          ></v-img>
          <p class="mt-5 mb-7">
            {{ product.description }}
          </p>
          <v-btn
            @click="$store.commit('cart/AddToCart', product)"
            min-height="45"
            min-width="170"
            class="text-capitalize"
            color="primary"
            >Añadir al carrito</v-btn
          >
        </v-col>
      </v-row>
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
export default {
  async created() {
    let datos = await this.$axios.get(process.env.baseUrl+"/product/"+this.$route.params.id)
    this.product = datos.data;
  },
  data() {
    return {
      product: null,
    };
  },
};
</script>

<style></style>
