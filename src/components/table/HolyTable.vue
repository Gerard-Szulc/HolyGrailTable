<script lang="ts" setup>
import {computed, onMounted, ref, toRefs, watch, watchEffect} from "vue";
import HolyTr from "./Td/HolyTr.vue";

const props = defineProps<{
  rows: any[];
  columns: any[];
  height: number;
}>()
toRefs(props);
const emit = defineEmits(['nextPage'])
const tableRef = ref()
const tableWrapper = ref()
const trRefs = ref([])
const viewPortHeight = 600
const viewPortHeightCss = `${viewPortHeight}px`
const borderSpacingValue = 2
const borderSpacing = `${borderSpacingValue}px`
const totalTableHeight = computed(() => {
      return props.height * props.rows.length + (borderSpacingValue * props.rows.length)
    })

const options = {
  root: tableRef.value,
  rootMargin: '0px',
  threshold: 0
}
const scrollStart = ref(0);
const scrollEnd = ref(0);


onMounted(() => {
  handleTablesScroll()
})
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
  // addObserver(trRefs.value[trRefs.value.length - 1] as Element, observer);
})

// watchEffect(() => {
//   console.log('watchEffect')
//   observer.disconnect();
//   observer = new IntersectionObserver(callback, options);
//
//   addObserver(trRefs.value[trRefs.value.length - 1] as Element, observer);
// }, {
//   flush: 'post'
// })
const start = computed(() => {
  return Math.max(Math.floor(scrollStart.value / (props.height + borderSpacingValue) - options.threshold), 0)
})
const end = computed(() => {
  return Math.min(Math.ceil(scrollEnd.value / (props.height + borderSpacingValue) + options.threshold), totalTableHeight.value)
})

const visibleRows = computed(() => {
  return {
    pre: props.rows.slice(0,start.value),
    visible: props.rows.slice(start.value, end.value),
    post: props.rows.slice(end.value, props.rows.length)
  }
})

const handleTablesScroll = () => {
  const tableWrapperComp = tableWrapper.value
  scrollStart.value = tableWrapperComp.scrollTop
  scrollEnd.value = tableWrapperComp.scrollTop + viewPortHeight
}
</script>
<template>
  <div id="holy-table-wrapper" ref="tableWrapper" @scroll="handleTablesScroll">
    <table class="table-ids" :style="`height: ${totalTableHeight}px`">
      <thead>
      <tr>
        <th style="position: sticky">
          {{columns[0].label}}
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(row, index) in visibleRows.pre" :key="index" :style="`max-height: ${height}px; height: ${height}px;`">
      </tr>
      <tr v-for="(row, index) in visibleRows.visible" :key="index" :style="`max-height: ${height}px; height: ${height}px;`">
        <td>
          {{ row[columns[0].value] }}
        </td>
      </tr>
      <tr v-for="(row, index) in visibleRows.post" :key="index" :style="`max-height: ${height}px; height: ${height}px;`">
      </tr>

      </tbody>
    </table>
    <table id="holy-table" ref="tableRef" :style="`height: ${totalTableHeight}px`">
      <thead>
      <tr>
        <th v-for="column in columns.filter((_,colInd) => colInd !== 0)" :key="column.value">
          {{ column.label }}
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(row, index) in visibleRows.pre" :key="index" :style="`max-height: ${height}px; height: ${height}px;`">
      </tr>
      <template v-for="(row, index) in visibleRows.visible" :key="index">
        <Suspense>
            <HolyTr
                :ref="el => { if (el) trRefs[index] = el }"
                :row="row"
                :index="index"
                :columns="columns"
                :height="height"
            />

          <template #fallback>
            <slot name="suspenseLoading">
              <tr :style="`max-height: ${height}px; height: ${height}px;`"><td>Loading</td></tr>
            </slot>
          </template>
        </Suspense>
      </template>
      <tr v-for="(row, index) in visibleRows.post" :key="index" :style="`max-height: ${height}px; height: ${height}px;`">
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
  border-spacing: v-bind(borderSpacing);
}
#holy-table-wrapper {
  display: flex;
  flex-direction: row;
  width: 100%;
  max-width: 600px;
  height: 600px;
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
  height: v-bind(viewPortHeightCss);
  max-height: v-bind(viewPortHeightCss);

  /*display: flex;*/
  /*flex-direction: row-reverse;*/
  /*justify-content: start;*/
}

</style>