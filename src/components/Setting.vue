<template>
  <div class="setting">
   <div class="setting-inner">
     <h1>설정</h1>
     <form @submit.prevent="saveSettings">
       <fieldset>
         <label for="starttime">시작 시간</label>
         <select id="starttime" v-model="settings.starttime">
           <option value="08:00">08:00</option>
           <option value="09:00">09:00</option>
           <option value="10:00">10:00</option>
         </select>
       </fieldset>
       <fieldset>
         <label for="lang">달력 언어</label>
         <select id="lang" v-model="settings.lang">
           <option value="ko">한국어</option>
           <option value="en">영어</option>
         </select>
       </fieldset>
       <fieldset class="submit">
         <button type="submit" class="save">저장</button>
       </fieldset>

     </form>
   </div>
  </div>
</template>

<script>
import axios from 'axios';
import { mapGetters, mapActions } from 'vuex';

export default {
  name: 'Setting',
  props: {},
  methods: {
    ...mapActions(['saveSettings']),
    saveSettings() {
      let url = `https://vue-daily-report-467ad.firebaseio.com/items/settings.json`;
      axios.put(url, this.settings).then(() => {
        alert('저장이 완료되었습니다');
      });
    },
    getSettings() {
      this.settings = this.savedSettings;
    }
  },
  data() {
    return {
      settings: {
        starttime: '08:00',
        lang: 'ko'
      }
    }
  },
  created() {
    this.getSettings();
  },
  computed: {
    ...mapGetters(['savedSettings'])
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
