<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto" max-width="1200">
      <v-divider :thickness="1" class="my-4"></v-divider>
      <div class="text-center">
        <h1 class="text-h2 font-weight-bold">Strategic Life Portfolio</h1>
      </div>
      <v-divider :thickness="1" class="my-4"></v-divider>
      <v-row class="mt-5" justify="center">
        <h3>Visualize your life portfolio in the 2x2 Life Portfolio</h3>
      </v-row>
      <v-row class="my-4" justify="center">
        <v-col cols="8">
          <Bubble
            id="bubbleChart"
            :data="chartData"
            :labels="chartLabels"
            :options="chartOptions"
          />
        </v-col>
        <v-col cols="1">
          <v-btn class="my-2" variant="tonal" @click="shareClick">
            Share
          </v-btn>
        </v-col>
      </v-row>
      <v-row justify="center">
        <v-col cols="10">
          <v-card
            class="py-4"
            prepend-icon="mdi-text-box-outline"
            rounded="lg"
            subtitle="Enter your life portfolio in this table"
            title="Your Life Porfolio"
            variant="text"
          >
            <v-data-table
              :headers="tableHeaders"
              :items="tableData"
              :sort-by="[{ key: 'area', order: 'asc' }]"
              class="text-caption custom-font"
              items-per-page="50"
            >
              <template v-slot:top>
                <v-toolbar flat>
                  <v-dialog v-model="dialog" max-width="800">
                    <template v-slot:activator="{ props }">
                      <v-btn
                        class="mb-2 mx-5"
                        color="primary"
                        variant="outlined"
                        dark
                        v-bind="props"
                      >
                        New Item
                      </v-btn>
                      <v-btn
                        class="mb-2 mx-5"
                        color="primary"
                        variant="outlined"
                        dark
                        @click="resetData"
                      >
                        Reset
                      </v-btn>
                      <v-btn
                        class="mb-2 mx-5"
                        color="primary"
                        variant="outlined"
                        dark
                        @click="importCsv"
                      >
                        Import CSV
                      </v-btn>
                      <input
                        ref="uploader"
                        class="d-none"
                        type="file"
                        @change="onImportCsvChanged"
                      />
                      <v-btn
                        class="mb-2 mx-5"
                        color="primary"
                        variant="outlined"
                        dark
                        @click="downloadCsv"
                      >
                        Download CSV
                      </v-btn>
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
              </template>
              <template v-slot:item="{ item }">
                <!-- write tr with background inline colour of orange, don't call getRowClass function-->
                <tr :style="{ backgroundColor: getRowColor(item.area) }">
                  <td class="font-weight-bold">
                    {{ item.area }}
                  </td>
                  <td class="font-weight-bold">
                    {{ item.unit }}
                  </td>
                  <td>
                    {{ item.importance }}
                  </td>
                  <td>
                    {{ item.satisfaction }}
                  </td>
                  <td>
                    {{ item.time }}
                  </td>
                  <td>
                    <v-icon class="me-2" size="small" @click="editItem(item)">
                      mdi-pencil
                    </v-icon>
                    <v-icon size="small" @click="deleteItem(item)">
                      mdi-delete
                    </v-icon>
                  </td>
                </tr>
              </template>
            </v-data-table>
          </v-card>
        </v-col>
      </v-row>

      <v-divider :thickness="1" class="my-4"></v-divider>

      <div class="text-center font-italic">
        Read More:
        <a
          href="https://hbr.org/2023/12/use-strategic-thinking-to-create-the-life-you-want"
        >
          https://hbr.org/2023/12/use-strategic-thinking-to-create-the-life-you-want
        </a>
      </div>
      <v-divider :thickness="1" class="my-4"></v-divider>
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
          area: "Relationships",
          unit: "Significant other",
          description: "Time with partner, dates",
          importance: 1,
          satisfaction: 1,
          time: 1,
        },
        {
          area: "Relationships",
          unit: "Family",
          description: "Engaging with kids, parents, siblings",
          importance: 2,
          satisfaction: 2,
          time: 2,
        },
        {
          area: "Body, mind, spirituality",
          unit: "Physical health/sports ",
          description: "Exercise, physical therapy",
          importance: 4,
          satisfaction: 4,
          time: 4,
        },
        {
          area: "Body, mind, spirituality",
          unit: "Mental health/mindfulness ",
          description: "Psychotherapy, meditation",
          importance: 5,
          satisfaction: 5,
          time: 5,
        },
        {
          area: "Body, mind, spirituality",
          unit: "Spirituality/faith",
          description: "Religious practice",
          importance: 6,
          satisfaction: 6,
          time: 6,
        },
        {
          area: "Community and society",
          unit: "Community/citizenship",
          description: "Membership in local clubs, jury duty",
          importance: 7,
          satisfaction: 7,
          time: 7,
        },
        {
          area: "Community and society",
          unit: "Societal engagement",
          description: "Volunteering, activism",
          importance: 8,
          satisfaction: 8,
          time: 8,
        },
        {
          area: "Job, learning, and finances",
          unit: "Job/career",
          description: "Work",
          importance: 9,
          satisfaction: 1,
          time: 9,
        },
        {
          area: "Job, learning, and finances",
          unit: "Education/learning",
          description: "Classes, training",
          importance: 9,
          satisfaction: 2,
          time: 9,
        },
        {
          area: "Job, learning, and finances",
          unit: "Finances",
          description: "Planning, investing",
          importance: 5,
          satisfaction: 2,
          time: 5,
        },
        {
          area: "Interests and entertainment",
          unit: "Hobbies/interests",
          description: "Reading, collectibles",
          importance: 9,
          satisfaction: 3,
          time: 9,
        },
        {
          area: "Interests and entertainment",
          unit: "Online entertainment",
          description: "Social media, TV, gaming",
          importance: 7,
          satisfaction: 1,
          time: 5,
        },
        {
          area: "Interests and entertainment",
          unit: "Offline entertainment",
          description: "Vacations, theater, sporting events",
          importance: 5,
          satisfaction: 1,
          time: 5,
        },
        {
          area: "Personal care",
          unit: "Physiological needs",
          description: "Eating, sleeping",
          importance: 2,
          satisfaction: 5,
          time: 5,
        },
        {
          area: "Personal care",
          unit: "Activities of daily living",
          description: "Commuting, housework",
          importance: 1,
          satisfaction: 3,
          time: 5,
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
          label: "Relationships",
          color: [219, 0, 0],
        },
        {
          label: "Body, mind, spirituality",
          color: [0, 118, 197],
        },
        {
          label: "Community and society",
          color: [255, 188, 0],
        },
        {
          label: "Job, learning, and finances",
          color: [0, 159, 40],
        },
        {
          label: "Interests and entertainment",
          color: [156, 0, 206],
        },
        {
          label: "Personal care",
          color: [65, 76, 79],
        },
      ],
      chartData: {
        datasets: [],
        /* Example of dataset
          {
            label: "Relationships",
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
        // Custom legend with distinct items only, no need to repeat
        responsive: true,
        maintainAspectRatio: true,
        // Styling
        borderColor: "#80fffff",
        borderWidth: 0.25,
        // write x and y axex labels as satisfaction and importance with min -1 and max as 11
        scales: {
          x: {
            title: {
              display: true,
              text: "Satisfaction",
            },
            min: -1,
            max: 11,
            // Add ticks from 0 to 10 with step size of 1 only
            ticks: {
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
            },
            min: -1,
            max: 11,
            ticks: {
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
          customCanvasBackgroundColor: {
            color: "white",
          },
          legend: {
            position: "bottom",
            display: true,
            onClick: (e) => {},
            labels: {
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
                let hours = context.raw.r / 4;
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
      }, // End of graphOptions
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
            // check if the data is valid and in range of 0 - 10 for importance and satisfaction, if not set as 1
            if (x.importance < 0) x.importance = 0;
            else if (x.importance > 10) x.importance = 10;
            if (x.satisfaction < 0) x.satisfaction = 0;
            else if (x.satisfaction > 10) x.satisfaction = 10;            
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
              r: item.time * 4,
            },
          ],
          backgroundColor: this.getRowColorString(item.area, 0.6),
        });
      }
      this.chartData = {
        datasets: datasets,
      };
    },
    // on click this function, image will be downloaded at client side in chartjs
    shareClick() {
      const canvas = document.getElementById("bubbleChart");
      const dataURL = canvas.toDataURL("image/png", 1.0);
      const link = document.createElement("a");
      link.download = "chart.png";
      link.href = dataURL;
      link.click();
    },
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
