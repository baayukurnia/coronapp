<template>
  <div id="app">
    <div class="header row">
      <div class="container">
        <h1 class="title">Waspada Covid-19
          <span class="titleHastag">#DirumahAja <a href="https://www.instagram.com/p/B-an80nFnLe/">#IkutGiveaway</a></span>
        </h1>
        <toggle-theme></toggle-theme>
      </div>
    </div>
    <div class="container">
      <p class="updatetime">Sumber data : Kementerian Kesehatan & JHU. Update : {{ stats.updated }}</p>
    </div>
    <div v-if="loaded" class="stats">
      <div class="container">
        <div class="stats-container flex-container">
          <Card>
            <h4 class="title">Positif</h4>
            <span class="number">{{ stats.cases }}</span>
            <span class="label red">(+{{ stats.todayCases }}) </span>
            <span class="label">Orang</span>
            <span class="emoji">🤒</span>
          </Card>
          
          <Card>
            <h4 class="title">Meninggal</h4>
            <span class="number">{{ stats.deaths }}</span>
            <span class="label red">(+{{ stats.todayDeaths }}) </span>
            <span class="label">Orang</span>
            <span class="emoji">😵</span>
          </Card>
          
          <Card>
            <h4 class="title">Sembuh</h4>
            <span class="number">{{ stats.recovered }}</span>
            <span class="label">Orang</span>
            <span class="emoji">😇</span>
          </Card>
          
          <Card>
            <h4 class="title">Dites</h4>
            <span class="number">{{ stats.tests }}</span>
            <span class="label">Orang</span>
            <span class="emoji">🌡️</span>
          </Card>
        </div>
      </div>
    </div>
    <div class="container">
      <p class="updatetext">* <strong>{{ stats.testsPerOneMillion }}</strong> dari satu juta penduduk telah dites : <strong>{{ stats.casesPerOneMillion }}%</strong> positif dan <strong>{{ stats.deathsPerOneMillion }}%</strong> meninggal.</p>
    </div>
    <div class="statsHistory">
      <div class="container">
        <h3>Statistik</h3>
        <line-chart v-if="loaded" :chart-data="chartData"/>
      </div>
    </div>
    
    <div class="dataProvinsi">
      <div class="container">
        <h3>Data Daerah</h3>
        <table style="width:100%;text-align:center">
          <tr>
            <th>No</th>
            <th>Provinsi</th>
            <th>Positif</th>
            <th>Sembuh</th>
            <th>Meninggal</th>
          </tr>
          <tr>
            <td>1</td>
            <td>Jakarta</td>
            <td>50</td>
            <td>50</td>
            <td>50</td>
          </tr>
        </table>
      </div>
    </div>

    <footer>
      <div class="container text-center">
      <p>Made with 💙 by <a href="https://instagram.com/baayukurnia">Bayu Kurnia</a> | 🙏 <a href="https://kawalcorona.com/">Ethical Hacker Indonesia</a> & <a href="https://github.com/NovelCovid/API">NovelCOVID-19</a></p>
      </div>
    </footer>
  </div>
</template>

<script>
import axios from "axios";

import ToggleTheme from './components/ToggleTheme.vue'
import Card from './components/Card.vue'
import LineChart from './components/charts/LineChart.vue'

export default {
  name: 'App',
  components: {
    ToggleTheme,
    Card,
    LineChart
  },
  data () {
    return {
      loaded: false,

      country: null,
      stats: {
        updated: null,
        cases: null,
        todayCases: null,
        deaths: null,
        todayDeaths: null,
        recovered: null,
        tests: null,
        casesPerOneMillion: null,
        deathsPerOneMillion: null,
        testsPerOneMillion: null
      },

      provinsi: [],

      chartData: {
        labels: null,
				datasets: [{
					label: '🤒 Positif',
					data: [null],
				}, {
					label: '😵 Meninggal',
					data: null,
				}, {
					label: '😇 Sembuh',
					data: null,
				}]
      }
    }
  },
  mounted() {
    let one =
      "https://corona.lmao.ninja/yesterday/ID";
    let two =
      "https://corona.lmao.ninja/v2/historical/ID?lastdays=30";
    let three =
      "https://api.kawalcorona.com/indonesia/provinsi/";

    const requestOne = axios.get(one);
    const requestTwo = axios.get(two);
    const requestThree = axios.get(three);

    axios
      .all([requestOne, requestTwo, requestThree])
      .then(
        axios.spread((...responses) => {
          const d = responses[0].data;
          const t = responses[1].data.timeline;
          const p = responses[2].data;

          this.chartData.labels = Object.keys(t.cases);
          this.chartData.datasets[0].data = Object.values(t.cases);
          this.chartData.datasets[1].data = Object.values(t.deaths);
          this.chartData.datasets[2].data = Object.values(t.recovered);

          this.country = d.country;
          this.stats.cases = d.cases.toLocaleString();
          this.stats.todayCases = d.todayCases.toLocaleString();
          this.stats.deaths = d.deaths.toLocaleString();
          this.stats.todayDeaths = d.todayDeaths.toLocaleString();
          this.stats.recovered = d.recovered.toLocaleString();
          this.stats.tests = d.tests.toLocaleString();
          this.stats.casesPerOneMillion = d.casesPerOneMillion.toLocaleString();
          this.stats.deathsPerOneMillion = d.deathsPerOneMillion.toLocaleString();
          this.stats.testsPerOneMillion = d.testsPerOneMillion.toLocaleString();
          this.stats.updated = d.updated;

          this.provinsi = p.data;
          this.loaded = true
        })
      )
      .catch(errors => {
        // react on errors.
        console.error(errors);
      });
  }
  }

</script> 

<style lang="scss">
//Typo
$serif: 'Playfair Display';
$sans: 'Open Sans';
// variables
// breakpoint
$sm: 576px;
$md: 768px;
$lg: 992px;
$xl: 1200px;

// Default variables
html {
	--base-font-size: 16px;
}

body {
	--bg-color: #f0f4f6;
	--bg-card: #fff;
	--body-gray-color: #aaa;
	--body-color: #111;
	--body-color-inverse: #fff;
  --primary-color: #01DBFF;
	--secondary-color: #F19769;
	--darker: rgba(0,0,0,0.02);
	--lighter: rgba(255,255,255,0.1);
  --space: 3rem;
  --content-width: 860px;
  --container-width: auto;
	--content-width--large: 1100px;
  --radius: 15px;
}

// Override variables for Dark theme
body[data-theme="dark"] {
 	--bg-color: #191d21;
	--bg-card: #232a31;

  --body-color: #fff;
  
  *{
    box-shadow: none !important;
  }
}

body{
  margin: 0;
  background: var(--bg-color);
  color: var(--body-color);
  overflow-x: hidden;
  font-family: $sans;
  transition: background-color .3s ease,
              color .3s ease,
              fill .3s ease;
}

#app{
  overflow-x: hidden;
}

footer{
  margin-top: 200px;
  p{
    opacity: 0.6;
    font-size: 12px;
    &:hover{
      opacity: 1;
    }
    a{
      text-decoration: none;
      color: var(--body-color);
      &:hover{
        color: var(--primary-color);
      }
    }
  }
}

.text-center{
  text-align: center;
}
.updatetime {
    font-size: 12px;
    opacity: 0.3;
    padding: 0 20px;
}
.updatetext {
    font-size: 12px;
    opacity: 0.8;
    padding: 0 20px;
}
.red{
      color: #dd1818
    }
.stats{
  &-container{
    overflow: auto;
    padding: 20px 0 50px;
  }
  .card{
    width: 100%;
    box-shadow: 0 30px 70px 0 rgba(130,136,171,.1);
    position: relative;
    min-width: 200px;
    margin-left: 20px;
    &:last-child{
        margin-right: 20px;
    }
    &-body{
      padding-right: 50px;
    }
  }
  .title{
    font-weight: 900;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin: 0;
    padding: 0;
    width: 100%;
    line-height: 12px;
    margin-bottom: 10px;
  }
  .number{
    display: block;
    font-weight: 900;
    font-size: 34px;
  }
  .label{
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  .emoji {
    position: absolute;
    right: 10px;
    font-size: 40px;
    top: 50%;
    transform: translateY(-50%);
}
}
h1,h2,h3{
  font-family: $serif;
  font-weight: 700;
}

.header{
  padding: 0 0 30px;
  margin-left: 0 !important;
  margin-right: 0 !important
}
.title{
  line-height: 40px;
  position: relative;
  float: left;
  padding-left: 20px;
  font-size: 1.5rem;

  &Hastag{
    white-space: nowrap;
    font-family: $sans;
    letter-spacing: 1px;
    position: absolute;
    left: 20px;
    top: 30px;
    font-size: 14px;
    opacity: 0.7;
    margin-left: 5px;
    line-height: 45px;
    float: left;
    text-transform: uppercase;
    font-weight: 300;
    a{
      color: var(--primary-color);
      text-decoration: none;
    }
  }
}

.container {
    width: 100%;
    margin-right: auto;
    margin-left: auto;
    h3{
        padding-right: 20px;
        padding-left: 20px;
    }
}

.flex-container{
  display: flex;
}

.statsHistory{
  padding-top: 20px;
  padding-bottom: 50px;
}

@media (min-width: 576px) {
  .title{
  float: left;
  font-size: 2rem;
  }
    .container {
        max-width: 540px;
        padding-right: 15px;
        padding-left: 15px;
    }
}

@media (min-width: 768px) {
    .container {
        max-width: 720px;
    }
}

@media (min-width: 992px) {
    .container {
        max-width: 960px;
    }
}

.container-fluid {
    width: 100%;
    padding-right: 15px;
    padding-left: 15px;
    margin-right: auto;
    margin-left: auto;
}

.row {
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-flex-wrap: wrap;
    -ms-flex-wrap: wrap;
    flex-wrap: wrap;
    margin-right: -15px;
    margin-left: -15px;
}
</style>
