<template>

  <v-container class="text-xs-center">
    <div>
      <h3>Select a dataset</h3>
      <v-btn v-if='datasetScanstate === "idle"' v-on:click.prevent='scan'>Scan available data</v-btn>
      <div class="text-xs-center" v-if='datasetScanstate === "scanning"'>
        <v-progress-circular
          indeterminate
          color="primary"
        ></v-progress-circular>
      </div>
      <div v-if='datasetScanstate === "scanned"'>
        <v-data-table
          :headers="headers"
          :items="datasets"
          class="elevation-1"
        >
          <template slot="headers" slot-scope="props">
            <tr>
              <th></th>
              <th
                v-for="header in props.headers"
                :key="header"
              >
                {{ header }}
              </th>
            </tr>
          </template>
          <template slot="items" slot-scope="props">
            <tr :active="props.expanded" @click="props.expanded = !props.expanded; setIndex = props.index">
              <td>
                <v-checkbox
                  :input-value="props.expanded"
                  primary
                  hide-details
                ></v-checkbox>
              </td>
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
            <v-btn to="/data/importer">Lets add more</v-btn>
          </template>
        </v-data-table>
        <v-btn v-on:click.prevent='openRange' v-if='!rangeVisible'>Adjust range</v-btn>
        <template v-if='rangeVisible'>
          <v-layout class="pt-2">
            <v-flex xs12 sm6>
              <div class="subheading">From</div>
              <v-date-picker v-model="customFrom" color="green lighten-1"></v-date-picker>
              <!-- <v-time-picker v-model="customFrom"></v-time-picker> -->
            </v-flex>
            <v-flex>
              <div class="subheading">To</div>
              <v-date-picker v-model="customTo" color="green lighten-1" header-color="primary"></v-date-picker>
            </v-flex>
          </v-layout>
        </template>
        {{ setIndex }}
        <!-- {{ this.datasets[0] }} -->
      </div>
    </div>
  </v-container>




</template>

<script>

import _ from 'lodash'
import Vue from 'vue'

import { post } from '../../../tools/ajax'
import spinner from '../../global/blockSpinner.vue'
import dataset from '../../global/mixins/dataset'

export default {
  components: {
    spinner
  },
  data: () => {
    return {
      setIndex: -1,
      selected: -1,
      headers: ['Exchange', 'Asset','Currency','From','To','Duration'],
      customTo: null,
      customFrom: null,
      rangeVisible: false,
      set: false
    };
  },
  mixins: [ dataset ],
  methods: {
    humanizeDuration: (n) => {
      return window.humanizeDuration(n, {largest: 4});
    },
    fmt: mom => mom.utc().format('YYYY-MM-DD HH:mm'),
    openRange: function() {
      if(this.setIndex === -1)
        return alert('Select a dataset to adjust range');

      this.updateCustomRange();

      this.rangeVisible = true;
    },
    updateCustomRange: function() {
      this.customTo = this.fmt(this.set.to);
      this.customFrom = this.fmt(this.set.from);
    },
    emitSet: function(val) {
      if(!val)
        return;

      let set;

      if(!this.customTo)
        set = val;
      else {
        set = Vue.util.extend({}, val);
        set.to = moment.utc(this.customTo, 'YYYY-MM-DD HH:mm').format();
        set.from = moment.utc(this.customFrom, 'YYYY-MM-DD HH:mm').format();
      }

      this.$emit('dataset', set);
    }
  },
  watch: {

    setIndex: function() {
      this.set = this.datasets[this.setIndex];

      this.updateCustomRange();

      this.emitSet(this.set);
    },

    customTo: function() { this.emitSet(this.set); },
    customFrom: function() { this.emitSet(this.set); }
  }
}
</script>
<style>
td.radio {
  width: 45px;
}
td label{
  display: inline;
  font-size: 1em;
}
</style>