<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto">
      <v-row class="text-center">
        <v-col>
          <h1 class="text-h4 text-sm-h2 font-weight-bold mt-5 mb-4">
            Strategic Life Portfolio
          </h1>
          <v-divider :thickness="1" class="my-1"></v-divider>
          <p class="mx-0 mx-sm-10 text-caption text-sm-body-1">
            ✍️ Assess your life portfolio for your 168-hour week across six
            Strategic Life Areas (SLAs) and various Strategic Life Units (SLUs).
            <br />
            ⭐️ Reflect on your life strategy and consider adjustments for best
            use of your own time, energy, and money investments! 😊⭐️ <br />
            <a
              href="https://hbr.org/2023/12/use-strategic-thinking-to-create-the-life-you-want"
            >
              Read More
            </a>
          </p>
          <v-divider :thickness="1" class="my-1"></v-divider>
        </v-col>
      </v-row>
      <v-row justify="center" align="center">
        <v-col cols="12" sm="8">
          <v-card class="" rounded="lg" variant="text">
            <v-card-title class="text-center font-weight-bold">
              <v-icon icon="mdi-chart-scatter-plot-hexbin"></v-icon>
              Your Life Porfolio
            </v-card-title>
            <Bubble
              class="my-0"
              id="bubbleChart"
              :data="chartData"
              :labels="chartLabels"
              :options="chartOptions"
            />
          </v-card>
        </v-col>
        <v-col cols="12" sm="2" class="text-center">
          <p class="text-body-2 font-italic font-weight-light">
            Radius: <br />Time (hours/week)
          </p>
        </v-col>
      </v-row>
      <v-row class="my-1" justify="center">
        <v-col cols="12">
          <v-card class="" rounded="lg" variant="text">
            <v-card-title
              class="text-body-1 text-sm-h6 text-center font-weight-bold"
            >
              <v-icon icon="mdi-text-box-outline"></v-icon>
              Enter Your Life Porfolio
            </v-card-title>
            <v-data-table
              :headers="tableHeaders"
              :items="tableData"
              :sort-by="[{ key: 'area', order: 'desc' }]"
              class="text-caption custom-font"
              items-per-page="50"
            >
              <template v-slot:top>
                <v-toolbar class="fill-width" flatn prominent>
                  <v-dialog v-model="dialog" width="800">
                    <template v-slot:activator="{ props }">
                      <div>
                        <v-btn
                          class="font-weight-bold mx-1 mx-sm-4"
                          color="primary"
                          variant="outlined"
                          dark
                          v-bind="props"
                        >
                          <!-- New Item -->
                          <v-icon icon="mdi-plus"></v-icon>
                        </v-btn>
                        <v-btn
                          class="font-weight-bold mx-1 mx-sm-4"
                          color="primary"
                          variant="outlined"
                          dark
                          @click="resetData"
                        >
                          <!-- Reset -->
                          <v-icon icon="mdi-reload"></v-icon>
                        </v-btn>
                        <v-btn
                          class="font-weight-bold mx-1 mx-sm-4"
                          color="primary"
                          variant="outlined"
                          dark
                          @click="importCsv"
                        >
                          <!-- Import CSV -->
                          <v-icon icon="mdi-import"></v-icon>
                        </v-btn>
                        <input
                          ref="uploader"
                          class="d-none"
                          type="file"
                          @change="onImportCsvChanged"
                        />
                        <v-btn
                          class="font-weight-bold mx-1 mx-sm-4"
                          color="primary"
                          variant="outlined"
                          dark
                          @click="downloadCsv"
                        >
                          <!-- Download CSV -->
                          <v-icon icon="mdi-download"></v-icon>
                        </v-btn>
                      </div>
                    </template>
                    <v-card>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12" md="6" sm="6">
                              <v-select
                                label="Area"
                                v-model="editedItem.area"
                                :items="chartLabels.map((x) => x.label)"
                              ></v-select>
                            </v-col>
                            <v-col cols="12" md="6" sm="6">
                              <v-text-field
                                v-model="editedItem.unit"
                                label="Unit"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="4" sm="4">
                              <v-select
                                v-model="editedItem.importance"
                                label="Importance"
                                :items="
                                  Array.from(
                                    { length: 11 },
                                    (_, index) => index
                                  )
                                "
                              ></v-select>
                            </v-col>
                            <v-col cols="12" md="4" sm="4">
                              <v-select
                                v-model="editedItem.satisfaction"
                                label="Satisfaction"
                                :items="
                                  Array.from(
                                    { length: 11 },
                                    (_, index) => index
                                  )
                                "
                              ></v-select>
                            </v-col>
                            <v-col cols="12" md="4" sm="4">
                              <v-text-field
                                v-model="editedItem.time"
                                label="Time (hours/week)"
                              ></v-text-field>
                            </v-col>
                          </v-row>
                        </v-container>
                      </v-card-text>

                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                          color="blue-darken-1"
                          variant="text"
                          @click="close"
                        >
                          Cancel
                        </v-btn>
                        <v-btn
                          color="blue-darken-1"
                          variant="text"
                          @click="save"
                        >
                          Save
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                  <v-dialog v-model="dialogDelete" max-width="500px">
                    <v-card>
                      <v-card-title class="text-h5"
                        >Are you sure you want to delete this
                        item?</v-card-title
                      >
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                          color="blue-darken-1"
                          variant="text"
                          @click="closeDelete"
                          >Cancel</v-btn
                        >
                        <v-btn
                          color="blue-darken-1"
                          variant="text"
                          @click="deleteItemConfirm"
                          >OK</v-btn
                        >
                        <v-spacer></v-spacer>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-toolbar>
                <div
                  class="text-body-1 text-center font-weight-bold"
                  :class="totalHours > 168 || totalHours < 0 ? 'text-red' : ''"
                >
                  Total hours for your week: {{ totalHours }} / 168
                  <div v-if="invalidTotalHours()">
                    (Your total hours per week is invalid, please correct the
                    numbers!)
                  </div>
                </div>
              </template>
              <template v-slot:item.area="{ item }">
                <v-chip
                  class="font-weight-bold tonal"
                  :style="{ backgroundColor: getRowColor(item.area) }"
                >
                  {{ item.area }}
                </v-chip>
              </template>
              <template v-slot:item.unit="{ item }">
                <div class="font-weight-bold">{{ item.unit }}</div>
              </template>
              <template v-slot:item.actions="{ item }">
                <v-icon class="me-2" size="small" @click="editItem(item)">
                  mdi-pencil
                </v-icon>
                <v-icon size="small" @click="deleteItem(item)">
                  mdi-delete
                </v-icon>
              </template>
            </v-data-table>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Noticia+Text:wght@100;300;400;500;700;900&display=swap");
.bubble-chart {
  height: 200px;
}
h1 {
  font-family: "Noticia Text", sans-serif;
}
.custom-font {
  font-family: "Noticia Text", sans-serif;
}
</style>

<script setup lang="ts">
import {
  Chart as ChartJS,
  Tooltip,
  Legend,
  PointElement,
  LinearScale,
} from "chart.js";
ChartJS.register(LinearScale, PointElement, Tooltip, Legend);
</script>

<script lang="ts">
import { Bubble } from "vue-chartjs";
import Papa from "papaparse";

// References: https://vuetifyjs.com/en/components/data-tables/basics/#crud-actions
export default {
  name: "App",
  components: { Bubble },
  data() {
    return {
      // Table Data Handling
      tableHeaders: [
        { title: "Area", key: "area", align: "start" },
        { title: "Unit", key: "unit" },
        { title: "Importance (0-10)", key: "importance" },
        { title: "Satisfaction (0-10)", key: "satisfaction" },
        { title: "Time (hours/week)", key: "time" },
        { title: "Actions", key: "actions", sortable: false },
      ],
      tableData: [],
      tableDataOriginal: [
        {
          area: "Relationships 🤗",
          unit: "Significant other 💑",
          description: "Time with partner, dates",
          importance: 8,
          satisfaction: 3,
          time: 2,
        },
        {
          area: "Relationships 🤗",
          unit: "Family 👪",
          description: "Engaging with kids, parents, siblings",
          importance: 8,
          satisfaction: 8,
          time: 6,
        },
        {
          area: "Relationships 🤗",
          unit: "Friendship 🤝",
          description: "Time with friends",
          importance: 7,
          satisfaction: 8,
          time: 10,
        },
        {
          area: "Body/Mind/Spirituality 🧘‍♀️",
          unit: "Physical health/sports 🏃‍♀️",
          description: "Exercise, physical therapy",
          importance: 6,
          satisfaction: 7,
          time: 6,
        },
        {
          area: "Body/Mind/Spirituality 🧘‍♀️",
          unit: "Mental health/mindfulness 🧠",
          description: "Psychotherapy, meditation",
          importance: 8,
          satisfaction: 2,
          time: 4,
        },
        {
          area: "Body/Mind/Spirituality 🧘‍♀️",
          unit: "Spirituality/faith ✨",
          description: "Religious practice",
          importance: 4,
          satisfaction: 8,
          time: 4,
        },
        {
          area: "Community and Society 🌍",
          unit: "Community/citizenship 🌳",
          description: "Membership in local clubs, jury duty",
          importance: 2,
          satisfaction: 3,
          time: 1,
        },
        {
          area: "Community and Society 🌍",
          unit: "Societal engagement 🗳️",
          description: "Volunteering, activism",
          importance: 6,
          satisfaction: 4,
          time: 3,
        },
        {
          area: "Job/Learning/Finances 💼",
          unit: "Job/career 💼",
          description: "Work",
          importance: 7,
          satisfaction: 5,
          time: 40,
        },
        {
          area: "Job/Learning/Finances 💼",
          unit: "Education/learning 📚",
          description: "Classes, training",
          importance: 6,
          satisfaction: 3,
          time: 10,
        },
        {
          area: "Job/Learning/Finances 💼",
          unit: "Finances 💰",
          description: "Planning, investing",
          importance: 6,
          satisfaction: 6,
          time: 3,
        },
        {
          area: "Interests and Entertainment 🎨",
          unit: "Hobbies/interests 🎨",
          description: "Reading, collectibles",
          importance: 9,
          satisfaction: 3,
          time: 9,
        },
        {
          area: "Interests and Entertainment 🎨",
          unit: "Online entertainment 📱",
          description: "Social media, TV, gaming",
          importance: 3,
          satisfaction: 6,
          time: 7,
        },
        {
          area: "Interests and Entertainment 🎨",
          unit: "Offline entertainment 🎮",
          description: "Vacations, theater, sporting events",
          importance: 2,
          satisfaction: 4,
          time: 3,
        },
        {
          area: "Personal Care 💆‍♂️",
          unit: "Physiological needs 🍽️",
          description: "Eating, sleeping",
          importance: 7,
          satisfaction: 7,
          time: 40,
        },
        {
          area: "Personal Care 💆‍♂️",
          unit: "Activities of daily living 🛀",
          description: "Commuting, housework",
          importance: 3,
          satisfaction: 7,
          time: 20,
        },
      ], // End of lifeData
      // Edit Dialog Handling
      dialog: false,
      dialogDelete: false,
      editedIndex: -1,
      editedItem: {
        area: "",
        unit: "",
        importance: 0,
        satisfaction: 0,
        time: 0,
      },
      defaultItem: {
        area: "",
        unit: "",
        importance: 0,
        satisfaction: 0,
        time: 0,
      },
      // Import CSV Variables
      isSelectingFile: false,
      selectedFile: null,
      // Chart variables
      chartLabels: [
        {
          label: "Relationships 🤗",
          color: [219, 0, 0],
        },
        {
          label: "Body/Mind/Spirituality 🧘‍♀️",
          color: [0, 118, 197],
        },
        {
          label: "Community and Society 🌍",
          color: [255, 188, 0],
        },
        {
          label: "Job/Learning/Finances 💼",
          color: [0, 159, 40],
        },
        {
          label: "Interests and Entertainment 🎨",
          color: [156, 0, 206],
        },
        {
          label: "Personal Care 💆‍♂️",
          color: [65, 76, 79],
        },
      ],
      chartData: {
        datasets: [],
        /* Example of dataset
          {
            label: "Relationships 🤗",
            backgroundColor: "#f87979",
            data: [
              {
                x: 20,
                y: 25,
                r: 5,
              },
            ],
          },
          */
      }, // End of graph data
      chartOptions: {
        responsive: true,
        maintainAspectRatio: true,
        aspectRatio: 1,
        borderColor: "#80fffff",
        borderWidth: 0.25,
        scales: {
          x: {
            title: {
              display: true,
              text: "Satisfaction",
              font: {
                family: "Noticia Text",
                size: 16,
              },
            },
            min: 0,
            max: 10,
            // Add ticks from 0 to 10 with step size of 1 only
            ticks: {
              font: {
                family: "Noticia Text",
              },
              callback: function (value, index, values) {
                if (value >= 0 && value <= 10) {
                  return value;
                }
              },
              stepSize: 1,
            },
          },
          y: {
            title: {
              display: true,
              text: "Importance",
              font: {
                family: "Noticia Text",
                size: 16,
              },
            },
            min: 0,
            max: 10,
            ticks: {
              font: {
                family: "Noticia Text",
              },
              callback: function (value, index, values) {
                if (value >= 0 && value <= 10) {
                  return value;
                }
              },
              stepSize: 1,
            },
          },
        },
        plugins: {
          legend: {
            position: "bottom",
            display: true,
            onClick: (e) => {},
            labels: {
              font: {
                family: "Noticia Text",
              },
              generateLabels: (chart) => {
                return this.chartLabels.map((x, i) => ({
                  text: `${x.label}`,
                  fillStyle: this.getRowColorString(x.label, 0.6),
                  index: i,
                }));
              },
            },
          },
          tooltip: {
            enabled: true,
            callbacks: {
              label: function (context) {
                let label = context.dataset.label || "";
                let hours = context.raw.r / 2;
                return [
                  `Area: ${label[0]}`,
                  `Unit: ${label[1]}`,
                  `Importantce: ${context.raw.y}`,
                  `Satisfaction: ${context.raw.x}`,
                  `Time: ${hours} hours/week`,
                ];
              },
            },
          },
        },
      }, // End of chartOptions
      totalHours: 0,
    };
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    this.resetData();
    // Set edit item defaul titem
    this.defaultItem = {
      area: this.tableDataOriginal[0].area,
      unit: this.tableDataOriginal[0].unit,
      importance: 1,
      satisfaction: 1,
      time: 1,
    };
    this.editedItem = Object.assign({}, this.defaultItem);
  },
  methods: {
    calculateTotalHours() {
      this.totalHours = this.tableData.reduce(
        (acc, item) => acc + item.time,
        0
      );
    },
    invalidTotalHours() {
      return this.totalHours > 168 || this.totalHours < 0;
    },
    resetData() {
      this.tableData = this.tableDataOriginal.map((x) => x);
      this.updateChart();
    },
    onImportCsvChanged(e) {
      this.selectedFile = e.target.files[0];

      Papa.parse(this.selectedFile, {
        header: true,
        transformHeader: (header) =>
          header
            .trim()
            .toLowerCase()
            .replace(" (hours/week)", "")
            .replace(" (0-10)", ""),
        complete: (results) => {
          console.log(results);
          this.tableData = results.data.map((x) => {
            // parseInt
            x.importance = parseInt(x.importance) || 1;
            x.satisfaction = parseInt(x.satisfaction) || 1;
            x.time = parseInt(x.time) || 1;
            // check if the data is valid and in range of 0 - 10 for importance and satisfaction, if not set as 1
            if (x.importance < 0) x.importance = 0;
            else if (x.importance > 10) x.importance = 10;
            if (x.satisfaction < 0) x.satisfaction = 0;
            else if (x.satisfaction > 10) x.satisfaction = 10;
            if (x.time < 0) x.time = 0;
            return x;
          });
          this.updateChart();
        },
      });
    },
    importCsv() {
      this.isSelecting = true;
      // After obtaining the focus when closing the FilePicker, return the button state to normal
      window.addEventListener(
        "focus",
        () => {
          this.isSelecting = false;
        },
        { once: true }
      );
      // Trigger click on the FileInput
      this.$refs.uploader.click();
    },
    downloadCsv() {
      // write code to generate csv template from this.tableHeader and this.tableData and download it in client side, string comma handled
      const csvContent =
        "data:text/csv;charset=utf-8," +
        this.tableHeaders
          .slice(0, -1)
          .map((header) => `"${header.title}"`)
          .join(",") +
        "\n" +
        this.tableData
          .map(
            (item) =>
              `"${item.area}","${item.unit}",${item.importance},${item.satisfaction},${item.time}`
          )
          .join("\n");
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "strategic_life_portfolio_template.csv");
      document.body.appendChild(link);
      link.click();
    },
    editItem(item) {
      this.editedIndex = this.tableData.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = this.tableData.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.tableData.splice(this.editedIndex, 1);
      this.closeDelete();
      this.updateChart();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      this.editedItem.time = parseInt(this.editedItem.time);
      if (this.editedIndex > -1) {
        Object.assign(this.tableData[this.editedIndex], this.editedItem);
      } else {
        this.tableData.push(this.editedItem);
      }
      this.close();
      this.updateChart();
    },
    updateChart() {
      this.chartData.datasets = [];
      const datasets = [] as {
        label: string[];
        data: { x: number; y: number; r: number }[];
        backgroundColor: string;
      }[];
      for (let i = 0; i < this.tableData.length; i++) {
        let item = this.tableData[i];
        datasets.push({
          label: [item.area, item.unit],
          data: [
            {
              x: item.satisfaction,
              y: item.importance,
              r: item.time * 2,
            },
          ],
          backgroundColor: this.getRowColorString(item.area, 0.6),
        });
      }
      this.chartData = {
        datasets: datasets,
      };
      // Also calculate total hours again
      this.calculateTotalHours();
    },
    // on click this function, image will be downloaded at client side in chartjs
    /* shareClick() {
      const canvas = document.getElementById("bubbleChart");
      const dataURL = canvas.toDataURL("image/png", 1.0);
      const link = document.createElement("a");
      link.download = "chart.png";
      link.href = dataURL;
      link.click();
    }, */
    getRowColor(area) {
      return this.getRowColorString(area, 0.3);
    },
    getRowColorString(area, opacity) {
      const colorCode = this.chartLabels.find((x) => x.label === area).color;
      return `rgba(${colorCode},${opacity})`;
    },
  },
};
</script>
