	


# Quicksort

	@testdata = [1..10e1] 


This function creates a new quicksort with the given pivot function.
The Pivot function takes (first, last) as param and should return
the index of the pivot in the list

	createQuicksortWith = (pivotFunction) ->
		quicksort = (list, first, last) ->
			first = 0 unless first?
			last = list.length-1 unless last?
			if first < last
				i = partition list, first, last, pivotFunction
				quicksort list, first, i-1
				quicksort list, i, last

this creates a quicksort that should prevent stack overflows by using some iteration
See http://de.wikipedia.org/wiki/Quicksort#Speicherplatz for this

it is much slower than the recursive one, though.

We use it for our mesurse, because the other has a stack on large n

	createStackSaveQuicksortWith = (pivotFunction) ->
		quicksort = (list, first, last) ->
			first = 0 unless first?
			last = list.length-1 unless last?
			while last > first
				i = partition list, first, last, pivotFunction
				if i-first < last-i
					quicksort list, first, i-1
					first++
				else
					quicksort list, i, last
					last = i-1




This is the partition function, it uses the given pivot function 

	partition = (list, first, last, getPivotPosition) ->
		
		pivot = list[getPivotPosition first, last]
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
		return i


This is a quicksort, that takes the last element in the list as the pivot element
	
	@quicksort = createStackSaveQuicksortWith (first, last) -> last-1

this variant gets a random Pivot element

	@randomQuicksort = createStackSaveQuicksortWith (first, last) -> _.random first, last



	plots = [
		 	{label: "quicksort", color: "#7EBBE6",visibleAtStartup: true, func:(n) => mesureTime n, quicksort}
		 	{label: "quicksort presorted", color: "#4D738F", visibleAtStartup: true, func:(n) => mesureTime n, quicksort, true}
		 	{label: "randomQuicksort", color: "#F3514B", visibleAtStartup: true, func:(n) => mesureTime n, randomQuicksort}
		 	{label: "randomQuicksort presorted", color: "#8F221C", visibleAtStartup: true, func:(n) => mesureTime n, randomQuicksort, true}
			
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
		_.defaults @,data: steps: 0, n:1000,nFactor: 1.1
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

				#type: "logarithmic"
				title:
					text: "time taken in ms"

		@data.chart = $chart.highcharts()
	
	reset = ->
		@n = 1000
		@steps = 0
		while @chart.series.length > 0 
			@chart.series[0].remove true

		for plot, index in plots
			@chart.addSeries name: plot.label, color: plot.color, data:[], visible: plot.visibleAtStartup


		

