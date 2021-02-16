<template>
  <div class="filters">
    <a v-for="filter in filters" class="filter" @click="filter.open = !filter.open" :key="filter.key">
      <div class="filter-label">
        <span class="filter-type">
          <i class="fas fa-chart-bar"></i> {{ filter.label }}
        </span> 
        {{ comparisonName(filter) }}
        {{ filter.value }} 
      </div>

      <ComparatorSelect
        v-model:comparison="filter.comparison"
        v-model:open="filter.open"
        :filters="filters"
        :comparison-fields="comparisonFields"
        class="comparator-select"
        @remove-filter="remove(filter)"
        @click.stop
      ></ComparatorSelect>
    </a>

    <a v-on:click='toggleFilterSelector()' class="btn-filter">
      Add filter <i class="fas fa-plus-circle"></i>
      <div v-if="open" class="filter-selector">
        <a v-for="filterable in filterables" v-on:click='addFilter(filterable)' :key="filterable.label">
          <i :class="getIcon(filterable)"></i>
          {{ filterable.label }}
        </a>
      </div>
    </a>
  </div>
</template>

<script>
  import axios from 'axios'

  const filterables = [
 {
    "key":"follower_count",
    "label":"Followers",
    "type":"number"
 },
 {
    "key":"following_count",
    "label":"Followings",
    "type":"number"
 },
 {
    "key":"media_count",
    "label":"Posts",
    "type":"number"
 },
 {
    "key":"engagement_rate",
    "label":"Engagement rate",
    "type":"number"
 },
 {
    "key":"is_private",
    "label":"Is private",
    "type":"boolean"
 },
 {
    "key":"external_url",
    "label":"Profile link",
    "type":"string"
 },
 {
    "key":"is_verified",
    "label":"Is verified",
    "type":"boolean"
 },
 {
    "key":"is_business",
    "label":"Is business",
    "type":"boolean"
 },
 {
    "key":"biography",
    "label":"Biography",
    "type":"string"
 },
 {
    "key":"like_count",
    "label":"Likes",
    "type":"number"
 },
 {
    "key":"comment_count",
    "label":"Comments",
    "type":"number"
 },
 {
    "key":"view_count",
    "label":"Video views",
    "type":"number"
 },
 {
    "key":"caption",
    "label":"Caption",
    "type":"string"
 },
 {
    "key":"posted_at",
    "label":"Posted",
    "type":"date"
 },
 {
    "key":"posted_at",
    "label":"Time of day",
    "type":"time"
 }
]

const comparisonFields = {
  'number': [
    { id:'gt', name: 'greater than', query: 'gt' },
    { id:'lt', name: 'less than', query: 'lt' },
    { id:'is', name: 'is', query: 'is' },
    { id:'not', name: 'is not', query: 'is_not' },
    { id:'unk', name: 'is unknown', query: 'unknown' },
    { id:'has', name: 'has any value', query: 'any' },
  ],
  'string': [
    { id:'is', name: 'is', query: 'is' },
    { id:'not', name: 'is not', query: 'is_not' },
    { id:'starts', name: 'starts with', query: 'starts_with' },
    { id:'ends', name: 'ends with', query: 'ends_with' },
    { id:'contains', name: 'contains', query: 'contains' },
    { id:'not contain', name: 'does not contain', query: 'not_contain' },
    { id:'unk', name: 'is unknown', query: 'unknown' },
    { id:'has', name: 'has any value', query: 'any' },
  ],
  'boolean': [
    { id:'true', name: 'true', query: 'true' },
    { id:'false', name: 'false', query: 'false' },
  ],
  'date': [
    { id:'less', name: 'less than', query: 'gt_relative' },
    { id:'more', name: 'more than', query: 'lt_relative' },
    { id:'exactly', name: 'exactly', query: 'is_relative' },
    { id:'after', name: 'after', query: 'gt' },
    { id:'before', name: 'before', query: 'lt' },
    { id:'is', name: 'is', query: 'on' },
    { id:'unk', name: 'is unknown', query: 'unknown' },
    { id:'has', name: 'has any value', query: 'any' },
  ],
  'time': [
    { id:'after', name: 'after', query: 'gt' },
    { id:'before', name: 'before', query: 'lt' },
    { id:'is', name: 'is', query: 'on' },
    { id:'unk', name: 'is unknown', query: 'unknown' },
    { id:'has', name: 'has any value', query: 'any' },
  ],
}

import ComparatorSelect from './ComparatorSelect.vue'

  export default {
    name: '',
    components: {
      ComparatorSelect,
    },
    props: {
      filtersInit: Array, // This is here in case the user wants to load filters.
    },
    data() {
      // comparisonFields and filterables are loaded from a constant in their own js files. I did this to avoid run a server. In a real case I would use fetch()
      return {
        filters: this.filtersInit ? this.filtersInit : [],
        open: false,
        comparisonFields,
        filterables,
       }
    },
    mounted() {
        const token = 'ehf27Xfp7pk99Lk6QCX5GMZLRKzrGheP818rG7wyuImVOerSFWkf7Wsx21qG' // This should be in a .env variable
        axios.defaults.headers.common = {'Authorization': `Bearer ${token}`} // I set default token for axios.
    },
    methods: {
      addFilter (filterable) {
        this.filters.push({
          id: `${this.filters.length + 1}`,
          ...filterable, // I inject the props at this level, to follow the same structure as the API.
          comparison: null, // I define it null just to have the filter data structure.
          value: null,
          open: true
        })
      },
      remove(filter) {
        this.filters = this.filters.filter( f => f.id != filter.id)
      },
      toggleFilterSelector() {
        this.open = !this.open
      },
      comparisonName(filter) {
        if(!filter.comparison) return // it's more clear avoiding nested code. So the following logic is more readable

        const comparisonField = this.comparisonFields[filter.type].find(f => f.id == filter.comparison)
        return comparisonField.name
      },
      getIcon(filterable) {
        const icons = {
          'number': 'chart-bar',
          'boolean': 'toggle-on',
          'string': 'file-alt',
          'date': 'calendar-check',
          'time': 'clock',
        }

        return 'fas fa-' + icons[filterable.type]
      }
    },
  }
</script>

<style>

    /*#d04c4c red*/
    /*#4b7bec azul*/
    /*#e7effd celeste*/
    /*#f1f2f3 gris*/

    body {
      background: #f1f2f3 !important;
      color: #212529;
      font-family: "verdana";
    }

    /*input {
      margin: 0 !important;
      padding: 0 !important;
    }*/

    a {
      text-decoration: none;
      color: #212529;
    }

    input {
      font-size: 16px;
      color: #212529;
      padding: 8px;
      margin-left: 8px;
      margin-top: 8px;
      border: 1px solid #ccc;
      border-radius: 8px;
    }

    input[type="text"] {
      width: 60px;
    }

    .filters {
      display: flex;
      background: #fff;
      margin: 20px;
      padding: 30px;
      border-radius: 14px;
      min-height: 200px;
    }

    .filter {
      margin-right: 10px;
      position: relative;
      min-width: 250px;
      cursor: pointer;
      height: 100%;
    }

    .filter-label {
      font-size: 18px;
      background: #f1f2f3;
      padding: 15px;
      border-radius: 8px;
    }

    .filter-type {
      font-weight: bold;
    }

    .filter-selector {
      position: absolute;
      top: 60px;
      background: #fff;
      width: 250px;
      box-shadow: #ddd 0 0 30px -5px;
    }

    .filter-selector i {
      margin-right: 5px;
    }

    .filter-selector a{
      font-size: 18px;
      padding: 8px 24px;
      display: block;
    }

    .filter-selector a:hover{
      background: #fafafa;
      color: #CF000F;
      cursor: pointer;
    }

    .btn-filter {
      display: block;
      color: #CF000F;
      font-size: 18px;
      padding: 14px;
      position: relative;
    }

    .btn-filter:hover {
      border-bottom: #fafafa 1px solid;
      cursor: pointer;
    }
    
    .comparator-select {
      position: absolute;
      background: #fff;
      width: 210px;
      box-shadow: #ddd 0 0 30px -5px;
      border-radius: 8px;
      padding-top: 20px;
    }

    .comparator-option {
      display: flex;
      align-items: center;
      padding: 8px 20px;
    }

    .comparator-option input {
      margin-right: 10px;
    }

    .comparator-option:hover {
      background: #fafafa;
      color: #CF000F;
    }

    .input-comparator {
      display: flex;
      flex-direction: column;
      text-transform: capitalize;
    }

    .input-comparator-options {
      border-top: 1px solid #f1f2f3;
    }

    .comparator-value {
      width: 160px;
      align-self: center;
    }

    .btn-warning {
      display: block;
      padding: 25px;
      color: #d04c4c;
      border-radius: 0 0 8px 8px;
    }

    .btn-warning:hover {
      color: #fff;
      background: #d04c4c;
    }

    .btn-warning i {
      margin-right: 5px;
    }

    .input-comparator:first-child {
      margin: 0;
    }
  
</style>