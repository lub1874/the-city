<template>
  <div class="event">
    <loading :isOpen="isOpen"></loading>
    <header>
      <select v-model="selectCity" @change="changeCity">
        <option v-for="city in cityList" :value="city.id">{{city.name}}</option>
      </select>
      <select v-model="selectDate" @change="changeDate">
        <option value="week">近一周</option>
        <option value="today">今天</option>
        <option value="tomorrow">明天</option>
        <option value="weekend">周末</option>
        <option value="all">全部</option>
      </select>
      <select v-model="selectType" @change="changeType">
        <option value="all">全部</option>
        <option value="music">音乐</option>
        <option value="film">电影</option>
        <option value="drama">戏剧</option>
        <option value="commonweal">公益</option>
        <option value="salon">讲座</option>
        <option value="exhibition">展览</option>
        <option value="party">聚会</option>

        <option value="sports">运动</option>
        <option value="travel">旅行</option>
        <option value="others">其他</option>
      </select>
      <select v-model="selectFee" @change="changeFee">
        <option value="free">免费</option>
        <option value="all">全部</option>
      </select>
      共 {{total}} 个

    </header>
    <div class="info_warp">
      <div v-for="event in arr">
        <a :href="event.url">
          <img :src="event.image">
          <div class="right_area">
            <span class="info_title">
              {{event.title}}{{'【' + event.category_name + '】'}}
            </span>
            <span class="rt">开始：{{event.begin_time}}</span><br/>
            <span class="rt">结束： {{event.end_time}}</span><br/>
            <span class="rt">地点：{{event.address}}</span><br/>
            <span class="rt">费用：
                <span :class="{'free-event': selectFee === 'all' && event.fee_str === '免费'}">{{event.fee_str}}</span>
            </span><br/>
            <span class="rt">参加：{{event.participant_count}}人</span>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .event {
    overflow: hidden;
  }

  header {
    text-align: center;
    padding: 1rem 0;
    background: #34495e;
    color: #fff;
    letter-spacing: 1px;
    position: fixed;
    width: 100%;
  }

  select {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background: none;
    border: none;
    color: #fff;
    font-size: 16px;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    outline: none;
  }

  .info_warp {
    margin: 3.3rem 0;
  }

  .info_warp div a {
    display: block;
    overflow: hidden;
    padding: 3% 3% 3% 0;
    text-decoration: none;
    border-bottom: 1px solid #bdc3c7;
    background: #ecf0f1;
  }

  .info_warp div a img {
    display: inline-block;
    float: left;
    margin: 0 3%;
    width: 30%;
  }

  .info_warp .right_area {
    color: #2c3e50;
    display: inline-block;
    width: 60%;
  }

  .right_area .rt {
    font-size: 0.9rem;
  }

  .info_title {
    font-size: 1rem;
    font-weight: 600;
    word-break: break-all;
    margin-bottom: 0.4rem;
    display: inline-block;
  }

  .free-event {
    color: #1ABC9C;
  }
</style>

<script>
  import loading from './Loading'
  export default {
    name: 'event',
    data () {
      return {
        isOpen: false,
        total: 0,
        arr: [],
        cityList: [],
        selectCity: localStorage.city || 108288,
        selectDate: localStorage.date || 'week',
        selectType: localStorage.type || 'all',
        selectFee: localStorage.fee || 'free'
      }
    },
    components: {loading},
    mounted() {
      this.loadCityList();
      this.loadData(this.selectCity, this.selectDate, this.selectType);
    },
    methods: {
      loadCityList() {
        this.$http.jsonp('https://api.douban.com/v2/loc/list', {params: {count: 48}}).then(function (res) {
          this.cityList = res.body.locs;
        })
      },
      loadData(cityId, date, type) {
        this.isOpen = true;
        this.$http.jsonp(
          'https://api.douban.com/v2/event/list',
          {
            params: {
              count: 60,
              loc: cityId,
              day_type: date,
              type: type
            }
          }
        ).then(function (res) {
          let data = res.body;
          this.arr = this.handleData(data.events, this.selectFee);
          this.total = this.arr.length;
          this.isOpen = false;
        })
      },
      handleData(data, isFee) {
        if (isFee === "free") {
          let filterData = [];
          filterData = data.filter(function (value, index, arr) {
            return value.fee_str === '免费';
          });
          filterData.sort(function (a, b) {
            return b.participant_count - a.participant_count;
          });

          return filterData;
        } else {
          return data;
        }
      },
      changeCity() {
        this.loadData(this.selectCity, this.selectDate, this.selectType);
        localStorage.city = this.selectCity;
      },
      changeDate() {
        this.loadData(this.selectCity, this.selectDate, this.selectType);
        localStorage.date = this.selectDate;
      },
      changeType() {
        this.loadData(this.selectCity, this.selectDate, this.selectType);
        localStorage.type = this.selectType;
      },
      changeFee() {
        this.loadData(this.selected, this.selDate, this.selType);
        localStorage.feeType = this.selFee;
      }
    }
  }
</script>
