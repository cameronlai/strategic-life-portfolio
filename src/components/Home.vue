<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto" max-width="1200">
      <v-divider :thickness="1" class="my-4"></v-divider>
      <div class="text-center">
        <h1 class="text-h2 font-weight-bold">Strategic Life Portfolio</h1>
      </div>
      <v-divider :thickness="1" class="my-4"></v-divider>
      <v-row justify="center">
      <br>
      Visualize your life portfolio in the 2x2 Life Portfolio
      </v-row>

      <v-row justify="center">
        <v-col cols="6">
          <Bubble
            :data="chartData"
            :labels="chartLabels"
            :options="chartOptions"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12">
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
          area: "1. Relationships",
          unit: "Significant other",
          importance: 10,
          satisfaction: 8,
          time: 3,
        },
        {
          area: "2. Body, mind, spirituality",
          unit: "Significant other",
          importance: 3,
          satisfaction: 6,
          time: 24,
        },
        {
          area: "3. Community and society",
          unit: "Community/citizenship",
          importance: 5,
          satisfaction: 2,
          time: 5,
        },
      ], // End of lifeData
      chartLabels: ["Data One", "Data Two"],
      chartData: {
        datasets: [
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
          {
            label: "2. Body, mind, spirituality",
            backgroundColor: "#7C8CF8",
            data: [
              {
                x: 10,
                y: 30,
                r: 15,
              },
            ],
          },
          {
            label: "3. Community and society",
            backgroundColor: "#9b42f5",
            data: [
              {
                x: 10,
                y: 2,
                r: 15,
              },
            ],
          },
        ],
      }, // End of graph data
      chartOptions: {
        responsive: true,
        maintainAspectRatio: true,
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
      console.log(this.tableData);
      const color = ["#9b42f5", "#f87979", "#7C8CF8"];
      for (let i = 0; i < this.tableData.length; i++) {
        let item = this.tableData[i];
        console.log(item);
        datasets.push({
          label: item.area,
          backgroundColor: color[i],
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
  },
};
</script>
