<template>
  <div>
    <h1>Jalkakäytävä-kulkijat</h1>
    <ul>
      <p v-for="(item, index) in slicedData" :key="index">
        {{ item.counterId }} - {{ item.dataTimestamp }} - {{ item.count }}
      </p>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";
import axios from "axios";

interface Stamp {
  counterId: string;
  dataTimestamp: string;
  interval: number;
  count: number;
}

interface Features {
  type: string;
  geometry: Geometry;
  properties: Property;
}
interface Property {
  id: string;
  name: string;
  domain: string;
}

interface Geometry {
  type: string;
  coordinates: number[];
}

export default defineComponent({
  name: "TrafficEvent",
  setup() {
    const stamp = ref<Stamp[]>([]);
    const features = ref<Features[]>([]);
    const fetchEvents = async () => {
      try {
        const response = await axios.get(
          "https://tie.digitraffic.fi/api/counting-site/v1/values"
        );
        stamp.value = response.data;
      } catch (error) {
        console.error(error);
        stamp.value = [];
      }
    };
    const fetchPlaces = async () => {
      try {
        const response = await axios.get(
          "https://tie.digitraffic.fi/api/counting-site/v1/counters"
        );
        features.value = response.data.features;
        console.log(response.data.features);
      } catch (error) {
        console.error(error);
        features.value = [];
      }
    };
    fetchEvents();
    fetchPlaces();

    const slicedData = computed<Stamp[]>(() => {
      const dates = stamp.value.map((item) => new Date(item.dataTimestamp));
      const maxDate = dates.reduce((a, b) => (a > b ? a : b), dates[0]);
      const filteredData = stamp.value.filter(
        (item) => new Date(item.dataTimestamp).getTime() === maxDate.getTime()
      );
      // map names from features.properties to stamp.counterId
      filteredData.forEach((item) => {
        const feature = features.value.find(
          (feature) => feature.properties.id === item.counterId
        );
        if (feature) {
          item.counterId = feature.properties.name;
          console.log(item.counterId);
        }
      });
      return filteredData;
    });

    return { slicedData };
  },
});
</script>

<style>
#trafficevent {
  text-align: center;
}
</style>
