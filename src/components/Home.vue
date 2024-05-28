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
              class="text-caption"
            >
              <template v-slot:top>
                <v-toolbar flat>
                  <v-dialog v-model="dialog" max-width="500px">
                    <template v-slot:activator="{ props }">
                      <v-btn class="mb-2" color="primary" dark v-bind="props">
                        New Item
                      </v-btn>
                    </template>
                    <v-card>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12" md="4" sm="6">
                              <v-text-field
                                v-model="editedItem.area"
                                label="Area"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="4" sm="6">
                              <v-text-field
                                v-model="editedItem.unit"
                                label="Unit"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="4" sm="6">
                              <v-text-field
                                v-model="editedItem.importance"
                                label="Importance"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="4" sm="6">
                              <v-text-field
                                v-model="editedItem.satisfaction"
                                label="Satisfaction"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" md="4" sm="6">
                              <v-text-field
                                v-model="editedItem.time"
                                label="Time (hours)"
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
              <template v-slot:item.actions="{ item }">
                <v-icon class="me-2" size="small" @click="editItem(item)">
                  mdi-pencil
                </v-icon>
                <v-icon size="small" @click="deleteItem(item)">
                  mdi-delete
                </v-icon>
              </template>
              <template v-slot:no-data>
                <v-btn color="primary" @click="initialize"> Reset </v-btn>
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
.bubble-chart {
  height: 200px;
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
// https://vuetifyjs.com/en/components/data-tables/basics/#crud-actions
export default {
  name: "App",
  components: { Bubble },
  data() {
    return {
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
      tableHeaders: [
        {
          title: "Area",
          align: "start",
          sortable: false,
          key: "area",
        },
        { title: "Unit", key: "unit" },
        { title: "Importance", key: "importance" },
        { title: "Satisfaction", key: "satisfaction" },
        { title: "Time (hours)", key: "time" },
        { title: "Actions", key: "actions", sortable: false },
      ],
      tableData: [
        {
          areaNum: 1,
          area: "1. Relationships",
          unit: "Significant other",
          description: "Time with partner, dates",
          importance: 1,
          satisfaction: 1,
          time: 1,
        },
        {
          areaNum: 1,
          area: "1. Relationships",
          unit: "Family",
          description: "Engaging with kids, parents, siblings",
          importance: 2,
          satisfaction: 2,
          time: 2,
        },
        {
          areaNum: 1,
          area: "1. Relationships",
          unit: "Family",
          description: "Engaging with kids, parents, siblings",
          importance: 3,
          satisfaction: 3,
          time: 3,
        },
        {
          areaNum: 2,
          area: "2. Body, mind, spirituality",
          unit: "Physical health/sports ",
          description: "Exercise, physical therapy",
          importance: 4,
          satisfaction: 4,
          time: 4,
        },
        {
          areaNum: 2,
          area: "2. Body, mind, spirituality",
          unit: "Mental health/mindfulness ",
          description: "Psychotherapy, meditation",
          importance: 5,
          satisfaction: 5,
          time: 5,
        },
        {
          areaNum: 2,
          area: "2. Body, mind, spirituality",
          unit: "Spirituality/faith",
          description: "Religious practice",
          importance: 6,
          satisfaction: 6,
          time: 6,
        },
        {
          areaNum: 3,
          area: "3. Community and society",
          unit: "Community/citizenship",
          description: "Membership in local clubs, jury duty",
          importance: 7,
          satisfaction: 7,
          time: 7,
        },
        {
          areaNum: 3,
          area: "3. Community and society",
          unit: "Societal engagement",
          description: "Volunteering, activism",
          importance: 8,
          satisfaction: 8,
          time: 8,
        },
        {
          areaNum: 4,
          area: "4. Job, learning, and finances",
          unit: "Job/career",
          description: "Work",
          importance: 9,
          satisfaction: 1,
          time: 9,
        },
        {
          areaNum: 4,
          area: "4. Job, learning, and finances",
          unit: "Education/learning",
          description: "Classes, training",
          importance: 9,
          satisfaction: 2,
          time: 9,
        },
        {
          areaNum: 4,
          area: "4. Job, learning, and finances",
          unit: "Finances",
          description: "Planning, investing",
          importance: 5,
          satisfaction: 2,
          time: 5,
        },
        {
          areaNum: 5,
          area: "5. Interests and entertainment",
          unit: "Hobbies/interests",
          description: "Reading, collectibles",
          importance: 9,
          satisfaction: 3,
          time: 9,
        },
        {
          areaNum: 5,
          area: "5. Interests and entertainment",
          unit: "Online entertainment",
          description: "Social media, TV, gaming",
          importance: 7,
          satisfaction: 1,
          time: 5,
        },
        {
          areaNum: 5,
          area: "5. Interests and entertainment",
          unit: "Offline entertainment",
          description: "Vacations, theater, sporting events",
          importance: 5,
          satisfaction: 1,
          time: 5,
        },
        {
          areaNum: 6,
          area: "6. Personal care",
          unit: "Physiological needs",
          description: "Eating, sleeping",
          importance: 2,
          satisfaction: 5,
          time: 5,
        },
        {
          areaNum: 6,
          area: "6. Personal care",
          unit: "Activities of daily living",
          description: "Commuting, housework",
          importance: 1,
          satisfaction: 3,
          time: 5,
        },
      ], // End of lifeData
      chartLabels: [
        "1. Relationships",
        "2. Body, mind, spirituality",
        "3. Community and society",
        "4. Job, learning, and finances",
        "5. Interests and entertainment",
        "6. Personal care",
      ],
      chartData: {
        datasets: [],
        /* Example of dataset
          {
            label: "1. Relationships",
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
        // boxwidth that is wider
        plugins: {
          legend: {
            position: "right",
            display: true,
            labels: {
              font: {
                size: 10,
              },
              filter: function (legendItem, chartData) {
                const firstIndex = chartData.datasets.findIndex(
                  (d) => d.label[0] === legendItem.text[0]
                );
                if (legendItem.datasetIndex === firstIndex) {
                  return true;
                } else {
                  return false;
                }
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
    this.updateChart();
  },
  methods: {
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
      let datasets = [];
      const colors = [
        "#f96b5c", // Red
        "#7C8CF8", // Blue
        "#F9DC5C", // Yellow
        "#7CF997", // Green
        "#9b42f5", // Purple
        "#808080", // Grey
      ];
      for (let i = 0; i < this.tableData.length; i++) {
        let item = this.tableData[i];
        console.log(item);
        datasets.push({
          label: [item.area, item.unit],
          backgroundColor: colors[item.areaNum - 1],
          data: [
            {
              x: item.satisfaction,
              y: item.importance,
              r: item.time,
            },
          ],
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
  },
};
</script>
