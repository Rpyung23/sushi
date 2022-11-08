<template>
  <div>
    <ComponentNavAdmin />

    <v-container>
      <div lazy-validation ref="form" class="mt-2">
        <p class="font-weight-bold">Nuevo Producto</p>
        <v-row>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalNameProducto"
              outlined
              label="Nombre Producto"
              type="text"
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalDescripProducto"
              outlined
              label="Descripción Producto"
              type="text"
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalCategoriasProducto"
              outlined
              label="Categoria"
              type="text"
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalPrecioProducto"
              outlined
              label="Precio Producto"
              type="number"
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalDescuentoProducto"
              outlined
              label="Descuento Producto"
              type="number"
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              v-model="modalStockProducto"
              outlined
              label="Stock"
              type="number"
            ></v-text-field>
          </v-col>
          <v-col cols="12" md="12">
            <v-text-field
              v-model="modalLinkProducto"
              outlined
              label="Imagen Producto"
              type="text"
            ></v-text-field>
          </v-col>
        </v-row>
      </div>
      <div class="mb-3">
        <v-btn @click="insertNuevoProducto()" min-width="150" min-height="45" color="primary"
          >REGISTRAR PRODUCTO</v-btn
        >
      </div>
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
import NavAdmin from "../../components/Desktop/NavAdmin";
import FM from "~/mixins/FormMixinx";

export default {
  mixins: [FM],
  components: {
    ComponentNavAdmin: NavAdmin,
  },
  data() {
    return {
      modalNameProducto: "",
      modalDescripProducto: "",
      modalCategoriasProducto: "",
      modalPrecioProducto: 0.0,
      modalDescuentoProducto: 0.0,
      modalStockProducto: 0,
      modalLinkProducto: "",
    };
  },
  methods: {
    async insertNuevoProducto() {
      try {
        var datos = await this.$axios.post(process.env.baseUrl + "/products", {
          category: this.modalCategoriasProducto,
          name: this.modalNameProducto,
          description: this.modalDescripProducto,
          imagen: this.modalLinkProducto,
          price: this.modalPrecioProducto,
          discount: this.modalDescuentoProducto,
          stock: this.modalStockProducto,
        });

        if (datos.data != null) {
          this.$router.push("/indexAdmin");
        } else {
          this.$swal({
          toast: true,
          text: "No se pudo registrar el producto",
          icon: "warning",
          timer: 2000,
          timerProgressBar: true,
          showConfirmButton: false,
          position: "top-end",
        });
        }
      } catch (error) 
      {
        this.$swal({
          toast: true,
          text: error.toString(),
          icon: "warning",
          timer: 2000,
          timerProgressBar: true,
          showConfirmButton: false,
          position: "top-end",
        });
      }
    },
  },
};
</script>

<style></style>
