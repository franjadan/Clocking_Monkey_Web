<template>
    <div class="container mt-5">
        <form id="search">
            <div>
              <label for="query" class="mr-3">Buscar</label>
              <input type="text" v-model="search" id="query" name="query" class="form-control w-75 d-inline">
            </div>
            <div>
              <download-excel :data="filteredData" :fields="json_fields" worksheet="My Worksheet" name="filename.xls" class="btn p-2" v-if="admin"><i class="fas fa-file-download mr-1"></i> Descargar Excel</download-excel>
            </div>
        </form>
        <div id="grid-template">
            <div>
                <table class="table table-striped table-bordered text-center">
                    <thead>
                        <tr>
                            <th v-for="(key, index) in columns" v-bind:key="index" scope="col">{{ key | capitalize}}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(entry, index) in dataPerPage" v-bind:key="index" scope="row">
                            <td v-for="(key, index) in keys" v-bind:key="index">
                                {{ entry[key] }}
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <div id="page-navigation" class="mt-5 row container">
            <button @click="movePages(-1)" class="col btn">Anterior</button>
            <span class="col text-center lead">{{ startRow / rowsPerPage + 1}} de {{ Math.ceil(filteredData.length / rowsPerPage) }}</span>
            <button @click="movePages(1)" class="col btn">Siguiente</button>
        </div>
    </div>
</template>

<script>
import Vue from 'vue'
import JsonExcel from 'vue-json-excel'
Vue.component('downloadExcel', JsonExcel)

export default {
  name: 'grid',
  props: {
    data: Array,
    columns: Array,
    keys: Array,
    admin: ''
  },
  data () {
    return {
      search: '',
      startRow: 0,
      rowsPerPage: 10,
      json_fields: {
        'Usuario': 'email',
        'Fecha': 'date',
        'Tipo': 'type'
      }
    }
  },
  computed: {
    dataPerPage: function () {
      return this.filteredData.filter((item, index) => index >= this.startRow && index < (this.startRow + this.rowsPerPage))
    },
    filteredData: function () {
      let filterKey = this.search && this.search.toLowerCase()
      let data = this.data

      if (filterKey) {
        data = data.filter(function (row) {
          return Object.keys(row).some(function (key) {
            return String(row[key]).toLowerCase().indexOf(filterKey) > -1
          })
        })
      }
      return data
    }
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    }
  },
  methods: {
    movePages: function (amount) {
      let newStartRow = this.startRow + (amount * this.rowsPerPage)

      if (newStartRow >= 0 && newStartRow < this.filteredData.length) {
        this.startRow = newStartRow
      }
    }
  }
}
</script>

<style scoped>

  thead th {
    background-color: rgba(104,159,56);
    color: #ffffff;
  }

  .btn{
    background-color: rgba(104,159,56);
    color: #ffffff;
  }

  #search{
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1.5em;
  }

  #grid-template{
    margin-top: 3em;
  }
</style>
