<script setup lang="ts">
import someComponent from './components/SomeComponent.vue'
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import HolyTable from "./components/table/HolyTable.vue";
import {reactive, ref, shallowRef} from "vue";
const columnsData = Array(20).fill(0).map((_, i) => ({value: 'id', label: 'id' + i}));
const arr = Array(200).fill(0).map((_, i) => ({id: i, klops: "hello", kokos: 'a' + i + 'b', label: 'A'}));
const rows = reactive([...arr])
const columns = reactive([{value: 'id', label: 'id'}, {value: 'klops', label: 'a'}, {value: 'kokos', label: 'b'}, ...columnsData])
const loadMoreData = () => {
  rows.push(...arr.map((el, i) => ({...el, id: i + rows.length})))
}
const height = ref(100)
</script>

<template>
  <HolyTable
      :columns="columns"
      :rows="rows"
      :height="height"
      @nextPage="loadMoreData"
  />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
