<template>
  <b-container fluid class="rounded border border-light">
    <b-form-row class="mt-2 mb-2">
      <b-col xl="6" class="text-left">
      </b-col>
      <b-col xl="6" class="text-right">
        <b-input-group>
          <b-input-group-addon>Floor</b-input-group-addon>
          <b-form-select v-model="floor" :options="options"/>
        </b-input-group>
      </b-col>
    </b-form-row>
    <b-table striped hover bordered show-empty
      ref="shelf-table"
      :items="items"
      :current-page="currentPage"
      :per-page="perPage"
      :fields="fields">
      <template slot="row_length" slot-scope="row">
        {{ row.value + ' mm' }}
      </template>
      <template slot="actions" slot-scope="row">
        <b-button :variant="row.item._used ? 'secondary' : 'success'" size="sm" :disabled="row.item._used" @click.stop="addShelfToSim(row.item)">
          <span class="fa fa-plus"/>
        </b-button>
      </template>
    </b-table>
    <b-row>
      <b-col xl="4" class="text-left">
        <select v-model="perPage" class="button dropdown-toggle">
          <option v-for="po in pageOpts" :key="po.value" :value="po.value">{{ po.label }}</option>
        </select>
        <span>per page</span>
      </b-col>
      <b-col xl="4" class="text-center">
      </b-col>
      <b-col xl="4" class="text-right">
        <b-pagination :total-rows="totalRows" :per-page="perPage" v-model="currentPage" align="right" size="sm"/>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import store from '@/stores/SimulationStore'

export default {
  data() {
    return {
      store: store,
      floor: '',
      currentPage: 1,
      perPage: 10,
      pageOpts: [
        { label: '5', value: 5 },
        { label: '10', value: 10 },
        { label: '20', value: 20 }
      ],
      fields: {
        name: { label: 'Name', sortable: true, tdClass: 'align-middle pt-1 pb-1' },
        row_length: { label: 'Length in mm', sortable: true, tdClass: 'align-middle pt-1 pb-1' },
        levels: { label: 'Levels', sortable: true, tdClass: 'align-middle pt-1 pb-1' },
        actions: { label: 'Actions', tdClass: 'align-middle pt-1 pb-1' },
      },
    }
  },
  computed: {
    options: function() {
      return store.state.floors.map(function(floor) { return { value: floor, text: floor.name } })
    },
    items: function() {
      return this.floor ? store.state.floor_shelfs[this.floor.id] : []
    },
    totalRows: function() {
      return this.floor ? store.state.floor_shelfs[this.floor.id].length : 0
    },
  },
  methods: {
    onFiltered(filteredItems) {
      this.totalRows = filteredItems.length
      this.currentPage = 1
    },
    addShelfToSim(shelf) {
      shelf._used = true
      var tmpShelf = JSON.parse(JSON.stringify(shelf))
      store.commit('addShelf', tmpShelf)
    },
  },
}
</script>
