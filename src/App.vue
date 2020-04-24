<template>
  <div id="app">
    <div class="header row">
      <div class="container">
        <h1 class="title">Waspada Covid-19
          <span class="titleHastag">#DirumahAja</span>
        </h1>
        <div class="buttons">
          <toggle-theme></toggle-theme>
          <button role="button" @click.prevent="reload" class="reload" :class="{ rotating: !loaded }">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-rotate-cw"><polyline points="23 4 23 10 17 10"></polyline><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"></path></svg>
          </button>
        </div>
      </div>
    </div>
    <transition name="fade">
      <div v-if="!loaded || !online" class="container message">
        <p class="updatetext text-center">{{ message }}</p>
      </div>
    </transition>
    <main>
      <div v-if="loaded">
        <div class="container">
          <p class="updatetime">Sumber data : Kementerian Kesehatan, JHU, Worldometers <br/> Update : {{ stats.updated }}</p>
        </div>
        <div class="stats">
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
                <h4 class="title">Sembuh</h4>
                <span class="number">{{ stats.recovered }}</span>
                <span class="label">Orang</span>
                <span class="emoji">😇</span>
              </Card>
              
              <Card>
                <h4 class="title">Meninggal</h4>
                <span class="number">{{ stats.deaths }}</span>
                <span class="label red">(+{{ stats.todayDeaths }}) </span>
                <span class="label">Orang</span>
                <span class="emoji">😵</span>
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
        <div class="statsHistory">
          <div class="container">
            <h3>Statistik</h3>
            <ul class="chartRange">
              <li :class="{ active: inWeek }" @click.prevent="inWeek = true; setInWeek()">
                7 Hari
              </li>
              <li :class="{ active: !inWeek }" @click.prevent="inWeek = false; setInMonth()">
                30 Hari
              </li>
            </ul>
            <line-chart :chart-data="datacollection" :height="330"  ref="chart"/>
          </div>
        </div>
        
        <div class="dataProvinsi">
          <div class="container">
            
            <h3 class="text-center">Data Daerah</h3>
            <p class="text-center">*urut kasus terbanyak</p>
            <table>
                <thead>
                  <tr>
                    <th scope="col">Provinsi</th>
                    <th scope="col"><span class="only-sm">🤒</span><span class="hidden-sm">Positif</span></th>
                    <th scope="col"><span class="only-sm">😇</span><span class="hidden-sm">Sembuh</span></th>
                    <th scope="col"><span class="only-sm">😵</span><span class="hidden-sm">Meninggal</span></th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="p in provinsi" :key="p.FID">
                    <td>{{ p.attributes.Provinsi }}</td>
                    <td class="red">{{ p.attributes.Kasus_Posi.toLocaleString() }}</td>
                    <td class="green">{{ p.attributes.Kasus_Semb.toLocaleString() }}</td>
                    <td class="purple">{{ p.attributes.Kasus_Meni.toLocaleString() }}</td>
                  </tr>
                </tbody>
            </table>
          </div>
        </div>
      </div>
    </main>
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
      online: null,
      loaded: false,
      message: "Memuat data..",
      
      inWeek: false,

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
      datacollection: {
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
  methods: {
    setInWeek() {
      var i;
      for(i=0;i<=2;i++){
        let x = this.datacollection.datasets[i].data
        this.datacollection.datasets[i].data = x.slice(Math.max(x.length - 7, 1))
      }
      let x = this.datacollection.labels
      this.datacollection.labels = x.slice(Math.max(x.length - 7, 1))
      
      this.$refs.chart.render()
    },
    setInMonth() {
      const t = JSON.parse(localStorage.getItem("t"));
      this.datacollection.labels = Object.keys(t.cases)
      this.datacollection.datasets[0].data = Object.values(t.cases)
      this.datacollection.datasets[1].data = Object.values(t.deaths)
      this.datacollection.datasets[2].data = Object.values(t.recovered)
      this.$refs.chart.render()
    },
    reload() {
      this.message = "Memuat ulang data.."
      this.loaded = false
      this.fetchData()
      this.inWeek = false
      this.$refs.chart.render()
    },
    fetchData() {
      let one =
        "https://corona.lmao.ninja/v2/countries/ID?yesterday=false";
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
            const d = responses[0].data
            const t = responses[1].data.timeline
            const p = responses[2].data
            // const kx = responses[3].data.filter(function (el) {return el.attributes.Country_Region === 'Indonesia' })
            
            // const k = kx[0].attributes
            
            localStorage.setItem("d", JSON.stringify(d));
            localStorage.setItem("t", JSON.stringify(t));
            localStorage.setItem("p", JSON.stringify(p));
            // localStorage.setItem("k", JSON.stringify(k));

            this.datacollection.labels = Object.keys(t.cases)
            this.datacollection.datasets[0].data = Object.values(t.cases)
            this.datacollection.datasets[1].data = Object.values(t.deaths)
            this.datacollection.datasets[2].data = Object.values(t.recovered)

            this.country = d.country
            this.stats.cases = d.cases.toLocaleString()
            this.stats.todayCases = d.todayCases.toLocaleString()
            this.stats.deaths = d.deaths.toLocaleString()
            this.stats.todayDeaths = d.todayDeaths.toLocaleString()
            this.stats.recovered = d.recovered.toLocaleString()
            this.stats.tests = d.tests.toLocaleString()
            this.stats.casesPerOneMillion = d.casesPerOneMillion
            this.stats.deathsPerOneMillion = d.deathsPerOneMillion
            this.stats.testsPerOneMillion = d.testsPerOneMillion
            this.stats.updated = new Date(d.updated)

            this.provinsi = p
            
            this.online = true
            this.loaded = true
          })
        )
        .catch(errors => {
          this.message = "Sepertinya anda sedang offline. Memuat data lawas.."
          this.online = false
          console.error(errors)
          this.loadSaved()
        });
    },
    loadSaved() {
        if (localStorage.d) {
          const d = JSON.parse(localStorage.getItem("d"));
          const t = JSON.parse(localStorage.getItem("t"));
          const p = JSON.parse(localStorage.getItem("p"));
          const k = JSON.parse(localStorage.getItem("k"));
          
          this.datacollection.labels = Object.keys(t.cases)
          this.datacollection.datasets[0].data = Object.values(t.cases)
          this.datacollection.datasets[1].data = Object.values(t.deaths)
          this.datacollection.datasets[2].data = Object.values(t.recovered)

          this.country = k.Country_Region
          this.stats.cases = k.Confirmed.toLocaleString()
          this.stats.todayCases = d.todayCases.toLocaleString()
          this.stats.deaths = k.Deaths.toLocaleString()
          this.stats.todayDeaths = d.todayDeaths.toLocaleString()
          this.stats.recovered = k.Recovered.toLocaleString()
          this.stats.tests = d.tests.toLocaleString()
          this.stats.casesPerOneMillion = d.casesPerOneMillion
          this.stats.deathsPerOneMillion = d.deathsPerOneMillion
          this.stats.testsPerOneMillion = d.testsPerOneMillion
          this.stats.updated = new Date(k.Last_Update)

          this.provinsi = p

          this.loaded = true
      }
    },
  },
  mounted() {
    this.fetchData()
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
    -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Opera and Firefox */
}

body {
	--bg-color: #f0f4f6;
	--bg-card: #fff;
	--body-gray-color: #aaa;
	--body-color: #111;
	--border-color: rgba(0,0,0,0.1);
	--body-color-inverse: #fff;
  --primary-color: #01DBFF;
	--secondary-color: #F19769;
	--darker: rgba(0,0,0,0.02);
	--th: rgb(235, 239, 241);
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
  --darker: rgba(0,0,0,0.1);
  --th: rgba(22, 26, 30, 1);

	--border-color: rgba(255,255,255,0.1);
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
  transition: .3s ease
}

::-webkit-scrollbar {
  width: 2px;
  height: 2px;
}
 
::-webkit-scrollbar-track {
  box-shadow: none
}
 
::-webkit-scrollbar-thumb {
  background-color: slategrey;
  outline: 1px solid slategrey;
  border-radius: 10px;
}

#app{
  overflow-x: hidden;
}

main{
  min-height: 35vh;
}
footer{
  margin-top: 50px;
  p{
    padding: 0 20px;
    opacity: 0.3;
    font-size: 10px;
    transition: 0.3s;
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
    font-size: 10px;
    opacity: 0.3;
    padding: 0 20px;
}
.updatetext {
    font-size: 12px;
    opacity: 0.8;
    padding: 0 20px;
    color: #e8a307;
}
.red{
      color: #dd1818
    }
.chartRange {
    margin: 0;
    padding: 0;
    position: absolute;
    top: 0;
    right: 15px;
    list-style: none;
    li{
      float: left;
      padding: 6px 12px;
      border-radius: 10px;
      font-size: 12px;
      font-weight: bold;
      border: 1px solid transparent;
      cursor: pointer;

      &:hover{
        opacity: 0.7;
      }

      &:not(:last-child){
        margin-right: 10px;
      }
      &.active{
        color: var(--primary-color);
        border-color: var(--primary-color)
      }
    }
}
.buttons{
    float: right;
    margin-top: 25px;
    margin-right: 10px;
    svg{
      width: 20px;
    }
    }
.reload {
    margin-left: 5px;
    transition: .3s;
    padding: 10px;
    background: none;
    border: 0;
    cursor: pointer;
    color: var(--body-color);
  &:hover{
    opacity: 0.7;
  }
  &.rotating{
    svg{
    animation: rotation 0.5s infinite linear;
    }
  }
}
.stats{
  &-container{
    overflow: auto;
    padding: 30px 0 20px;
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
    font-size: 28px;
  }
  .label{
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  .emoji {
    position: absolute;
    right: 15px;
    font-size: 40px;
    top: 50%;
    transform: translateY(-50%);
}
}
h1,h2,h3, .dataProvinsi caption{
  font-family: $serif;
  font-weight: 700;
}

.dataProvinsi{
  h3{
    margin: 0;
  }
  p{
    margin: 10px 0 15px;
    font-size: 12px;
    font-weight: 400;
    color: var(--body-color);
    opacity: 0.3;
    font-family: $sans;
  }
}
.header{
  padding: 30px 0 15px;
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
  .container{
    position: relative;
    h3{
      line-height: 30px;
    }
  }
}

@media (min-width: 576px) {
  .buttons{
    margin-top: 35px;
  }
  .title{
  float: left;
  font-size: 2rem;
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

table {
  border-collapse: collapse;
  margin: 0;
  padding: 0;
  width: 100%;
  table-layout: fixed;

  .red{
    color: #f54747;
  }
  .green{
    color: #3bce8a;
  }
  .purple{
    color: #8d64b3;
  }
  tbody{
  background-color: var(--bg-card);
  }
}

table caption {
  font-size: 1.5em;
  margin: .5em 0 .75em;
}

table tr {
  padding: .35em;
  &:nth-child(odd){
    background: var(--darker);
  }
  &:last-child{
    td{
      padding-bottom: 30px;
    }
  }
}

table th,
table td {
  padding: .625em;
  text-align: left;
  &:first-child{
    padding-left: 20px;
  }
}
thead{
  border-radius: 20px 20px 0 0;
    tr th{
      background: var(--th);
    }
    th:first-child{
      width: 50%;
    }
}
table th {
  font-size: .85em;
  letter-spacing: .1em;
  text-transform: uppercase;
  position: sticky;
  top: 0;
}
@media screen and (max-width: 350px) {
.buttons{
  margin-top: 15px;
    svg{
      width: 18px;
    }
    button{
      margin: 0 !important;
      padding: 5px 10px !important;
      display: block
    }
  }
}
@media screen and (min-width: $md) {
  .only-sm{
    display: none;
  }
}
@media screen and (max-width: $md) {
  .header{
    text-align: center;
  }
  .title{
    &Hastag{
      font-size: 12px;
    }
  }
  table{
    font-size: 14px;
  }
  .hidden-sm{
    display: none;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

@keyframes rotation {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(359deg);
  }
}
</style>
