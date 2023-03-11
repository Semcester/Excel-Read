<template>
  <div
    style="
      box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
      border-radius: 5px;
    "
    class="d-flex flex-column pa-md-4 mx-lg-auto mb-10 mt-10"
  >
    <v-snackbar
      v-model="snackbar"
      :timeout="timeout"
      style="position: top"
      color="red-darken-2"
    >
      {{ text }}

      <template v-slot:actions>
        <v-btn color="white" variant="text" @click="snackbar = false">
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
        v-for="(img, index) in imageUrl"
        :key="index"
        @click="getProduct(img.url, product.MPN)"
        :class="{ selected: selectedIndex == index }"
      >
        <h3 class="mt-3" color="green" v-if="selectedIndex == index">
          Checked
        </h3>
        <v-img
          @load="getDemention(index)"
          ref="image"
          class="align-end text-white pa-md-16"
          :width="250"
          :height="200"
          :src="img.url"
          :alt="img.url"
          @click="selectedIndex = index"
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
            @click="getProduct(img.url, product.MPN)"
          >
            Download Image
          </v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: ["product"],
  data() {
    return {
      selectedImage: "",
      selectedIndex: null,
      snackbar: false,
      text: "Bu fotoğraf indirilmiyor. Sağ tıklayıp indirin...",
      timeout: 2000,
    };
  },
  computed: {
    imageUrl() {
      const imageArray = this.product.images;
      for (let i = 0; i < imageArray.length; i++) {
        const item = imageArray[i].url;

        if (item.endsWith(".webp")) {
          imageArray[i].url = item.slice(0, -6);
        }
      }
      return imageArray;
    },
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
    async getImage(product) {},

    async getProduct(url, fileName) {
      console.log("FN", url);
      console.log("NAME", fileName);
      if (url.endsWith(".webp")) {
        url = url.slice(0, -6);
      }
      try {
        const response = await axios.get(
          "http://localhost:3000/downloadImage",
          {
            params: { url, fileName },
          }
        );

        if (response.data.success) {
          console.log("Image downloaded successfully!");
        } else {
          console.error("Error downloading image:", response.data.message);
        }
      } catch (err) {
        console.error("Error downloading image:", err.message);
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
selected {
  box-shadow: rgba(50, 50, 93, 0.25) 0px 50px 100px -20px,
    rgba(0, 0, 0, 0.3) 0px 30px 60px -30px,
    rgba(10, 37, 64, 0.35) 0px -2px 6px 0px inset;
}
</style>
