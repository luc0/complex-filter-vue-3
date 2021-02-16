<template>
  <div>
    <a @click="$emit('change-radio', comparison.id)" href="#" class="comparator-opftion">
      <input
        :value="comparison"
        type="radio"
        name="filterable"
        class="comparator-radio"
        :checked="showComparisonValueInput()"
      />
      <span>{{ comparison.name }}</span>
    </a>

    <input 
      v-if="showComparisonValueInput()"
      v-model="filter.value" 
      :type="getInputType(filter)" 
      @keyup.enter.native="$emit('submit-filters')"
      class="comparator-value"
      />
  </div>
</template>

<script>
  // Current comparator selector and value.
  export default {
  	props: {
      comparison: Object,
      filter: Object
    },
  	methods: {
  		showComparisonValueInput() {
  			return (this.filter.comparison == this.comparison.id && this.needsValue())
  		},
      needsValue() {
        return this.filter.type != 'boolean' // booleans doesn't need input. TODO: there are other cases that doesn't need value (for example: "is unknown").
      },
      getInputType(filter) {
        const types = {
          'number': 'number',
          'string': 'text',
          'date': 'date',
          'time': 'time',
        }

        return types[filter.type] // more readable than using switch case.
      },
      
  	}
  }
</script>