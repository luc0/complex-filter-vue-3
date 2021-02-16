<template>
  <items-filter :filtersInit="[]"></items-filter>
</template>

<script>
import ItemsFilter from './components/ItemsFilter.vue'

export default {
  name: 'App',
  components: {
    ItemsFilter
  }
}
</script>