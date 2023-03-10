<template>
  <div class="d-flex flex-column pa-md-4 mx-lg-auto">
    <div>
      <h3 class="mb-10">{{ product.MPN }}</h3>
    </div>
    <div class="d-flex">
      <v-card
        class="justify-center ml-3"
        v-for="(img, index) in product.images"
        :key="index"
        @click="getProduct"
      >
        <v-img
          class="align-end text-white pa-md-16"
          :width="250"
          :height="200"
          :src="img"
          :alt="img"
          @click="selectedIndex = index"
          :class="{ selected: selectedIndex == index }"
        >
        </v-img>
      </v-card>
    </div>
    <div class="button-group">
      <v-btn variant="tonal" @click="downloadImage()"> Skip </v-btn>
      <v-btn color="info" @click="downloadImage()"> Download </v-btn>
    </div>
  </div>
</template>

<script>
import { saveAs } from "file-saver";

export default {
  props: ["product"],
  data() {
    return { selectedImage: "", selectedIndex: null };
  },
  created() {
    console.log("PRODUCT COMPONENT", this.product);
    console.log("SELECTED IMAGE", this.selectedImage);
  },
  methods: {
    getProduct(product) {
      this.isSelected = !this.isSelected;
      this.selectedImage = product.target.src;
      console.log("SELECTED IMAGE", this.selectedImage);
    },
    async downloadImage(value) {
      if (this.selectedImage == "") {
        alert("Bir Resim Seç");
        return;
      }
      try {
        const corsAnywhereUrl = "https://cors-anywhere.herokuapp.com/";
        const response = await fetch(corsAnywhereUrl + this.selectedImage);
        const blob = await response.blob();
        this.selectedImage = "";

        saveAs(blob, this.product.MPN);
      } catch (e) {
        throw new Error(e);
      }

      console.log("Download IMAGE", this.selectedImage);
    },
  },
};
</script>

<style scoped>
.button-group {
  margin-top: 40px;
  display: flex;
  justify-content: flex-end;
}
button {
  margin-left: 10px;
}
v-img {
}
</style>
