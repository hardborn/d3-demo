<template>
  <div id='wrapper'>
    <img id='logo' src='~@/assets/logo.png' alt='electron-vue'>
    <main>
  
      <div class='left-side'>
        <span class='title'>
          Welcome to your new project!
        </span>
        <system-information></system-information>
  
        <svg width='960' height='500'></svg>
  
      </div>
  
      <div class='right-side'>
        <div class='doc'>
          <div class='title'>Getting Started</div>
          <p>
            electron-vue comes packed with detailed documentation that covers everything from internal configurations, using the project structure, building your application, and so much more.
          </p>
          <button @click='open(" https://simulatedgreg.gitbooks.io/electron-vue/content/ ")'>Read the Docs</button>
          <br>
          <br>
        </div>
        <div class='doc'>
          <div class='title alt'>Other Documentation</div>
          <button class='alt' @click='open(" https://electron.atom.io/docs/ ")'>Electron</button>
          <button class='alt' @click='open(" https://vuejs.org/v2/guide/ ")'>Vue.js</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import SystemInformation from './LandingPage/SystemInformation'
import * as d3 from 'd3'
// import {JSDOM} from 'jsdom'

export default {
  name: 'landing-page',
  components: { SystemInformation },
  methods: {
    open (link) {
      this.$electron.shell.openExternal(link)
    }
  },
  mounted () {
    // var document = new JSDOM()
    // console.log(document)
    var data = d3.range(1000).map(d3.randomBates(10))

    var formatCount = d3.format(',.0f')

    var svg = d3.select('svg')
    var margin = { top: 10, right: 30, bottom: 30, left: 30 }
    var width = +svg.attr('width') - margin.left - margin.right
    var height = +svg.attr('height') - margin.top - margin.bottom
    var g = svg.append('g').attr('transform', 'translate(' + margin.left + ',' + margin.top + ')')

    var x = d3.scaleLinear()
      .rangeRound([0, width])

    var bins = d3.histogram()
      .domain(x.domain())
      .thresholds(x.ticks(20))(data)

    var y = d3.scaleLinear()
      .domain([0, d3.max(bins, function (d) { return d.length })])
      .range([height, 0])

    var bar = g.selectAll('.bar')
      .data(bins)
      .enter().append('g')
      .attr('class', 'bar')
      .attr('transform', function (d) { return 'translate(' + x(d.x0) + ',' + y(d.length) + ')' })

    bar.append('rect')
      .on('mouseover', function () {
        this.style.fill = 'red'
      })
      .on('mouseleave', function () {
        this.style.fill = ''
      })
      .attr('x', 1)
      .attr('width', x(bins[0].x1) - x(bins[0].x0) - 1)
      .attr('height', function (d) { return height - y(d.length) })

    bar.append('text')
      .attr('dy', '.75em')
      .attr('y', 6)
      .attr('x', (x(bins[0].x1) - x(bins[0].x0)) / 2)
      .attr('text-anchor', 'middle')
      .text(function (d) { return formatCount(d.length) })

    g.append('g')
      .attr('class', 'axis axis--x')
      .attr('transform', 'translate(0,' + height + ')')
      .call(d3.axisBottom(x))
  }
}

</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Source Sans Pro', sans-serif;
}

#wrapper {
  background: radial-gradient( ellipse at top left,
  rgba(255, 255, 255, 1) 40%,
  rgba(229, 229, 229, .9) 100%);
  height: 100vh;
  padding: 60px 80px;
  width: 100vw;
}

#logo {
  height: auto;
  margin-bottom: 20px;
  width: 420px;
}

main {
  display: flex;
  justify-content: space-between;
}

main>div {
  flex-basis: 50%;
}

.left-side {
  display: flex;
  flex-direction: column;
}

.welcome {
  color: #555;
  font-size: 23px;
  margin-bottom: 10px;
}

.title {
  color: #2c3e50;
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 6px;
}

.title.alt {
  font-size: 18px;
  margin-bottom: 10px;
}

.doc p {
  color: black;
  margin-bottom: 10px;
}

.doc button {
  font-size: .8em;
  cursor: pointer;
  outline: none;
  padding: 0.75em 2em;
  border-radius: 2em;
  display: inline-block;
  color: #fff;
  background-color: #4fc08d;
  transition: all 0.15s ease;
  box-sizing: border-box;
  border: 1px solid #4fc08d;
}

.doc button.alt {
  color: #42b983;
  background-color: transparent;
}

.red {
  fill: red;
  /* not background-color! */
}

.fancy {
  fill: none;
  stroke: black;
  /* similar to border-color */
  stroke-width: 3pt;
  /* similar to border-width */
  stroke-dasharray: 3, 5, 10;
}

.bar rect {
  fill: steelblue;
}

.bar text {
  fill: orangered;
  font: 10px sans-serif;
}
</style>
