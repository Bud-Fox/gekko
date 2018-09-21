<template>
  <v-container class="text-xs-center">
    <div>
      <h3>Local Data</h3>
      <p class="subheading">Gekko needs local market data in order to backtest strategies. The local
data can also be used in a warmup period when running a strategy against a
live market.</p>
    </div>
    <h2>Available Datasets</h2>
    <v-btn color="primary" v-if='datasetScanstate === "idle"' v-on:click.prevent='scan'>Scan Available data</v-btn>
    <div class="text-xs-center" v-if='datasetScanstate === "scanning"'>
      <v-progress-circular
        indeterminate
        color="primary"
      ></v-progress-circular>
    </div>
    <div v-if='datasetScanstate === "scanned"'>
      <v-data-table
        v-model="selected"
        :headers="headers"
        :items="datasets"
        class="elevation-1"
      >
        <template slot="headers" slot-scope="props">
          <tr>
            <th
              v-for="header in props.headers"
              :key="header"
            >
              {{ header }}
            </th>
          </tr>
        </template>
        <template slot="items" slot-scope="props">
          <tr>
            <td class="text-xs-left">{{ props.item.exchange }}</td>
            <td class="text-xs-left">{{ props.item.asset }}</td>
            <td class="text-xs-left">{{ props.item.currency }}</td>
            <td class="text-xs-left">{{ fmt(props.item.from) }}</td>
            <td class="text-xs-left">{{ fmt(props.item.to) }}</td>
            <td class="text-xs-left">{{ humanizeDuration(props.item.to.diff(props.item.from)) }}</td>
          </tr>
        </template>
        <template slot="no-data">
          <v-alert :value="true" color="error" icon="warning">
            You don't have vailable datasets :(
          </v-alert>
        </template>
      </v-data-table>
      <!-- {{ selected }} -->
    </div>
    <h2>Import More Data</h2>
    <div>You can easily import more market data directly from exchanges using the importer</div>
    <v-btn color="primary" to='/data/importer'>Go to the importer!</v-btn>
  </v-container>
</template>

<script>

import dataset from '../global/mixins/dataset'
// global moment
// global humanizeDuration


export default {
  mixins: [ dataset ],
  data: () => ({
    headers: ['Exchange', 'Asset','Currency','From','To','Duration'],
    viewUnscannable: false
    
  }),
  methods: {
    toggleUnscannable: function() { this.viewUnscannable = true },
    humanizeDuration: (n) => window.humanizeDuration(n),
    fmt: mom => mom.format('YYYY-MM-DD HH:mm')
  }
}
</script>

<style scoped>
  .v-progress-circular {
    margin: 1rem
  }
</style>
