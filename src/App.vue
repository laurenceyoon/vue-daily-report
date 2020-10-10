<template>
  <div id="app">
    <Nav></Nav>
    <router-view/>
  </div>
</template>

<script>
import Nav from '@/components/Nav';

import axios from 'axios';

import { mapGetters, mapActions } from 'vuex';

export default {
  components: {
    Nav
  },
  methods: {
    ...mapActions(['saveSettings']),
    init() {
      if (typeof this.savedSettings.starttime === 'undefined') {
        this.getSettings();
      }
    },
    getSettings() {
      let url = `https://vue-daily-report-467ad.firebaseio.com/items/settings.json`;
      axios.get(url).then((res) => {
        this.saveSettings(res.data);
      });
    }
  },
  created() {
    this.init();
  },
  computed: {
    ...mapGetters(['savedSettings']),
  }
}
</script>

<style lang="scss" src="./assets/css/style.scss">
</style>
