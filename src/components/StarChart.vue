<template>
  <div class="star-chart">
    <svg v-bind:viewBox="viewBoxDimensions">
      <g v-bind:transform="globalContainerTransform">
        <g class="circles">
          <circle v-for="circle in circles" v-bind:cx="circle.cx" v-bind:cy="circle.cy" v-bind:r="circle.r"/>
        </g>
        <g class="rays">
          <line v-for="line in rays" v-bind:x1="line.x1" v-bind:y1="line.y1"
                v-bind:x2="line.x2"  v-bind:y2="line.y2" v-bind:style="line.style"></line>
        </g>
        <g class="data-area">
          <polygon v-bind:points="dataArea"/>
        </g>
        <g class="data">
          <circle v-for="dot in dataCoordinates" v-bind:cx="dot.cx" v-bind:cy="dot.cy" v-bind:r="dot.r"
                  v-bind:fill="dot.fill"/>
        </g>
      </g>
    </svg>
  </div>
</template>

<script type="text/babel">
  export default {

    name: 'StarChart',

    data () {
      return {
        viewBox: {
          x1: null,
          y1: null,
          x2: null,
          y2: null
        },
        chartSize: null,
        chartCoordinates: {
          x: null,
          y: null
        },
        circles: [],
        rays: [],
        minValue: null,
        maxValue: null,
        step: null,
        chartData: null, // Array of { label: '', value: 0, color: '#hexa' } (maybe label not needed)
        dataCoordinates: []
      }
    },

    computed: {

      viewBoxDimensions () {
        return this.viewBox.x1 + ' ' + this.viewBox.y1 + ' ' + this.viewBox.x2 + ' ' + this.viewBox.y2
      },

      globalContainerTransform () {
        return 'translate(' + this.viewBox.x2 / 2 + ',' + this.viewBox.y2 / 2 + ')'
      },

      dataArea () {
        let i = 0
        let dataArea = ''

        while (i < this.dataCoordinates.length) {
          dataArea += ' ' + this.dataCoordinates[i].cx + ', ' + this.dataCoordinates[i].cy
          i++
        }

        return dataArea
      }

    },

    created () {
      this.viewBox.x1 = this.viewBox.y1 = 0
      this.viewBox.x2 = this.viewBox.y2 = 1000
      this.step = 1
      this.minValue = 0
      this.maxValue = 5
      this.chartSize = 300
      this.chartCoordinates.x = 0
      this.chartCoordinates.y = 0

      this.chartData = [
        { label: 'val1', color: 'red', value: 2 },
        { label: 'val2', color: 'lightblue', value: 4 },
        { label: 'val3', color: 'lightgreen', value: 5 },
        { label: 'val4', color: 'red', value: 5 },
        { label: 'val5', color: 'lightblue', value: 4 },
        { label: 'val6', color: 'lightgreen', value: 4 },
        { label: 'val7', color: 'red', value: 5 },
        { label: 'val8', color: 'lightblue', value: 4 },
        { label: 'val9', color: 'lightgreen', value: 4 },
        { label: 'val10', color: 'red', value: 5 },
        { label: 'val11', color: 'lightblue', value: 5 },
        { label: 'val12', color: 'lightgreen', value: 4 }
      ]

      this.createCircles()
      this.createRays()
      this.populateChart()
    },

    methods: {
      createCircles: function () {
        const nbCircles = (this.maxValue - this.minValue) / this.step
        const step = this.chartSize / nbCircles

        let i = 0
        let circleSize = this.chartSize

        while (i < nbCircles) {
          this.circles.push({
            cx: this.chartCoordinates.x,
            cy: this.chartCoordinates.y,
            r: circleSize
          })
          circleSize -= step
          i++
        }
      },

      createRays () {
        const nbRays = this.chartData.length
        const fullCircleRadAngle = this.degreeToRadian(360)
        const stepAngle = fullCircleRadAngle / nbRays

        let i = 0
        let x = this.chartCoordinates.x
        let y = this.chartSize
        let rayAngle = this.degreeToRadian(180)

        while (i < nbRays) {
          x = (this.chartSize * Math.sin(rayAngle)) + this.chartCoordinates.x
          y = (this.chartSize * Math.cos(rayAngle)) + this.chartCoordinates.y

          this.rays.push({
            x1: this.chartCoordinates.x,
            y1: this.chartCoordinates.y,
            x2: x,
            y2: y,
            angle: rayAngle
          })

          rayAngle -= stepAngle
          i++
        }
      },

      populateChart () {
        const nbSteps = (this.maxValue - this.minValue) / this.step
        let i = 0
        let ray = null
        let x = null
        let y = null

        while (i < this.chartData.length) {
          ray = (this.chartSize / nbSteps) * this.chartData[i].value

          x = (ray * Math.sin(this.rays[i].angle)) + this.chartCoordinates.x
          y = (ray * Math.cos(this.rays[i].angle)) + this.chartCoordinates.y

          this.dataCoordinates.push({
            cx: x,
            cy: y,
            r: 7,
            fill: this.chartData[i].color
          })
          i++
        }
      },

      // To put in a math utils
      degreeToRadian (degree) {
        return degree * (Math.PI / 180)
      },

      radianToDegree (radian) {
        return radian / (Math.PI / 180)
      }
    }

  }
</script>

<style lang="css">
  body {
    background: #1a3455;
  }

  .star-chart{
    width: 50%;
    height: 50%;
  }
  svg {
    width: 100%;
    height: 100%;
    border: 1px solid black;
  }

  .circles circle {
    fill: rgb(205, 205, 205);
    stroke: rgb(205, 205, 205);
    fill-opacity: 0.1;
  }

  line {
    stroke: white;
    stroke-width: 1px;
  }

  .data-area {
    /*fill: rgb(245, 245, 245);*/
    fill: rgb(239, 2, 2);
    fill-opacity: 0.7;
  }

</style>
