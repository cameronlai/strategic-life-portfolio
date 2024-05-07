<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto" max-width="1200">
      <div class="text-center">
        <h1 class="text-h2 font-weight-bold">Strategic Life Portfolio</h1>
      </div>

      <div class="py-4" />

      <v-row>
        <v-col cols="6">
          <v-card
            class="py-4"
            prepend-icon="mdi-text-box-outline"
            rounded="lg"
            subtitle="Enter your life portfolio in this table"
            title="Your Life Porfolio"
            variant="text"
          >
            <v-table>
              <thead>
                <tr>
                  <th class="text-left">Area</th>
                  <th class="text-left">Unit</th>
                  <th class="text-left">Importance</th>
                  <th class="text-left">Satifcation</th>
                  <th class="text-left">Time (Hours)</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in lifeData" :key="item.name">
                  <td>{{ item.area }}</td>
                  <td>{{ item.unit }}</td>
                  <td>{{ item.time }}</td>
                  <td>{{ item.importance }}</td>
                  <td>{{ item.satisfaction }}</td>
                </tr>
              </tbody>
            </v-table>
          </v-card>
        </v-col>

        <v-col cols="1"> </v-col>

        <v-col cols="5" >
          <v-card
            class="py-4"
            prepend-icon="mdi-eye-outline"
            rounded="lg"
            subtitle="Visualize your life portfolio in this graph"
            title="2x2 Life Portfolio"
            variant="text"
          >
          </v-card>
          <Bubble
            :data="chartData"
            :labels="chartLabels"
            :options="chartOptions"
            hei
          />
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

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

export default {
  name: "App",
  components: { Bubble },
  data() {
    return {
      lifeData: [
        {
          area: "Relationships",
          unit: "Significant other",
          importance: 10,
          satisfaction: 2,
          time: 1,
        },
        {
          area: "Body, mind, spirituality",
          unit: "Significant other",
          importance: 10,
          satisfaction: 2,
          time: 1,
        },
        {
          area: "Community and society",
          unit: "Community/citizenship",
          importance: 10,
          satisfaction: 2,
          time: 1,
        },
      ], // End of lifeData
      chartLabels: ["Data One", "Data Two"],
      chartData: {
        datasets: [
          {
            label: "Data One",
            backgroundColor: "#f87979",
            data: [
              {
                x: 20,
                y: 25,
                r: 5,
              },
              {
                x: 40,
                y: 10,
                r: 10,
              },
              {
                x: 30,
                y: 22,
                r: 30,
              },
            ],
          },
          {
            label: "Data Two",
            backgroundColor: "#7C8CF8",
            data: [
              {
                x: 10,
                y: 30,
                r: 15,
              },
              {
                x: 20,
                y: 20,
                r: 10,
              },
              {
                x: 15,
                y: 8,
                r: 30,
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
};
</script>
