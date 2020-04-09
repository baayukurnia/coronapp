<script>
import {Line} from 'vue-chartjs'

export default {
  extends: Line,
  props: ['chartData'],
  methods: {
    render () {

      this.border1 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 100, 400, 0)
      this.background1 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 0, 100, 900)

      this.border1.addColorStop(0, '#333333')
      this.border1.addColorStop(1, '#dd1818')

      this.background1.addColorStop(0, '#dd1818')
      this.background1.addColorStop(0.4, 'transparent')
      
      this.border2 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 100, 400, 0)
      this.background2 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 0, 0, 750)

      this.border2.addColorStop(0, '#4e54c8')
      this.border2.addColorStop(1, '#8f94fb')

      this.background2.addColorStop(0, '#8f94fb')
      this.background2.addColorStop(0.5, 'transparent')
      
      this.border3 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 100, 400, 0)
      this.background3 = this.$refs.canvas.getContext('2d').createLinearGradient(0, 0, 0, 750)

      this.border3.addColorStop(0, '#11998e')
      this.border3.addColorStop(1, '#38ef7d')

      this.background3.addColorStop(0, '#38ef7d')
      this.background3.addColorStop(0.5, 'transparent')

      this.renderChart({
        labels: this.chartData.labels,
        datasets: [
          {
            label: this.chartData.datasets[0].label,
            borderWidth: 3,
            pointHoverBorderWidth: 3,
            pointRadius: 5,
            pointHoverRadius: 10,
            pointHitDetectionRadius: 20,
            pointBackgroundColor: 'transparent',
            pointHoverBackgroundColor: '#fff',
            pointBorderColor: 'transparent',
            pointHoverBorderColor: this.border1,
            borderColor: this.border1,
            backgroundColor: this.background1,
            data: this.chartData.datasets[0].data
          },
          {
            label: this.chartData.datasets[1].label,
            borderWidth: 2,
            pointRadius: 0,
            pointHoverRadius: 5,
            borderDash: [5, 5],
            borderColor: this.border2,
            pointBackgroundColor: this.border2,
            pointBorderColor: this.border2,
            backgroundColor: this.background2,
            data: this.chartData.datasets[1].data
          },
          {
            label: this.chartData.datasets[2].label,
            borderWidth: 2,
            pointRadius: 0,
            pointHoverRadius: 5,
            borderColor: this.border3,
            pointBackgroundColor: this.border3,
            backgroundColor: this.background3,
            data: this.chartData.datasets[2].data
          }
        ]},
         {
            responsive: true,
            maintainAspectRatio: false,
            showScale: true,
            animation: {
              duration: 3000,
              easing: 'easeOutElastic'
            },
            elements: {
                line: {
                    tension: 0.6
                }
            },
            legend: {
              position: 'bottom',
                labels: {
                  defaultFontFamily: 'Open Sans'
                }
            },
            scales: {
              yAxes: [{
                ticks: {
                  fontSize: 12,
                  fontStyle: "bold",
                  mirror: false,
                  fontColor: 'rgba(128,128,128,1)',
                  labelOffset: 0,
                  beginAtZero: false,
                    callback: function(label, index, labels) {
                        return label/1000+'K';
                    }
                },
                gridLines: {
                  drawBorder: false,
                  display: true,
                  color:  'rgba(128,128,128,0.2)',
                  borderDash: [7, 7],
                  lineWidth: 2,
                }
              }],
              xAxes: [ {
                ticks:{
                  labelOffset: -10,
                  fontSize: 14,
                  fontColor: 'rgba(128,128,128,0.5)',
                  maxRotation: 0,
                  minRotation: 0,
                  autoSkip: true,
                  autoSkipPadding: 15,
                },
                type: 'time',
                time: {
                  unit: 'day',
                  tooltipFormat:'D MMM',
                  displayFormats: {
                      day: 'D MMM'
                  }
                },
                gridLines: {
                  display: false,
                  color: 'rgba(128,128,128,0.3)',
                }
              }]
            },
            tooltips: {
              backgroundColor: '#232a31',
              titleFontStyle: 'normal',
              titleFontSize: 18,
              bodyFontFamily: "'Open Sans', sans-serif",
              cornerRadius: 15,
              bodyFontColor: '#fff',
              bodyFontSize: 14,
              xPadding: 14,
              yPadding: 14,
              displayColors: false,
              mode: 'index',
              intersect: false,
            }
        }
      )
    }
  },
  watch: {
    'chartData' (to, from) {
      this.render()
    }
  },
  mounted () {
    this.render()
  }
}
</script>

<style lang="scss" scoped>
canvas#line-chart {
    transform: translateX(20px);
}
</style>