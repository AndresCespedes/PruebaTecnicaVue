<template class="mt-4 mb-4">
  <div class="ml-4 mr-4">
    <v-row>
      <v-col cols="6" md="2" sm="2">
        <v-text-field
          v-model="search"
          @change="filtrarSearch()"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-col>

      <v-col cols="6" md="2" sm="2">
        <v-select
          v-model="culturalRightSelected"
          :items="culturalRights"
          @change="searchCultural()"
          label="Derechos Culturales"
        ></v-select>
      </v-col>

      <v-col cols="6" md="2" sm="2">
        <v-select
          v-model="nacSelected"
          :items="nac"
          @change="searchNac()"
          label="Nac"
        ></v-select>
      </v-col>

      <v-col cols="6" md="2" sm="2">
        <v-select
          v-model="estadoSelected"
          :items="estado"
          @change="searchEstado()"
          label="Estado"
        ></v-select>
      </v-col>

      <v-col cols="6" md="2" sm="2">
        <v-select
          v-model="experticiaSelected"
          :items="experticia"
          @change="searchExperticia()"
          label="Experticia"
        ></v-select>
      </v-col>

      <v-row justify="center">
        <v-col cols="6" md="5" sm="5" lg="4">
          <v-text-field
            v-model="fechaInicialFiltro"
            label="Fecha Inicial"
            @change="filtrarFechaInicial()"
            type="date"
            required
          ></v-text-field>
        </v-col>

        <v-col cols="6" md="5" sm="5" lg="4">
          <v-text-field
            v-model="fechaFinalFiltro"
            label="Fecha Final"
            @change="filtrarFechaFinal()"
            type="date"
            required
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row class="mt-1 mb-6" justify="center">
        <v-col cols="6" md="3" sm="4" lg="4" xs="3">
          <v-btn color="success" @click="filterPerDate()"
            >Filtrar por fecha</v-btn
          >
        </v-col>
        <v-col cols="6" md="3" sm="4" lg="4" xs="3">
          <v-btn
            v-model="exportarExcel"
            color="success"
            @click="exportarXlsx()"
          >
            <v-icon left>mdi-file-excel</v-icon>
            Exportar a xlsx
          </v-btn>
        </v-col>

        <v-col cols="6" md="3" sm="4" lg="4" xs="3">
          <v-btn v-model="exportarPDf" color="error" @click="exportarPdf()">
            <v-icon right>mdi-file-pdf-outline</v-icon>
            Exportar a PDF
          </v-btn>
        </v-col>
      </v-row>
    </v-row>
  </div>
</template>

<script>
export default {
  name: "FiltrosFormulario",
  methods: {
    searchCultural() {
      this.$emit("culturalRights", this.culturalRightSelected);
    },

    searchNac() {
      this.$emit("nac", this.nacSelected);
    },

    searchEstado() {
      this.$emit("estado", this.estadoSelected);
    },

    searchExperticia() {
      this.$emit("experticia", this.experticiaSelected);
    },
    filtrarSearch() {
      this.$emit("search", this.search);
    },
    exportarXlsx() {
      this.$emit("excel", this.exportarExcel);
    },
    exportarPdf() {
      this.$emit("pdf", this.exportarPDf);
    },
    filtrarFechaInicial() {
      this.$emit("filtroFechaIncial", this.fechaInicialFiltro);
    },
    filtrarFechaFinal() {
      this.$emit("filtroFechaFinal", this.fechaFinalFiltro);
    },
    filterPerDate() {
      this.$emit("filterPerDate", true);
    },
  },
  data() {
    return {
      culturalRightSelected: "",
      nacSelected: "",
      estadoSelected: "",
      experticiaSelected: "",
      search: "",
      fechaInicialFiltro: "",
      fechaFinalFiltro: "",
      filtroFecha: "",
      exportarPDf: null,
      exportarExcel: null,
      culturalRights: [
        "Identidad y patrimonios culturales",
        "Referencias a comunidades culturales",
        "Acceso y participación en la vida cultural",
        "Educación y formación",
        "Información y comunicación",
        "Cooperación cultural",
      ],

      nac: [
        "ALCALÁ",
        "ANDALUCÍA",
        "ANSERMANUEVO",
        "ARGELIA",
        "BOLÍVAR",
        "BUENAVENTURA",
        "BUGA",
      ],

      estado: ["Aprobado", "Rechazado", "En revisión"],

      experticia: [
        "Artes plásticas",
        "Teatro",
        "Música",
        "Danza",
        "Cocina tradicional.",
        "Juegos tradicionales.",
        "Promoción de lectura.",
      ],
    };
  },
};
</script>
