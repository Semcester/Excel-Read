<template>
  <div>
    <v-btn
      class="ma-2 d-flex align-center"
      color="info"
      @click="goBack"
      v-if="data.length > 0"
    >
      <v-icon start icon="mdi-arrow-left"></v-icon>
      Yeni Dosya Yükle
    </v-btn>

    <div v-if="data.length <= 0" class="d-flex flex-column align-center mt-16">
      <h3>Download Shit</h3>
      <v-file-input
        class="w-25"
        show-size
        accept=".xlsx"
        label="Import Excel File"
        variant="underlined"
        @change="handleFileUpload"
      ></v-file-input>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      data: [],
    };
  },

  methods: {
    goBack() {
      this.$emit("excelData", (this.data = []));
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = (e) => {
        const data = e.target.result;
        const workbook = XLSX.read(data, { type: "binary" });

        const worksheetName = workbook.SheetNames[1];
        const worksheet = workbook.Sheets[worksheetName];

        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        localStorage.setItem("myData", JSON.stringify(jsonData));
        console.log("json data", jsonData);

        this.data = jsonData.map((item) => {
          return {
            MPN: item.MPN,
            BASLIK: item.BAŞLIK,
            DETAY: item.DETAY,
            ARACMODEL: item.ARACMODEL,
            images: [
              { url: item.IMG1, originalWidth: null, originalHeight: null },
              { url: item.IMG2, originalWidth: null, originalHeight: null },
              { url: item.IMG3, originalWidth: null, originalHeight: null },
              { url: item.IMG4, originalWidth: null, originalHeight: null },
              { url: item.IMG5, originalWidth: null, originalHeight: null },
            ],
            STATUS: item.STATUS,
          };
        });
        this.$emit("excelData", this.data);
      };

      reader.readAsBinaryString(file);
    },
  },
};
</script>
