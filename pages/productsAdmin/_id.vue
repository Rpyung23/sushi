<template>
  <div>
    <ComponentNavAdmin />
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
        </v-col>
        <v-btn
        min-width="300"
        min-height="45"
        color="red"
        @click="deleteProducto()"
        style="color:white;"
        >ELIMINAR</v-btn
      >
      </v-row>
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
import NavAdmin from "../../components/Desktop/NavAdmin.vue";

export default {
  components:{
    ComponentNavAdmin: NavAdmin,
  },
  methods: {
    async deleteProducto(){
      await this.$axios.delete(process.env.baseUrl+"/products/"+this.$route.params.id)
      this.$router.push("/indexAdmin");
    }
  },
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
