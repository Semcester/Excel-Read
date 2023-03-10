<template>
  <div
    style="
      box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
      border-radius: 5px;
    "
    class="d-flex flex-column pa-md-4 mx-lg-auto mb-10 mt-10"
  >
    <v-snackbar v-model="snackbar" :timeout="timeout" position="top">
      {{ text }}

      <template v-slot:actions>
        <v-btn color="blue" variant="text" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
    <div>
      <v-table theme="light" class="d-flex mb-10">
        <thead>
          <tr class="font-weight-bold">
            <th class="text-left">MPN</th>
            <th class="text-left">BAŞLIK</th>
            <th class="text-left">DETAY</th>
            <th class="text-left">ARAÇ MODEL</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ product.MPN }}</td>
            <td>{{ product.BASLIK }}</td>
            <td>{{ product.DETAY }}</td>
            <td>{{ product.ARACMODEL }}</td>
          </tr>
        </tbody>
      </v-table>
    </div>

    <div class="d-flex mb-5">
      <v-card
        class="justify-center ml-3"
        v-for="(img, index) in product.images"
        :key="index"
        @click="getProduct(img.url)"
      >
        <v-img
          @load="getDemention(index)"
          ref="image"
          class="align-end text-white pa-md-16"
          :width="250"
          :height="200"
          :src="img.url"
          :alt="img.url"
          @click="selectedIndex = index"
          :class="{ selected: selectedIndex == index }"
        ></v-img>
        <v-card-text>
          Image Size:<strong
            >{{ img.originalWidth }} x {{ img.originalHeight }}</strong
          >
        </v-card-text>
        <v-card-actions class="d-flex justify-center">
          <v-btn
            :width="180"
            variant="tonal"
            color="info"
            @click="downloadImage(img.url)"
          >
            Download Image
          </v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>
import { saveAs } from "file-saver";
import axios from "axios";

export default {
  props: ["product"],
  data() {
    return {
      selectedImage: "",
      selectedIndex: null,
      snackbar: false,
      text: "Bu fotoğraf indirilmiyor. Sağ tıklayıp indirin...",
      timeout: 4000,
    };
  },

  methods: {
    async getImageSize(index) {
      return new Promise((resolve, reject) => {
        const img = new Image();
        img.onload = () => {
          resolve({ width: img.naturalWidth, height: img.naturalHeight });
        };
        img.onerror = () => {
          reject(new Error(`Failed to load image at index ${index}`));
        };
        img.src = this.product.images[index].url;
      });
    },
    async getDemention(index) {
      const imageSize = await this.getImageSize(index);

      this.product.images[index].originalWidth = imageSize.width;
      this.product.images[index].originalHeight = imageSize.height;
    },
    async downloadImage(product) {
      if (product.endsWith(".webp")) {
        product = product.slice(0, -6);
      }
      console.log("PRODUCT SON HALİ", product);
      this.isSelected = !this.isSelected;
      this.selectedImage = product;
      console.log("SELECTED IMAGE", this.selectedImage);
      if (this.selectedImage == "") {
        alert("Bir Resim Seç");
        return;
      }
      try {
        const corsAnywhereUrl = "https://cors-anywhere.herokuapp.com/";

        const response = await fetch(corsAnywhereUrl + this.selectedImage);
        if (response.ok === true) {
          const blob = await response.blob();
          this.selectedImage = "";

          saveAs(blob, this.product.MPN);
        } else {
          this.snackbar = true;
        }
      } catch (e) {
        console.log("HATAAAA");
      }

      console.log("Download IMAGE", this.selectedImage);
    },
    getProduct(product) {
      if (product.endsWith(".webp")) {
        product = product.concat(".jpg");
      }
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
</style>
