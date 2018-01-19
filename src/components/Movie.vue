<template>
  <div class="movie">
    <loading :isOpen="isOpen"></loading>
    <header>
      <select v-model="selectCity" @change="changeCity">
        <option v-for="city in cityList" :value="city.id">{{city.name}}</option>
      </select>
      <select v-model="selectMovieType" @change="changeMovieType">
        <option value="in_theaters">正在上映</option>
        <option value="coming_soon">即将上映</option>
      </select>
      共{{total}}部


    </header>

    <div class="info_warp">
      <div v-for="movie in arr">
        <a :href="movie.alt">
          <img :src="movie.images.medium" :alt="movie.title">
          <div class="right_area">
            <h3 class="info_title">{{movie.title}}</h3>
            <span
              :class="{'high': movie.rating.average >= 8, 'low': movie.rating.average <= 6}">{{movie.rating.average | handleNoStar}}</span>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .movie {
    overflow: hidden;
  }
  header {
    text-align: center;
    padding: 1rem 0;
    background: #34495E;
    color: #fff;
    letter-spacing: 1px;
    position: fixed;
    width:100%;
  }
  select {
    appearance:none;
    -webkit-appearance:none;
    -webkit-appearance: none;
    background: none;
    border: none;
    color: #fff;
    font-size: 16px;
    -webkit-tap-highlight-color: rgba(0,0,0,0);
    -webkit-tap-highlight-color:transparent;
    outline:none;
  }
  .info_warp {
    margin: 3.3rem 0;
  }
  .info_warp div a {
    display: block;
    overflow: hidden;
    padding: 1rem 1rem 1rem 0;
    text-decoration: none;
    border-bottom: 1px solid #BDC3C7;
    background: #ECF0F1;
  }
  .info_warp div a img {
    display: inline-block;
    float: left;
    margin:0 2rem;
    width: 30%;
  }
  .info_warp div .right_area {
    color: #2C3E50;
  }
  .high {
    color:#FF4949;
  }
  .low {
    color: #13CE66;
  }
  .right span {
    font-size: 1.4rem;
  }
</style>

<script>
  import loading from './Loading'
  export default {
    name: "movie",
    data () {
      return {
        isOpen: false,
        cityList: [],
        arr: [],
        total: 0,
        selectCity: localStorage.city || 108288,
        selectMovieType: localStorage.movieType || 'in_theaters'
      }
    },
    components: {loading},
    mounted () {
      this.loadCityList();
      this.loadData(this.selectCity, this.selectMovieType);
    },
    methods: {
      loadCityList () {
        this.$http.jsonp(
          'https://api.douban.com/v2/loc/list',
          {
            params: {
              count: 48
            }
          }
        ).then(function (res) {
          this.cityList = res.body.locs;
        })
      },
      loadData (cityId, type) {
        this.isOpen = true;
        this.$http.jsonp(
          'https://api.douban.com/v2/movie/' + type,
          {
            params: {
              city: cityId,
              count: 100
            }
          }
        ).then(function (res) {
          let data = res.body;
          this.arr = this.handleData(data.subjects, type);
          this.isOpen = false;
        })
      },
      handleData (data, type) {
        let filterArr = [];
        if (type === 'in_theaters') {
          filterArr = data.sort(function (a, b) {
            return b.rating.average - a.rating.average
          })
        } else {
          filterArr = data;
        }
        this.total = filterArr.length;
        return filterArr;
      },
      changeCity() {
        this.loadData(this.selectCity, this.selectMovieType);
        localStorage.city = this.selectCity;
      },
      changeMovieType() {
        this.loadData(this.selectCity, this.selectMovieType);
        localStorage.movieType = this.selectMovieType;
      }
    },
    filters: {
      handleNoStar(v) {
        if (v === 0) {
          return '暂无评分'
        } else {
          return v
        }
      }
    }
  }
</script>
