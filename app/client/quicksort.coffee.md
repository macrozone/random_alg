	


# Quicksort

simple quicksort implementation. 
It tends to be faster then the built-in js-implementation in some cases

	@testdata = [1..10e1] 

	@quicksort = (list, first, last) ->
		first = 0 unless first?
		last = list.length-1 unless last?
		if first < last
			length = first + last

take the center as the pivot element
			
			pivotPosition = Math.floor length / 2
			pivotPosition = last-1
			
			pivot = list[pivotPosition]
			i = first
			j = last

			until i > j
				i++ while list[i] < pivot
				j-- while pivot < list[j]
				if i<= j
					temp = list[i]
					list[i] = list[j]
					list[j] = temp
					i++
					j--

			quicksort list, first, j
			quicksort list, i, last


this variant gets a random Pivot element

	@randomQuicksort = (list, first, last) ->
		first = 0 unless first?
		last = list.length-1 unless last?
		if first < last
			length = first + last
			pivotPosition = _.random first, last
			pivot = list[pivotPosition]

			i = first
			j = last

			until i > j
				i++ while list[i] < pivot
				j-- while pivot < list[j]
				if i<= j
					temp = list[i]
					list[i] = list[j]
					list[j] = temp
					i++
					j--

			randomQuicksort list, first, j
			randomQuicksort list, i, last


	plots = [
			{label: "quicksort", visibleAtStartup: true, func:(n) => mesureTime n, quicksort}
			{label: "randomQuicksort", visibleAtStartup: true, func:(n) => mesureTime n, randomQuicksort}
			{label: "quicksort presorted", visibleAtStartup: true, func:(n) => mesureTime n, quicksort, true}
			{label: "randomQuicksort presorted", visibleAtStartup: true, func:(n) => mesureTime n, randomQuicksort, true}
			
to compare, we have a n * log(n) graph, factor is more or less ranom, it may differ on different systems

			{label: "c * n * log(n)", visibleAtStartup: false, func: (n) => 0.00001*n*Math.log n}
			]

	mesureTime = (n, algo, presorted) ->
		data = [0..n]
		if !presorted
			data = Tools.shuffle data

		startTime = new Date().getTime()
		algo data
		endTime = new Date().getTime()
		endTime - startTime


	startExperiment = ->

		turn = =>
			@steps++
		
			for plot, index in plots
				if @chart.series[index].visible
					t = plot.func @n
					@chart.series[index].addPoint [@n, t]

			@n = Math.round @n * @nFactor
			if @running then _.delay turn,50
			
		@running = !@running
		if @running then _.defer turn


	Template.quicksort.events = 
		"click .start": (event, template) ->
			startExperiment.call template.data
		"click .reset": (event, template) ->
			reset.call template.data

	Template.quicksort.rendered = ->
		_.defaults @,data: steps: 0, n:1000,nFactor: 1.25
		initChart.call @
		reset.call @data
			

	initChart = ->
		$chart = $(@find(".chart")).highcharts
			chart:
				height: 600
				zoomType: 'x'
			legend:
				layout: "vertical"
				itemStyle:
					paddingTop: "8px"
					paddingBottom: "8px"
			type: "bar"
			title: "Sortings"
			xAxis:
				type: "logarithmic"
				minorTickInterval: 0.1
				title:
					text: "n"
			yAxis:

				type: "logarithmic"
				title:
					text: "time taken in ms"

		@data.chart = $chart.highcharts()
	
	reset = ->
		@n = 1000
		@steps = 0
		while @chart.series.length > 0 
			@chart.series[0].remove true

		for plot, index in plots
			@chart.addSeries name: plot.label, data:[], visible: plot.visibleAtStartup


		

