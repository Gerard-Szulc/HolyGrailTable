<script lang="ts" setup>
import {onMounted, ref, toRefs, watch, watchEffect} from "vue";

const props = defineProps<{
  rows: any[];
  columns: any[];
}>()
toRefs(props);
const emit = defineEmits(['nextPage'])

const tableRef = ref()
const trRefs = ref([])


const options = {
  root: tableRef.value,
  rootMargin: '0px',
  threshold: 0
}

function callback(entries: any, observer: any) {
  console.log(observer);

  entries.forEach((entry: any) => {
    if (entry.isIntersecting) {
      emit('nextPage')
    }
    console.log('entry', entry);
  });
}

let observer = new IntersectionObserver(callback, options);
const addObserver = (target: Element, observer: IntersectionObserver) => {
  observer.observe(target);
}
const removeObserver = (target: Element, observer: IntersectionObserver) => {
  observer.unobserve(target);
}

onMounted(() => {
  addObserver(trRefs.value[trRefs.value.length - 1] as Element, observer);
})

watchEffect(() => {
  console.log('watchEffect')
  observer.disconnect();
  observer = new IntersectionObserver(callback, options);

  addObserver(trRefs.value[trRefs.value.length - 1] as Element, observer);
}, {
  flush: 'post'
})

</script>
<template>
  <div id="holy-table-wrapper">
    <table class="table-ids">
      <thead>
      <tr>
        <th style="position: sticky">
          {{columns[0].label}}
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(row, index) in rows" :key="index">
        <td>
          {{ row[columns[0].value] }}
        </td>
      </tr>
      </tbody>
    </table>
    <table id="holy-table" ref="tableRef">
      <thead>
      <tr>
        <th v-for="column in columns.filter((_,colInd) => colInd !== 0)" :key="column.value">
          {{ column.label }}
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(row, index) in rows" :key="index" :ref="el => { if (el) trRefs[index] = el }">
        <td v-for="column in columns.filter((_,colInd) => colInd !== 0)" :key="`${column.value}-${index}`">
          <component v-if="column.component" :is="column.component" :row="row" :column="column" :index="index" />
          <span v-else>{{ row[column.value] }}</span>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.table-ids {
  z-index: 1;
  left: 0;
  position: sticky;
  background-color: #f5f5f5;
}
table {
  height: 100%;
}
#holy-table-wrapper {
  display: flex;
  flex-direction: row;
  width: 100%;
  max-width: 400px;
  height: 240px;
  margin: 0 auto;
  overflow-x: auto;
  border-spacing: 0;
}

th {
  position: sticky;
  top: 0;
  background: #42b983;
}

#holy-table tbody {
  /*display: flex;*/
  /*flex-direction: row-reverse;*/
  /*justify-content: start;*/
}

</style>