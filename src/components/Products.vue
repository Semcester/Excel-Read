<template>
  <v-responsive class="d-flex align-center text-center fill-height">
    <div class="products" v-for="product in excelData" :key="product.STATUS">
      <app-product :product="product"></app-product>
    </div>
  </v-responsive>
</template>

<script>
import Product from "./Product.vue";
export default {
  components: {
    appProduct: Product,
  },
  props: ["excelData"],
};
</script>

<style scoped>
.products {
  display: flex;
  text-align: center;
}
</style>
