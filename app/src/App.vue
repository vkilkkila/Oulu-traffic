<template>
  <div>
    <TrafficEvent />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import TrafficEvent from "./components/TrafficEvent.vue";

export default defineComponent({
  name: "App",
  components: { TrafficEvent },
  setup() {
    const message = ref("Hello, Vue!");

    function increment() {
      message.value += "!";
    }

    return {
      message,
      increment,
    };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
