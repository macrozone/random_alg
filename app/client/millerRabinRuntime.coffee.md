
	COLOR_PROBABILITY = "#AA4643"
	COLOR_RUNTIME = "#89A54E"
	BIG_INT_2 = BigInt.int2bigInt 2,1

	initChart = (template)->
		console.log template
		$chart = $(template.find(".chart")).highcharts
			chart:
				height: 600
				zoomType: 'x'
			legend:
				layout: "vertical"
				itemStyle:
					paddingTop: "8px"
					paddingBottom: "8px"
			type: "bar"
			title: "Miller rabin"
			xAxis:
				title:
					text: "length of number tested (in base 2)"
			yAxis: 
				min: 0, 
				title: 
					text: "time taken in ms", style: color: COLOR_RUNTIME
				
					
		chart = $chart.highcharts()

		chart.addSeries name: "runtime", data:[],color: COLOR_RUNTIME
		template.data.chart = chart
	

	getTime = -> new Date().getTime()

	startExperiment = ->

		turn = =>
			@steps++
			
			candidate = BigInt.randBigInt @n, 1


			candidate_minus_1 = BigInt.addInt candidate, -1
			a = Tools.bigIntRandBetween BIG_INT_2, candidate_minus_1
		
			startTime = getTime()
			result = BigInt.millerRabin candidate,a
			endTime = getTime()
			runtime = endTime - startTime
		
			@chart.series[0].addPoint 
				x: @n 
				y: runtime
				name: "<strong>Number tested:</strong> <br />"+(Tools.chunkString BigInt.bigInt2str(a, 10),20).join("<br />")

			@n += @nStep
			if @running then _.delay turn,50
			


		@running = !@running
		if @running then _.defer turn
		

		



	
	Template.millerRabinRuntime.events = 
		"click .start": (event, template) ->
			
			startExperiment.call template.data

	Template.millerRabinRuntime.rendered = ->
		_.defaults @,data: steps: 0, n:10,nStep:10
		initChart this