<template>
  <div>
    <DesktopNav />
    <HomeCarousel :sale_items="sale_items" />
    <br /><br />
    <v-container>
      <h1 class="text-md-h4 text-h6">Nuestros Productos</h1>
      <br />
      <ProductSlider :products="sale_items" />
      <br /><br /><br />
      <!--<Newsletter />-->
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
export default {
  async created() {
    this.sale_items = [
  {
    "id": 1,
    "name": "Sushi Mixto",
    "onSale": false,
    "tags": ["Comida", "Sushi"],
    "image": "https://firebasestorage.googleapis.com/v0/b/vigitrack-empresas.appspot.com/o/301128508_168638429051724_4155866758676802258_n.jpg?alt=media",
    "description": "Sushi es un plato típico de origen japonés basado en arroz aderezado con vinagre de arroz, azúcar y sal y combinado con otros ingredientes como pescados crudos, mariscos, verduras, etc.",
    "price": 10.50,
    "salePrice": 10.80,
    "ratings": 4.0
  },
  {
    "id": 2,
    "name": "Onigiri",
    "onSale": true,
    "tags": ["Comida", "Onigiri"],
    "image": "https://firebasestorage.googleapis.com/v0/b/vigitrack-empresas.appspot.com/o/329083.jpg?alt=media",
    "description": "Onigiri también conocido como Omusubi es un plato japonés que consiste en una bola de arroz rellena o mezclada con otros ingredientes. Suele tener forma triangular u oval, y a veces está envuelta en una pequeña tira de alga nori.",
    "price": 9.20,
    "salePrice": 9.80,
    "ratings": 4.5
  },
  {
    "id": 3,
    "name": "Ramen ",
    "onSale": true,
    "tags": ["Nike", "Men"],
    "image": "https://firebasestorage.googleapis.com/v0/b/vigitrack-empresas.appspot.com/o/8496b9_dec9e6545a8e4cd4a91c50adeac43f4c_mv2.jpg?alt=media",
    "description": "El ramen es un plato de fideos japonés. Si bien cada región de Japón tiene su propia receta de ramen, la preparación básica consiste en distintos tipos de fideos japoneses servidos en un caldo preparado.",
    "price": 20.10,
    "salePrice": 21.80,
    "ratings": 4.5
  }
]
  },
  data() {
    return {
      products: null,
      sale_items: null,
    };
  },
};
</script>

<style></style>
