<template>
  <div id='wrapper'>
    <body>
    </body>
  </div>
</template>

<script>
import * as d3 from 'd3'
import fs from 'fs'
export default {

  methods: {
    RandomData () {
      var addToLane = function (chart, item) {
        var name = item.lane
        if (!chart.lanes[name]) {
          chart.lanes[name] = []
        }

        var lane = chart.lanes[name]

        var sublane = 0
        while (isOverlapping(item, lane[sublane])) {
          sublane++
        }
        if (!lane[sublane]) {
          lane[sublane] = []
        }

        lane[sublane].push(item)
      }

      var isOverlapping = function (item, lane) {
        if (lane) {
          for (var i = 0; i < lane.length; i++) {
            var t = lane[i]
            if (item.start < t.end && item.end > t.start) {
              return true
            }
          }
        }
        return false
      }

      var parseData = function (data) {
        let i = 0
        let length = data.length

        var chart = { lanes: {} }

        for (i; i < length; i++) {
          var item = data[i]
          addToLane(chart, item)
        }
        return collapseLanes(chart)
      }

      var collapseLanes = function (chart) {
        var lanes = []
        var items = []
        var laneId = 0
        var now = new Date()

        for (var laneName in chart.lanes) {
          var lane = chart.lanes[laneName]

          console.log('lane.length: ' + lane.length)
          for (var i = 0; i < lane.length; i++) {
            var subLane = lane[i]

            lanes.push({
              id: laneId,
              label: i === 0 ? laneName : ''
            })

            for (var j = 0; j < subLane.length; j++) {
              var item = subLane[j]
              items.push({
                id: item.id,
                lane: laneId,
                start: item.start,
                end: item.end,
                class: item.end > now ? 'future' : 'past',
                desc: item.desc
              })
            }
            laneId++
          }
        }
        console.log(lanes)
        return { lanes: lanes, items: items }
      }

      // var randomNumber = function (min, max) {
      //   return Math.floor(Math.random(0, 1) * (max - min)) + min
      // }

      // var generateRandomWorkItems = function () {
      //   var data = []
      //   let laneCount = randomNumber(5, 7)
      //   let totalWorkItems = randomNumber(20, 30)
      //   let startMonth = randomNumber(0, 1)
      //   let startDay = randomNumber(1, 28)
      //   // let totalMonths = randomNumber(4, 10)

      //   for (var i = 0; i < laneCount; i++) {
      //     let dt = new Date(2012, startMonth, startDay)
      //     for (var j = 0; j < totalWorkItems; j++) {
      //       var dtS = new Date(dt.getFullYear(), dt.getMonth(), dt.getDate() + randomNumber(1, 5), randomNumber(8, 16), 0, 0)

      //       var dateOffset = randomNumber(0, 7)
      //       dt = new Date(dtS.getFullYear(), dtS.getMonth(), dtS.getDate() + dateOffset, randomNumber(dateOffset === 0 ? dtS.getHours() + 2 : 8, 18), 0, 0)

      //       var workItem = {
      //         name: 'work item ' + j,
      //         lane: 'lane ' + i,
      //         start: dtS,
      //         end: dt,
      //         desc: 'This is a description.'
      //       }

      //       data.push(workItem)
      //     }
      //   }
      //   return data
      // }

      var getDateByExcel = function () {
        console.log('111111')
        var testData = fs.readFileSync(__static + '/test.csv').toString()
        var data = d3.csvParse(testData, function (d) {
          // console.log(d)
          return {
            // ID: d.ID,
            name: d.mediaName,
            lane: d.mediaName,
            // ower: d.ower,
            start: new Date(d.startTime),
            end: new Date(d.endTime)
            // updateTime: d.updateTime
            // year: new Date(+d.Year, 0, 1), // convert "Year" column to Date
            // make: d.Make,
            // model: d.Model,
            // length: +d.Length // convert "Length" column to number
          }
        })
        console.log('data :' + data[0].toString())
        return data
      }

      return parseData(getDateByExcel())
      // return getDateByExcel()
    }
  },

  mounted () {
    var data = this.RandomData()
    // console.log(data)
    // var testData = ''
    // var filedata = fs.readFileSync(__static + '/test.csv')

    // testData = filedata.toString()
    // var data = d3.csvParse(testData, function (d) {
    //   console.log('d:' + d)
    //   return {
    //     ID: d.ID,
    //     mediaName: d.mediaName,
    //     screenName: d.screenName,
    //     ower: d.ower,
    //     startTime: d.startTime,
    //     endTime: d.endTime,
    //     updateTime: d.updateTime
    //     // year: new Date(+d.Year, 0, 1), // convert "Year" column to Date
    //     // make: d.Make,
    //     // model: d.Model,
    //     // length: +d.Length // convert "Length" column to number
    //   }
    // })
    // console.log('data :' + data)
    var lanes = data.lanes
    var items = data.items
    var now = new Date()

    var margin = { top: 20, right: 15, bottom: 15, left: 160 }
    var width = 1800 - margin.left - margin.right
    var height = 900 - margin.top - margin.bottom
    var miniHeight = lanes.length * 12 + 50
    var mainHeight = height - miniHeight - 50

    var x = d3.scaleTime()
      .domain([d3.min(items, function (d) { return d.start }), d3.max(items, function (d) { return d.end })])
      .range([0, width])
    var x1 = d3.scaleTime().range([0, width])

    var ext = d3.extent(lanes, function (d) { return d.id })
    var y1 = d3.scaleLinear().domain([ext[0], ext[1] + 1]).range([0, mainHeight])
    var y2 = d3.scaleLinear().domain([ext[0], ext[1] + 1]).range([0, miniHeight])

    var chart = d3.select('body')
      .append('svg:svg')
      .attr('width', width + margin.right + margin.left)
      .attr('height', height + margin.top + margin.bottom)
      .attr('class', 'chart')

    chart.append('defs').append('clipPath')
      .attr('id', 'clip')
      .append('rect')
      .attr('width', width)
      .attr('height', mainHeight)

    var main = chart.append('g')
      .attr('transform', 'translate(' + margin.left + ',' + margin.top + ')')
      .attr('width', width)
      .attr('height', mainHeight)
      .attr('class', 'main')

    var mini = chart.append('g')
      .attr('transform', 'translate(' + margin.left + ',' + (mainHeight + 60) + ')')
      .attr('width', width)
      .attr('height', miniHeight)
      .attr('class', 'mini')

    // draw the lanes for the main chart
    main.append('g').selectAll('.laneLines')
      .data(lanes)
      .enter().append('line')
      .attr('x1', 0)
      .attr('y1', function (d) { return d3.format('o')(y1(d.id)) + 0.5 })
      .attr('x2', width)
      .attr('y2', function (d) { return d3.format('o')(y1(d.id)) + 0.5 })
      .attr('stroke', function (d) { return d.label === '' ? 'white' : 'lightgray' })

    main.append('g').selectAll('.laneText')
      .data(lanes)
      .enter().append('text')
      .text(function (d) { return d.label })
      .attr('x', -10)
      .attr('y', function (d) { return y1(d.id + 0.5) })
      .attr('dy', '0.5ex')
      .attr('text-anchor', 'end')
      .attr('class', 'laneText')

    // draw the lanes for the mini chart
    mini.append('g').selectAll('.laneLines')
      .data(lanes)
      .enter().append('line')
      .attr('x1', 0)
      .attr('y1', function (d) { return d3.format('o')(y2(d.id)) + 0.5 })
      .attr('x2', width)
      .attr('y2', function (d) { return d3.format('o')(y2(d.id)) + 0.5 })
      .attr('stroke', function (d) { return d.label === '' ? 'white' : 'lightgray' })

    mini.append('g').selectAll('.laneText')
      .data(lanes)
      .enter().append('text')
      .text(function (d) { return d.label })
      .attr('x', -10)
      .attr('y', function (d) { return y2(d.id + 0.5) })
      .attr('dy', '0.5ex')
      .attr('text-anchor', 'end')
      .attr('class', 'laneText')
    // draw the x axis
    var xDateAxis = d3.axisBottom(x)
      // .scale(x)
      .ticks(d3.timeHour)
      .tickFormat(d3.timeFormat('%H:%M:%S'))
      .tickSize(6, 0, 0)

    var x1DateAxis = d3.axisBottom(x1)
      // .scale(x1)
      .ticks(d3.timeDay, 1)
      .tickFormat(d3.timeFormat('%Y-%m-%d'))
      .tickSize(6, 0, 0)

    var xMonthAxis = d3.axisTop(x)
      // .scale(x)
      .ticks(d3.timeHour, 1)
      .tickFormat(d3.timeFormat('%H:%M:%S'))
      .tickSize(15, 0, 0)

    var x1MonthAxis = d3.axisTop(x1)
      // .scale(x1)
      .ticks(d3.timeDay, 1)
      .tickFormat(d3.timeFormat('%Y-%m-%d'))
      .tickSize(15, 0, 0)

    main.append('g')
      .attr('transform', 'translate(0,' + mainHeight + ')')
      .attr('class', 'main axis date')
      .call(x1DateAxis)

    main.append('g')
      .attr('transform', 'translate(0,0.5)')
      .attr('class', 'main axis month')
      .call(x1MonthAxis)
      .selectAll('text')
      .attr('dx', 5)
      .attr('dy', 12)

    mini.append('g')
      .attr('transform', 'translate(0,' + miniHeight + ')')
      .attr('class', 'axis date')
      .call(xDateAxis)

    mini.append('g')
      .attr('transform', 'translate(0,0.5)')
      .attr('class', 'axis month')
      .call(xMonthAxis)
      .selectAll('text')
      .attr('dx', 5)
      .attr('dy', 12)
    // draw a line representing today's date

    main.append('line')
      .attr('y1', 0)
      .attr('y2', mainHeight)
      .attr('class', 'main todayLine')
      .attr('clip-path', 'url(#clip)')

    mini.append('line')
      .attr('x1', x(now) + 0.5)
      .attr('y1', 0)
      .attr('x2', x(now) + 0.5)
      .attr('y2', miniHeight)
      .attr('class', 'todayLine')

    // draw the items
    var itemRects = main.append('g')
      .attr('clip-path', 'url(#clip)')

    mini.append('g').selectAll('miniItems')
      .data(getPaths(items))
      .enter().append('path')
      .attr('class', function (d) { return 'miniItem ' + d.class })
      .attr('d', function (d) { return d.path })

    // invisible hit area to move around the selection window
    mini.append('rect')
      .attr('pointer-events', 'painted')
      .attr('width', width)
      .attr('height', miniHeight)
      .attr('visibility', 'hidden')
      // .on('mouseup', moveBrush)

    // console.log('d3.timeMonday(now) : ' + d3.timeMonday(now))
    // draw the selection area
    var brush = d3.brushX()
      // .extent([d3.timeMonday(now), d3.timeSaturday.ceil(now)])
      .extent([[0, 0], [width, height]])
      .on('brush', display)

    mini.append('g')
      .attr('class', 'x brush')
      .call(brush)
      .selectAll('rect')
      .attr('y', 1)
      .attr('height', miniHeight - 1)

    mini.selectAll('rect.background').remove()
    display()

    function display () {
      console.log('+++display+++')
      var s = d3.event.selection
      if (s != null) {
        var rects, labels
        var minExtent = d3.timeMinute(x.invert(s[0]))
        var maxExtent = d3.timeMinute(x.invert(s[1]))
        console.log('s: ' + s)
        var visItems = items.filter(function (d) { return d.start < maxExtent && d.end > minExtent })

        // mini.select('.brush').call(brush.extent([minExtent, maxExtent]))

        x1.domain([minExtent, maxExtent])

        if ((maxExtent - minExtent) > 1468800000) {
          x1DateAxis.ticks(d3.timeMonday, 1).tickFormat(d3.timeFormat('%a %d'))
          x1MonthAxis.ticks(d3.timeMonday, 1).tickFormat(d3.timeFormat('%b - Week %W'))
        } else if ((maxExtent - minExtent) > 172800000) {
          x1DateAxis.ticks(d3.timeDay, 1).tickFormat(d3.timeFormat('%a %d'))
          x1MonthAxis.ticks(d3.timeMonday, 1).tickFormat(d3.timeFormat('%b - Week %W'))
        } else {
          x1DateAxis.ticks(d3.timeHour, 4).tickFormat(d3.timeFormat('%I %p'))
          x1MonthAxis.ticks(d3.timeDay, 1).tickFormat(d3.timeFormat('%b %e'))
        }
        // x1Offset.range([0, x1(d3.time.day.ceil(now) - x1(d3.time.day.floor(now)))]);
        // shift the today line
        main.select('.main.todayLine')
          .attr('x1', x1(now) + 0.5)
          .attr('x2', x1(now) + 0.5)

        // update the axis
        main.select('.main.axis.date').call(x1DateAxis)
        main.select('.main.axis.month').call(x1MonthAxis)
          .selectAll('text')
          .attr('dx', 5)
          .attr('dy', 12)

        // upate the item rects
        rects = itemRects.selectAll('rect')
          .data(visItems, function (d) { return d.id })
          .attr('x', function (d) { return x1(d.start) })
          .attr('width', function (d) { return x1(d.end) - x1(d.start) })

        rects.enter().append('rect')
          .attr('x', function (d) { return x1(d.start) })
          .attr('y', function (d) { return y1(d.lane) + 0.1 * y1(1) + 0.5 })
          .attr('width', function (d) { return x1(d.end) - x1(d.start) })
          .attr('height', function (d) { return 0.8 * y1(1) })
          .attr('class', function (d) { return 'mainItem ' + d.class })

        rects.exit().remove()

        // update the item labels
        labels = itemRects.selectAll('text')
          .data(visItems, function (d) { return d.id })
          .attr('x', function (d) { return x1(Math.max(d.start, minExtent)) + 2 })

        labels.enter().append('text')
          .text(function (d) { return 'Item\n\n\n\n Id: ' + d.id })
          .attr('x', function (d) { return x1(Math.max(d.start, minExtent)) + 2 })
          .attr('y', function (d) { return y1(d.lane) + 0.4 * y1(1) + 0.5 })
          .attr('text-anchor', 'start')
          .attr('class', 'itemLabel')

        labels.exit().remove()
      }
    }

    // function moveBrush () {
    //   console.log('+++moveBrush+++')
    //   var origin = d3.mouse(this)
    //   var point = x.invert(origin[0])
    //   var halfExtent = (brush.extent()[1].getTime() - brush.extent()[0].getTime()) / 2
    //   var start = new Date(point.getTime() - halfExtent)
    //   var end = new Date(point.getTime() + halfExtent)

    //   brush.extent([start, end])
    //   display()
    // }

    // generates a single path for each item class in the mini display
    // ugly - but draws mini 2x faster than append lines or line generator
    // is there a better way to do a bunch of lines as a single path with d3?
    function getPaths (items) {
      var paths = {}
      var d
      var offset = 0.5 * y2(1) + 0.5
      var result = []
      for (var i = 0; i < items.length; i++) {
        d = items[i]
        if (!paths[d.class]) paths[d.class] = ''
        paths[d.class] += ['M', x(d.start), (y2(d.lane) + offset), 'H', x(d.end)].join(' ')
      }

      for (var className in paths) {
        result.push({ class: className, path: paths[className] })
      }

      return result
    }
  }
}
</script>

<style>
.chart {
	shape-rendering: crispEdges;
}

.mini text {
	font: 9px sans-serif;	
}

.main text {
	font: 12px sans-serif;	
}

.month text {
	text-anchor: start;
}

.todayLine {
	stroke: blue;
	stroke-width: 1.5;
}

.axis line, .axis path {
	stroke: black;
}

.miniItem {
	stroke-width: 6;	
}

.future {
	stroke: gray;
	fill: #ddd;
}

.past {
	stroke: green;
	fill: lightgreen;
}

.brush .extent {
	stroke: gray;
	fill: blue;
	fill-opacity: .165;
}
</style>
