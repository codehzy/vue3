<template>
  <div id="app">
    <h1>{{ count }}</h1>
    <h1>{{ double }}</h1>
  </div>
  <ul>
    <li v-for="number in numbers" :key="number">{{ number }}</li>
  </ul>
  <h1>{{ person.name }}</h1>
  <h1>x:{{ x }},y:{{ y }}</h1>
  <h1 v-if="loading">Loading....</h1>
  <h1>
    <img v-if="loaded" :src="result[0].url" width="100" height="100" />
  </h1>
  <button @click="increase">👍🏻+1</button>
  <button @click="updateGreeting">同步更新title</button>
</template>

<script lang="ts">
import {
  ref,
  computed,
  reactive,
  toRefs,
  onMounted,
  onUpdated,
  onRenderTriggered,
  watch,
} from "vue";
import useMousePosition from "./hooks/useMousePosition";
import useURLLoader from "./hooks/useURLLoader";
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
  numbers: number[];
  person: { name?: string };
}

interface DogResult {
  message: string;
  status: string;
}

interface CatResult {
  id: string;
  url: string;
  width: number;
  height: number;
}

export default {
  name: "App",
  setup() {
    // const count = ref(0);
    // const double = computed(() => {
    //   return count.value * 2;
    // });
    // const increase = () => {
    //   count.value++;
    // };
    onMounted(() => {
      console.log("mounted");
    });
    onUpdated(() => {
      console.log("updated");
    });
    onRenderTriggered((event) => {
      console.log(event);
    });
    const data: DataProps = reactive({
      count: 0,
      increase: () => {
        data.count++;
      },
      double: computed(() => data.count * 2),
      numbers: [1, 2, 3],
      person: {},
    });

    const { x, y } = useMousePosition();
    const { result, loading, loaded } = useURLLoader<DogResult[]>(
      "https://api.thecatapi.com/v1/images/search?limit=1"
    );
    watch(result, () => {
      if (result.value) {
        console.log("value", result.value[0]);
      }
    });

    const greetings = ref("");
    const updateGreeting = () => {
      greetings.value += "Hello! ";
    };
    watch([greetings, () => data.count], (newValue, oldVlue) => {
      console.log("old", oldVlue);
      console.log("new", newValue);
      document.title = "updated" + greetings.value + data.count;
    });
    const refData = toRefs(data);
    return {
      ...refData,
      updateGreeting,
      x,
      y,
      result,
      loading,
      loaded,
    };
  },
};
</script>

<style></style>
