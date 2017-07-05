<template>
  <div id="wrapper">
    <img id="logo" src="~@/assets/logo.png" alt="electron-vue">
    <main>
  
      <div class="left-side">
        <span class="title">
          Welcome to your new project!
        </span>
        <system-information></system-information>
  
        <svg width="350" height="160">
          <!-- 60px x 10px margin -->
          <g class="layer" transform="translate(60,10)">
            <!-- cx = 270px * ($X / 3)
                  ^      ^   ^
      width of graph  x-value max(x)
  
            cy = 120px - (($Y / 80) * 120px)
                   ^       ^     ^       ^
        top of graph   y-value  max(y)  scale -->
            <circle r="5" cx="0" cy="105" />
            <circle r="5" cx="90" cy="90" />
            <circle r="5" cx="180" cy="60" />
            <circle r="5" cx="270" cy="0" />
  
            <g class="y axis">
              <line x1="0" y1="0" x2="0" y2="120" />
              <text x="-40" y="105" dy="5">$10</text>
              <text x="-40" y="0" dy="5">$80</text>
            </g>
            <g class="x axis" transform="translate(0, 120)">
              <line x1="0" y1="0" x2="270" y2="0" />
              <text x="-30" y="20">January 2014</text>
              <text x="240" y="20">April</text>
            </g>
          </g>
        </svg>
  
      </div>
  
      <div class="right-side">
        <div class="doc">
          <div class="title">Getting Started</div>
          <p>
            electron-vue comes packed with detailed documentation that covers everything from internal configurations, using the project structure, building your application, and so much more.
          </p>
          <button @click="open('https://simulatedgreg.gitbooks.io/electron-vue/content/')">Read the Docs</button>
          <br>
          <br>
        </div>
        <div class="doc">
          <div class="title alt">Other Documentation</div>
          <button class="alt" @click="open('https://electron.atom.io/docs/')">Electron</button>
          <button class="alt" @click="open('https://vuejs.org/v2/guide/')">Vue.js</button>
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
    d3.selectAll('.hover-me')
      .on('mouseover', function () {
        this.style.backgroundColor = 'yellow'
      })
      .on('mouseleave', function () {
        this.style.backgroundColor = ''
      })
      .on('click', function () {
        this.style.backgroundColor = 'red'
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
