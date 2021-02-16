<template>
  <div v-if="filter.open"> 
    <input-comparator
      @change-radio="select($event)"
      @submit-filters="submitFilters()"
      v-for="comparison in comparisonFields[filter.type]"
      :key="comparison.id"
      :comparison=comparison
      :filter=filter
      :filters=filters
      class="input-comparator"
    ></input-comparator>

    <div class="input-comparator-options" @click.stop="$emit('remove-filter', filter)" >
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
      filter: Object,
      filters: Array,
      comparisonFields: Array
    },
    methods: {
      select(comparisonId) {
        this.filters.map((filter) => {
            if (filter.id === this.filter.id) {
                filter.comparison = comparisonId
            }
        });
        this.$emit('update:filters', this.filters)

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
        this.filters.map((filter) => {
            if (filter.id === this.filter.id) {
                filter.open = open
                console.log('yes')
            }
        });
        //this.filter.open = !this.filter.open
      },
      async submitFilters() {
        this.toggleFilterEdition()
        
        const account = '5.min.crafts' // TODO: should be pass it as a prop maybe. (depends on functionality)
        // following consts, should be in a config file.
        const platform = 'instagram' 

        const page = 1;
        const sortBy = "posted_at"
        const sortDesc = true
              
       // TODO: this should be in a config file, for more dinamic urls, (take in account version number.)
       // Just for security reasons I hide the site with xxxxx.com, the response is working in the real case.
        try {
          const posts = await axios.post(`https://xxxxx.com/api/v3/${platform}/accounts/${account}/posts/searches`, {
             "filters": this.getFiltersQueryData(),
             "page": page,
             "sort_by": sortBy,
             "sort_desc": sortDesc
          })
          console.log('posts', posts)
        } catch (error) {
          console.log(error);
        }
      }
    }
  }
</script>
