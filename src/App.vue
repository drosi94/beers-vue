<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Beers</span>
      </v-toolbar-title>
    </v-toolbar>

    <v-content>
      <BeerList/>
    </v-content>
  </v-app>
</template>

<script>
import BeerList from './components/BeerList'

export default {
  name: 'App',
  components: {
    BeerList
  },
  data () {
    return {
      //
    }
  }
}
</script>
