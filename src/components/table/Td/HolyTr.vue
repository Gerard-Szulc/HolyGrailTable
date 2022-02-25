<template>
  <tr :style="`max-height: ${height}px; height: ${height}px;`">
    <td v-for="column in columns.filter((_,colInd) => colInd !== 0)" :key="`${column.value}-${index}`">
      <component v-if="column.component" :is="column.component" :row="row" :column="column" :index="index"/>
      <span v-else>{{ row[column.value] }}</span>
    </td>
  </tr>
</template>

<script setup>
defineProps({
  row: {},
  index: {
    type: Number
  },
  height: {},
  columns: {
    type: Array,
    default: () => [{}]
  },
})
const hello = await new Promise(resolve => {
  setTimeout(() => resolve('hello'), 2000)
})
</script>

<style scoped>

</style>