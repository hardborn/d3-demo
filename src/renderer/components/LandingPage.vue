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
          <button @click='open('https://simulatedgreg.gitbooks.io/electron-vue/content/')'>Read the Docs</button>
          <br>
          <br>
        </div>
        <div class='doc'>
          <div class='title alt'>Other Documentation</div>
          <button class='alt' @click='open('https://electron.atom.io/docs/')'>Electron</button>
          <button class='alt' @click='open('https://vuejs.org/v2/guide/')'>Vue.js</button>
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
    var svg = d3.select('svg')
    var margin = { top: 20, right: 20, bottom: 30, left: 50 }
    var width = +svg.attr('width') - margin.left - margin.right
    var height = +svg.attr('height') - margin.top - margin.bottom
    var g = svg.append('g').attr('transform', 'translate(' + margin.left + ',' + margin.top + ')')

    var parseTime = d3.timeParse('%d-%b-%y')

    var x = d3.scaleTime()
      .rangeRound([0, width])

    var y = d3.scaleLinear()
      .rangeRound([height, 0])

    var line = d3.line()
      .x(function (d) { return x(d.date) })
      .y(function (d) { return y(d.close) })

    d3.tsv('./data.tsv', function (d) {
      console.log(d.date)
      console.log(d.close)
      d.date = parseTime(d.date)
      d.close = +d.close
      // console.log(d)
      return d
    }, function (error, data) {
      if (error) throw error

      console.log(data)
      x.domain(d3.extent(data, function (d) { return d.date }))
      y.domain(d3.extent(data, function (d) { return d.close }))

      g.append('g')
        .attr('transform', 'translate(0,' + height + ')')
        .call(d3.axisBottom(x))
        .select('.domain')
        .remove()

      g.append('g')
        .call(d3.axisLeft(y))
        .append('text')
        .attr('fill', '#000')
        .attr('transform', 'rotate(-90)')
        .attr('y', 6)
        .attr('dy', '0.71em')
        .attr('text-anchor', 'end')
        .text('Price ($)')

      g.append('path')
        .datum(data)
        .attr('fill', 'none')
        .attr('stroke', 'steelblue')
        .attr('stroke-linejoin', 'round')
        .attr('stroke-linecap', 'round')
        .attr('stroke-width', 1.5)
        .attr('d', line)
    })
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
</style>
