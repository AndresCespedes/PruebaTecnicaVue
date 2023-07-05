<template>
  <v-container fluid>
    <FormularioCrear @agregarDato="agregarDato($event)" />
    <v-card variant="outlined">
      <v-divider :thickness="14"></v-divider>
      <FiltrosFormulario
        @culturalRights="filtrarCultural($event)"
        @nac="filtrarNac($event)"
        @estado="filtrarEstado($event)"
        @experticia="filtrarExperticia($event)"
        @search="filtrarSearch($event)"
        @excel="exportToExcel($event)"
        @pdf="exportToPDF($event)"
        @filtroFechaIncial="filtrarFechaIncial($event)"
        @filtroFechaFinal="filtrarFechaFinal($event)"
        @filterPerDate="filterPerDate()"
      />
      <v-spacer></v-spacer>
      <v-data-table
        :headers="headers"
        :search="search"
        :items="items"
        item-key="id"
      ></v-data-table>
    </v-card>
  </v-container>
</template>

<script>
import moment from "moment";
import { utils, writeFileXLSX } from "xlsx";
import Vue from "vue";
import Snotify from "vue-snotify";
import pdfMake, { log } from "pdfmake/build/pdfmake";
import pdfFonts from "pdfmake/build/vfs_fonts";
import FormularioCrear from "../components/FormularioCrear";
import FiltrosFormulario from "../components/FiltrosFormulario";

Vue.use(Snotify);
pdfMake.vfs = pdfFonts.pdfMake.vfs;

export default {
  components: {
    FormularioCrear,
    FiltrosFormulario,
  },

  methods: {
    getDateActivity() {
      let date = moment().format("YYYY-MM-DD");
      return date;
    },

    getStartTime() {
      let hour = moment().format("h:mm A");
      return hour;
    },

    finalHour() {
      let finalHour = moment().add(1, "hour").format("h:mm A");
      return finalHour;
    },

    loadDate() {
      let finalHour = moment().add(1, "hour").format("YYYY-MM-DD, h:mm A");
      return finalHour;
    },

    filtrarCultural(param) {
      this.filtros.culturalRights = param;
      this.searchAll();
    },
    filtrarNac(param) {
      this.filtros.nac = param;
      this.searchAll();
    },
    filtrarEstado(param) {
      this.filtros.estado = param;
      this.searchAll();
    },
    filtrarExperticia(param) {
      this.filtros.experticia = param;
      this.searchAll();
    },
    filtrarSearch(param) {
      this.filtros.search = param;
      this.filteredItems();
    },
    filtrarFechaIncial(param) {
      this.filtros.fechaIncial = param;
      console.log(this.filtros.fechaIncial);
    },
    filtrarFechaFinal(param) {
      this.filtros.fechaFinal = param;
      console.log(this.filtros.fechaFinal);
    },
    searchAll() {
      const resultado = this.items.filter((elemento) => {
        console.log(this.filtros.culturalRights);
        console.log(elemento.cultural_right_id);

        const filterCulturalRights = this.filtros.culturalRights
          ? this.filtros.culturalRights === elemento.cultural_right_id
          : true;

        const filterNac = this.filtros.nac
          ? this.filtros.nac.includes(elemento.nac_id)
          : true;

        const filterEstado = this.filtros.estado
          ? this.filtros.estado.includes(elemento.estado)
          : true;

        const filterExperticia = this.filtros.experticia
          ? this.filtros.experticia.includes(elemento.expertise_id)
          : true;

        console.log(filterNac);
        console.log(filterEstado);
        console.log(filterExperticia);

        return (
          filterCulturalRights && filterNac && filterEstado && filterExperticia
        );
      });
      this.items = resultado;
    },

    filteredItems() {
      const searchText = this.filtros.search.toLocaleLowerCase();
      const filteredItem = this.items.filter((item) => {
        let objectReturned = Object.values(item).some((value) =>
          value.toString().toLowerCase().includes(searchText)
        );
        return objectReturned;
      });
      this.items = filteredItem;
    },
    agregarDato(param) {
      this.items.push(param);
      console.log(param);
    },
    exportToExcel(param) {
      let informacionOrdenada = this.items.map((e) => {
        return {
          "#": e.id,
          CONSECUTIVO: e.consecutive,
          NOMBRE: e.monitor_id,
          "Derecho cultural": e.cultural_right_id,
          NAC: e.nac_id,
          Fecha: e.activity_date,
          "Hora Inicio": e.start_time,
          "Hora Final": e.final_hour,
          EXPERTICIA: e.expertise_id,
          "Fecha Carga": this.loadDate(),
          ESTADO: e.estado,
        };
      });
      const ws = utils.json_to_sheet(informacionOrdenada);
      const wb = utils.book_new();
      utils.book_append_sheet(wb, ws, "DatosPlantilla");
      writeFileXLSX(wb, "DatosPlantilla.xlsx");
      this.$snotify.success("Excel Exportado con éxito", "Éxito", {
        timeout: 3000,
        showProgressBar: false,
      });
    },
    exportToPDF(param) {
      const bodyTable = this.items.map((e) => {
        return [
          e.id,
          e.consecutive,
          e.monitor_id,
          e.cultural_right_id,
          e.nac_id,
          e.activity_date,
          e.start_time,
          e.final_hour,
          e.expertise_id,
          this.loadDate(),
          e.estado,
        ];
      });
      const documentDefinition = {
        pageOrientation: "landscape", //Orientación de la pagina -> horizontal
        content: [
          { text: "Tabla de Datos", style: "header" }, //Titulo de tabla
          {
            table: {
              body: [
                [
                  "#",
                  "CONSECUTIVO",
                  "NOMBRE",
                  "Derecho cultural",
                  "NAC",
                  "Fecha",
                  "Hora Inicio",
                  "Hora Final",
                  "EXPERTICIA",
                  "Fecha Carga",
                  "ESTADO",
                ], // Encabezados de la tabla
                ...bodyTable, // Filas de la tabla generadas con map
              ],
            },
            layout: {
              fillColor: function (rowIndex, node, columnIndex) {
                return rowIndex % 2 === 0 ? "#F0F0F0" : null; // Color de fondo alternado para filas
              },
            },
          },
        ],
        styles: {
          header: {
            fontSize: 18,
            bold: true,
            margin: [0, 0, 0, 10],
          },
          tableHeader: {
            bold: true,
            fillColor: "#CCCCCC", // Color de fondo del encabezado de la tabla
          },
        },
      };

      const pdfDocGenerator = pdfMake.createPdf(documentDefinition);
      pdfDocGenerator.download("tabla_datos.pdf");
    },
    filterPerDate() {
      this.datosFiltrados = this.items.filter((e) => {
        // Convierte las fechas a objetos Date
        let fechaInicio = this.filtros.fechaIncial;
        let fechaFin = this.filtros.fechaFinal;
        console.log(fechaInicio);
        console.log(fechaFin);

        // Obtiene la fecha del dato actual y la convierte a objeto Date
        const fechaDato = e.activity_date;
        console.log(fechaDato);

        // Compara las fechas
        return (
          moment(fechaDato, "YYYY-MM-DD") >=
            moment(fechaInicio, "YYYY-MM-DD") &&
          moment(fechaDato, "YYYY-MM-DD") <= moment(fechaFin, "YYYY-MM-DD")
        );
      });
      this.items = this.datosFiltrados;
    },
  },

  data() {
    return {
      search: "",
      headers: [
        { text: "#", value: "id" },
        { text: "CONSECUTIVO", value: "consecutive" },
        { text: "NOMBRE", value: "monitor_id" },
        { text: "DERECHO CULTURAL", value: "cultural_right_id" },
        { text: "NAC", value: "nac_id" },
        { text: "FECHA", value: "activity_date" },
        { text: "HORA INICIO", value: "start_time" },
        { text: "HORA FINAL", value: "final_hour" },
        { text: "EXPERTICIA", value: "expertise_id" },
        { text: "FECHA CARGA", value: "fechaCarga" },
        { text: "ESTADO", value: "estado" },
      ],
      items: [
        {
          id: 1,
          consecutive: "FP1",
          monitor_id: "KAREM LOPEZ",
          cultural_right_id: "Cooperación cultural",
          nac_id: "ALCALÁ",
          activity_date: "2023-06-26",
          start_time: this.getStartTime(),
          final_hour: this.finalHour(),
          expertise_id: "Danza",
          fechaCarga: this.loadDate(),
          estado: "En revisión",
        },
        {
          id: 2,
          consecutive: "FP2",
          monitor_id: "KAREM LOPEZ",
          cultural_right_id: "Acceso y participación en la vida",
          nac_id: "Ulloa",
          activity_date: "2023-06-27",
          start_time: this.getStartTime(),
          final_hour: this.finalHour(),
          expertise_id: "Promoción de lectura",
          fechaCarga: this.loadDate(),
          estado: "En revisión",
        },
        {
          id: 3,
          consecutive: "FP3",
          monitor_id: "KAREM LOPEZ",
          cultural_right_id: "Información y comunicación",
          nac_id: "ALCALÁ",
          activity_date: "2023-06-28",
          start_time: this.getStartTime(),
          final_hour: this.finalHour(),
          expertise_id: "Artes plásticas",
          fechaCarga: this.loadDate(),
          estado: "Aprobado",
        },
      ],

      filtros: {
        culturalRights: "",
        nac: "",
        estado: "",
        experticia: "",
        search: "",
        datosFiltrados: "",
        fechaIncial: "",
        fechaFinal: "",
      },
    };
  },
};
</script>
