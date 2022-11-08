<template>
  <div>
    <ComponentNavAdmin />
    <br />
    <div class="text-center" v-if="mListaVentasRealizadas.length <= 0">
      <v-img class="d-block mx-auto" src="/emptycart.svg" width="500"></v-img>
      <p>ACTUALMENTE NO TIENE COMPRAS REALIZADAS.</p>
    </div>
    <v-container>
      <el-table
        v-if="mListaVentasRealizadas.length > 0"
        :data="mListaVentasRealizadas"
      >
        <el-table-column min-width="150" prop="_id" label="Codigo">
        </el-table-column>
        <el-table-column min-width="200" prop="idPaypal" label="Code Paypal">
        </el-table-column>

        <el-table-column
          min-width="200"
          prop="date_Compra"
          label="Fecha Compra"
        >
        </el-table-column>

        <el-table-column min-width="150" prop="total" label="Total($)">
        </el-table-column>
      </el-table>
    </v-container>
    <br /><br />
    <Footer />
    <ScrollTop />
  </div>
</template>

<script>
import NavAdmin from "../components/Desktop/NavAdmin.vue";
import { Table, TableColumn } from "element-ui";

export default {
  components: {
    [Table.name]: Table,
    [TableColumn.name]: TableColumn,
    ComponentNavAdmin: NavAdmin,
  },
  data() {
    return {
      mListaVentasRealizadas: [],
    };
  },

  methods: {
    getFecha_format(fecha) {
      var new_fecha = new Date(fecha);

      var dia = new_fecha.getDate();

      var mes = new_fecha.getMonth();

      mes += 1;

      var min = new_fecha.getMinutes();
      var hour = new_fecha.getHours();
      var seg = new_fecha.getSeconds();

      if (dia < 10) {
        dia = "0" + dia;
      }
      if (mes < 10) {
        mes = "0" + mes;
      }

      if (hour < 10) {
        hour = "0" + hour;
      }

      if (min < 10) {
        min = "0" + min;
      }

      if (seg < 10) {
        seg = "0" + seg;
      }

      return (
        new_fecha.getFullYear() +
        "-" +
        mes +
        "-" +
        dia +
        " " +
        hour +
        ":" +
        min +
        ":" +
        seg
      );
    },

    async readVentasRealizadas() {
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/readVentasRealizadasPaypal",
          {
            token: this.$cookies.get("token"),
          }
        )

        console.log(datos.data.datos)

        for (var i = 0; i < datos.data.datos.length; i++) {
          datos.data.datos[i].date_Compra = this.getFecha_format(
            datos.data.datos[i].date_Compra
          );
        }

        this.mListaVentasRealizadas.push(...datos.data.datos);
      } catch (error) {
        console.log(error.toString())
        this.mListaVentasRealizadas = [];
      }

      console.log(this.mListaVentasRealizadas)
    },
  },
  mounted() {
    this.readVentasRealizadas();
  },
};
</script>

<style></style>
