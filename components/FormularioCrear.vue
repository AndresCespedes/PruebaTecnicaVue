<template>
  <v-row justify="center" class="mb-6 mt-1">
    <v-dialog v-model="dialog" width="auto">
      <template v-slot:activator="{ on }">
        <v-btn color="primary" v-bind="on" @click="openDialog()">
          Crear Registro
        </v-btn>
      </template>
      <v-card class="mt-4 mb-4">
        <v-form ref="formularioCrear" v-model="formularioCrear">
          <v-container>
            <v-row>
              <v-col cols="6" md="6">
                <v-text-field
                  v-model="activity_name"
                  :rules="activityNameRules"
                  label="Nombre Actividad"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" md="6">
                <v-text-field
                  v-model="activity_date"
                  :rules="activityDateRules"
                  label="Fecha"
                  type="date"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="6" md="6">
                <v-menu
                  v-model="menuInitialTime"
                  :close-on-content-click="false"
                  offset-y
                >
                  <template v-slot:activator="{ on }">
                    <v-text-field
                      v-model="intitalHourFormatted"
                      :rules="initialHourRules"
                      label="Hora Inicial"
                      readonly
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-time-picker
                    v-model="selectedStartTime"
                    @input="selectedInitiallHour()"
                  >
                  </v-time-picker>
                </v-menu>
              </v-col>

              <v-col cols="6" md="6">
                <v-menu
                  v-model="menuFinalTime"
                  :close-on-content-click="false"
                  offset-y
                >
                  <template v-slot:activator="{ on }">
                    <v-text-field
                      v-model="finalHourFormatted"
                      :rules="finalHourRules"
                      label="Hora Final"
                      readonly
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-time-picker
                    v-model="selectedFinalHours"
                    @input="selectedFinalHour()"
                  >
                  </v-time-picker>
                </v-menu>
              </v-col>

              <v-col cols="6" md="6">
                <v-select
                  v-model="culturalRightSelected"
                  :items="culturalRight"
                  :rules="culturalRightRules"
                  density="derechosculturales"
                  label="Derechos Culturales"
                ></v-select>
              </v-col>

              <v-col cols="6" md="6">
                <v-select
                  v-model="nacSelected"
                  :items="nac"
                  density="nac"
                  label="Nac"
                ></v-select>
              </v-col>

              <v-col cols="6" md="6">
                <v-select
                  v-model="experticiaSelected"
                  :rules="experticiaRules"
                  :items="experticia"
                  density="experticia"
                  label="Experticia"
                ></v-select>
              </v-col>

              <v-col cols="6" md="4">
                <div>
                  <v-btn
                    color="success"
                    :disabled="!formularioCrear"
                    @click="saveForm()"
                    >Guardar</v-btn
                  >
                  <v-alert
                    v-if="showErrorMessage"
                    type="error"
                    dismissible
                    @input="dismissAlert"
                    >{{ errorMessage }}</v-alert
                  >
                </div>
              </v-col>
            </v-row>
          </v-container>
        </v-form>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import moment from "moment";

export default {
  name: "FormularioCrear",
  methods: {
    openDialog() {
      this.dialog = true;
    },
    selectedInitiallHour() {
      this.intitalHourFormatted = moment(
        this.selectedStartTime,
        "HH:mm"
      ).format("h:mm A");

      this.menuInitialTime = false;
    },
    selectedFinalHour() {
      this.finalHourFormatted = moment(this.selectedFinalHours, "HH:mm").format(
        "h:mm A"
      );

      this.menuFinalTime = false;
    },
    saveForm() {
      if (
        moment(this.intitalHourFormatted, "h:mm A") >
        moment(this.finalHourFormatted, "h:mm A")
      ) {
        this.errorMessage =
          "La hora inicial no pude ser mayor que la hora final";
        this.showErrorMessage = true;
      } else {
        let newData = {
          id: this.lastId++,
          consecutive: `FP${this.fp++}`,
          monitor_id: "KAREM LOPEZ",
          cultural_right_id: this.culturalRightSelected,
          nac_id: this.nacSelected,
          activity_date: this.activity_date,
          start_time: this.intitalHourFormatted,
          final_hour: this.finalHourFormatted,
          expertise_id: this.experticiaSelected,
          fechaCarga: moment().format("YYYY-MM-DD h:mm A"),
          estado: "En revisión",
        };

        this.showErrorMessage = false;
        this.dialog = false;
        this.$emit("agregarDato", newData);
      }
    },
    dismissAlert() {
      this.showErrorMessage = false;
    },
  },
  data() {
    return {
      dialog: false,
      formularioCrear: true,
      firstname: "",
      activity_date: "",
      activityDateRules: [
        (value) => {
          if (value) return true;
          return "El campo Fecha es requerido";
        },
      ],
      activity_name: "",
      activityNameRules: [
        (value) => {
          if (value) return true;
          return "El campo Nombre Actividad es requerido";
        },
      ],
      culturalRightSelected: "",
      culturalRightRules: [
        (value) => {
          if (value) return true;
          return "El campo Derechos Culturales es requerido";
        },
      ],
      nacSelected: "",
      experticiaSelected: "",
      experticiaRules: [
        (value) => {
          if (value) return true;
          return "El campo Experticia es requerido";
        },
      ],
      menuInitialTime: false,
      selectedStartTime: null,
      menuFinalTime: false,
      selectedFinalHours: null,
      finalHourFormatted: "",
      finalHourRules: [
        (value) => {
          if (value) return true;
          return "El campo Hora Final es requerido";
        },
      ],
      intitalHourFormatted: "",
      initialHourRules: [
        (value) => {
          if (value) return true;
          return "El campo Hora Inicial es requerido";
        },
      ],
      lastId: 4,
      fp: 4,
      showErrorMessage: false,
      errorMessage: "",
      culturalRight: [
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
