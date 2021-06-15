<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" width="100" height="100" />
    <h1>{{ count }}</h1>
    <h1>{{ double }}</h1>
    <h1>{{ greetings }}</h1>
    <p>{{ error }}</p>
    <Suspense>
      <template #default>
        <div>
          <async-show />
          <dog-show />
        </div>
      </template>
      <template #fallback>
        <h1>Loading !...</h1>
      </template>
    </Suspense>
    <button @click="openModal">Open Modal</button><br />
    <modal :isOpen="modalIsOpen" @close-modalone="onModalClose">
      My Modal !!!!</modal
    >
    <h1 v-if="loading">Loading!...</h1>
    <img v-if="loaded" :src="result[0].url" width="100" height="100" />
    <h1>X: {{ x }}, Y: {{ y }}</h1>
    <button @click="increase">👍+1</button><br />
    <button @click="updateGreeting">Update Title</button>
  </div>
</template>

<script lang="ts">
import { ref, computed, reactive, toRefs, watch, onErrorCaptured } from "vue";
import useMousePosition from "./hooks/useMousePosition";
import useURLLoader from "./hooks/useURLLoader";
import Modal from "./components/Modal.vue";
import AsyncShow from "./components/AsyncShow.vue";
import DogShow from "./components/DogShow.vue";
interface DataProps {
  count: number;
  double: number;
  increase: () => void;
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
  components: {
    Modal,
    AsyncShow,
    DogShow,
  },
  setup() {
    const error = ref(null);
    onErrorCaptured((e: any) => {
      error.value = e;
      return true;
    });

    const data: DataProps = reactive({
      count: 0,
      increase: () => {
        data.count++;
      },
      double: computed(() => data.count * 2),
    });
    const { x, y } = useMousePosition();
    const { result, loading, loaded } = useURLLoader<CatResult[]>(
      "https://api.thecatapi.com/v1/images/search?limit=1"
    );
    watch(result, () => {
      if (result.value) {
        console.log("value", result.value[0].url);
      }
    });
    const greetings = ref("");
    const updateGreeting = () => {
      greetings.value += "Hello! ";
    };
    const refData = toRefs(data);
    const modalIsOpen = ref(false);
    const openModal = () => {
      modalIsOpen.value = true;
    };
    const onModalClose = () => {
      modalIsOpen.value = false;
    };
    return {
      ...refData,
      greetings,
      updateGreeting,
      x,
      y,
      result,
      loading,
      loaded,
      modalIsOpen,
      openModal,
      onModalClose,
      error,
    };
  },
};
</script>

<style></style>
