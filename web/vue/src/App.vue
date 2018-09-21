<template>
  <v-app :dark="darkTheme">
    <v-navigation-drawer
      :mini-variant.sync="miniVariant"
      :clipped="clipped"
      v-model="drawer"
      fixed
      app
      :dark="darkTheme"
    >
      <v-list>
        <v-list-tile
          router
          :to="item.to"
          :key="i"
          v-for="(item, i) in items"
          exact
        >
          <v-list-tile-action>
            <v-icon v-html="item.icon"></v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="item.title"></v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
        <v-list-tile>
            <v-list-tile-action>
              <v-icon>library_books</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <a href="https://gekko.wizb.it/docs/introduction/about_gekko.html">
                <v-list-tile-title>Documentation</v-list-tile-title>
              </a>
            </v-list-tile-content>
        </v-list-tile>
        <v-list-tile>
          <v-list-tile-action>
            <v-switch
              v-model="darkTheme"
            ></v-switch>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title>Dark Theme</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar color="primary" fixed app :clipped-left="clipped">
      <v-toolbar-side-icon @click="drawer = !drawer"></v-toolbar-side-icon>
      <v-btn
        icon
        @click.stop="miniVariant = !miniVariant"
      >
        <v-icon v-html="miniVariant ? 'chevron_right' : 'chevron_left'"></v-icon>
      </v-btn>
      <v-btn
        icon
        @click.stop="clipped = !clipped"
      >
        <v-icon>web</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title"></v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>
    <v-content>
      <router-view>
        <v-container fluid></v-container>
      </router-view>
    </v-content>
    <v-footer fixed app>
      <div>Use Gekko at your own risk.</div>
      <v-spacer></v-spacer>
      <div>
        Using Gekko v{{ version.gekko }} and Gekko UI v{{ version.ui }}.
      </div>
    </v-footer>
  </v-app>
</template>

<script>
const gekkoPackage = require('../../../package.json');
const uiPackage = require('../../../package.json');

export default {
  data () {
    return {
      clipped: true,
      darkTheme: true,
      drawer: true,
      items: [
        { icon: 'home', title: 'Home', to: '/home' },
        { icon: 'show_chart', title: 'Live Gekkos', to: '/live-gekkos' },
        { icon: 'insert_chart', title: 'Backtest', to: '/backtest' },
        { icon: 'pie_chart', title: 'Local data', to: '/data' },
        { icon: 'apps', title: 'Config', to: '/config' }
      ],
      miniVariant: true,
      title: 'Gekko UI',
      version: {
        gekko: gekkoPackage.version,
        ui: uiPackage.version
      }
    }
  },
  name: 'app'
}
</script>

<style>

#app {
  display: flex;
  min-height: 100vh;
  flex-direction: column;
}

.fill {
  flex: 1;
}

.text {
  max-width: 500px;
}
input {
  background: none;
  margin-top: 0.5em;
}

.params {
  min-height: 235px;
  line-height: 1.3em;
}

.hr {
  margin-top: 2rem;
  margin-bottom: 2rem;
  height: 10px;
  background-color: rgba(250,250,250,.99);
}

.btn--primary {
  display: inline-block;
  margin-right: 12px;
  margin-bottom: 12px;
  height: 40px;
  padding: 0 18px;
  border-radius: 4px;
  background-color: #3498db;
  text-shadow: 0 1px 3px rgba(36,180,126,.4);
  box-shadow: 0 4px 6px rgba(50,50,93,.11), 0 1px 3px rgba(0,0,0,.08);
  color: #fff;
  text-decoration: none;
  line-height: 40px;
  transition: transform 250ms;
}

.btn--primary:hover {
  transform: translateY(-1px);
  box-shadow: 0 7px 14px rgba(50,50,93,.1), 0 3px 6px rgba(0,0,0,.08);
  background-color: #3498db;
  color: #fff;
  text-decoration: none;
}

.btn--primary:active,
.btn--primary:focus {
  background-color: #3498db;
  color: #fff;
  text-decoration: none;
}

.btn--primary:active {
  transform: translateY(1px);
}
</style>
