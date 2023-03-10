<template>
  <v-app>
    <v-main>
      <v-container>
        <v-responsive class="d-flex align-center text-center fill-height">
          <HelloWorld />
          <app-read-file @excelData="getData"></app-read-file>
          <app-products :excelData="excelData"></app-products>
          <!--  -->
        </v-responsive>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "@/components/HelloWorld.vue";
import ReadExcelFile from "./components/ReadExcelFile.vue";
import Products from "./components/Products.vue";
export default {
  components: {
    appReadFile: ReadExcelFile,
    appProducts: Products,
  },
  data() {
    return { excelData: [] };
  },
  methods: {
    getData(value) {
      this.excelData = value;

      console.log("APP COMP", this.excelData);
    },
    getIndex(value) {
      console.log(value.target.value);
    },
  },
  created() {},
};
</script>

<style>
body {
  background-color: red;
}
</style>
