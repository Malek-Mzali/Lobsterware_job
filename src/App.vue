<template>

  <b-container class="px-5" fluid>
    <b-spinner v-show="loading" ref="loadApi"></b-spinner>

    <b-row class="justify-content-md-between justify-content-sm-evenly">
      <b-col class="mb-2" md="4">
        <b-form-input v-model="SearchInput" placeholder="search by title .." type="search"></b-form-input>
      </b-col>
      <b-col md="2">
        <b-form-select v-model="selected" :options="options" class="ml-5"></b-form-select>
      </b-col>
    </b-row>
  </b-container>
  <b-container class="p-5" fluid>
    <b-table id="api-table" :fields="fields" :head-variant="'light'" :items="filtredResult" bordered fixed hover
             responsive show-empty small></b-table>
  </b-container>

</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      fields: ['API', 'Description', 'Category', 'HTTPS', 'Cors', 'Link'],
      result: [],
      filtredResult: [],
      selected: null,
      options: [
        {value: null, text: 'All Categories'},
      ],
      SearchInput: '',
      loading: true,
    }

  },
  watch: {
    SearchInput: function (val) {
      this.filtredResult = this.result
          .filter((value) => {
            return this.selected == null ? value.API.toLowerCase().includes(val.toLowerCase()) : value.API.toLowerCase().includes(val.toLowerCase()) && value.Category.includes(this.selected)
          })
          .map((value) => value)

    },
    selected: function (val) {
      this.filtredResult = this.result
          .filter((value) => {
            return val == null ? this.result : value.Category.includes(val)
          })
          .map((value) => value)
    },

  },
  methods: {
    async getData() {
      const res = await fetch("https://api.publicapis.org/entries");
      const finalRes = await res.json();
      this.result = finalRes.entries;
      this.filtredResult = finalRes.entries;
      this.result.forEach(obj => {
        if (this.options.filter(e => e.value === obj.Category).length === 0) {
          this.options.push({value: obj.Category, text: obj.Category},)
        }
      })
      this.loading = false
    },
  },

  mounted() {
    this.getData().then()
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
