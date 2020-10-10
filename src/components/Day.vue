<template>
  <div class="day">
    <h1 class="day-title">
      {{ $route.params.date }}
    </h1>
    <ul class="day-list">
      <li v-for="item in items"
      :key="item.id"
      class="day-list-item"
      :class="{'open': item.open}"
      >
        <div class="time">
          <h2>{{ item.id }}</h2>
        </div>

        <div class="action">
          <p 
            v-if="!item.open"
            @click="toggleOpen(item)"
          >
            {{ item.action }}
            <span v-if="item.action.length === 0">내용을 작성해주세요.</span>
          </p>
          <input 
          v-if="item.open" 
          type="text" 
          v-model="item.action" 
          @keyup.enter="updateItem(item)" 
          placeholder="한 일을 작성해주세요">
        </div>

        <day-score
        @onUpdateScore="onUpdateScore"
        :item="item"
        >
        </day-score>

        <div class="note" v-if="item.open">
          <textarea v-model="item.note" placeholder="노트를 작성해주세요">
          </textarea>
        </div>

        <div class="buttons" v-if="item.open">
          <button class="save" @click="updateItem(item)">저장</button>
          <button class="cancel" @click="toggleOpen(item)">취소</button>
        </div>

      </li>
    </ul>
  </div>
</template>

<script>
import DayScore from './DayScore';
import axios from 'axios';
import moment from 'moment';

import { mapGetters } from 'vuex';

export default {
  name: 'Day',
  methods: {
    onUpdateScore(item, score) {
      item.score = score;
      this.updateItem(item);
    },
    getItems() {
      let url = `https://vue-daily-report-467ad.firebaseio.com/items.json?orderBy="$key"&startAt="${this.$route.params.date}"&endAt="${this.$route.params.date}-22-24"`;
      axios.get(url).then((res) => {
        this.items = this.displayItems(res.data);
      }); 
    },
    displayItems(result) {
      let items = [];
      let startTime = this.savedSettings.starttime;

      for (let i = 0; i < 12; i++) {
        let datetime = moment(this.$route.params.date + ' '+ startTime);
        let itemKey = `${this.$route.params.date}-${datetime.add(i*2, 'hours').format('HH')}-${datetime.add(2, 'hours').format('HH')}`;
        let item = {
          id: itemKey,
          action: '',
          note: '',
          open: false,
          score: undefined
        }

        Object.keys(result).map((key) => {
          if (key === itemKey) {
            item = result[key];
          }
        })
        items.push(item);
      }
      return items;
    },
    updateItem(item) {
      let url = `https://vue-daily-report-467ad.firebaseio.com/items/${item.id}.json`;
      axios.put(url, item).then(() => {
        this.getItems();
      });
    },
    toggleOpen(item) {
      item.open = !item.open;
    }
  },
  components: {
    DayScore
  },
  data() {
    return {
      items: []
    }
  },
  props: {},
  created() {
    this.getItems();
  },
  watch: {
    '$route' () {
      this.getItems();
    }
  },
  computed: {
    ...mapGetters(['savedSettings'])
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
</style>
