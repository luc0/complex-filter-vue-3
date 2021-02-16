<template>
  <div v-if="open"> 
    <input-comparator
      @change-radio="select($event)"
      @submit-filters="submitFilters()"
      v-for="comparison in comparisonFields[modelValue.type]"
      :comparison=comparison
      :filter=modelValue
      :filters=filters
      class="input-comparator"
    ></input-comparator>

    <div class="input-comparator-options" @click.stop="$emit('remove-filter', modelValue)" >
      <a class="btn-warning">
        <i class="fas fa-trash"></i> Delete Filter
      </a>
    </div>
  </div>
</template>

<script>
import InputComparator from './InputComparator.vue'
import axios from 'axios'

  export default {
    components: {
      InputComparator,
    },
    props: {
      //modelValue: Object,
      comparison: Object,
      open: Boolean,
      filters: Array,
      comparisonFields: Array
    },
    methods: {
      select(comparisonId) {
        this.comparison = comparisonId
        this.$emit('update:comparison', this.comparison)

        const isBoolean = (comparisonId == 'true' || comparisonId == 'false')
        if(isBoolean) {
          this.submitFilters()
        }
      },
      getFiltersQueryData() { // transform the data to what API expects to be.
        let data = []

        this.filters.map(filter => {
          data.push({
            key: filter.key,
            type: filter.type,
            comparison: filter.comparison,
            value: filter.value,
          })
        })

        return data
      },
      toggleFilterEdition() {
        this.open = !this.open
      },
      async submitFilters() {
        this.toggleFilterEdition()
        
        const account = '5.min.crafts' // TODO: should be pass it as a prop maybe. (depends on functionality)
        // following consts, should be in a config file.
        const platform = 'instagram' 

        const page = 1;
        const sortBy = "posted_at"
        const sortDesc = true
        
        /* const accounts = await axios.post(`https://igblade.com/api/v3/users/${userId}/accounts/searches`) */
       
       // TODO: this should be in a config file, for more dinamic urls, (take in account version number.)
        const posts = await axios.post(`https://igblade.com/api/v3/${platform}/accounts/${account}/posts/searches`, {
           "filters": this.getFiltersQueryData(),
           "page": page,
           "sort_by": sortBy,
           "sort_desc": sortDesc
        })

        console.log('posts',posts)
      }
    },
  }
</script>
