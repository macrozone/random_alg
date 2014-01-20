
	BIG_INT_2 = BigInt.int2bigInt 2,1
	
	BASE = 10

	steps = 0
	$chart = null
	chart = null
	candidate = null
	candidate_minus_1 = null
	cumulatedRuntime = 0


	RESULT_COMPOSITE = 0
	RESULT_PROBABLY_PRIME = 1


	getTime = -> new Date().getTime()

	initWithStringCandidate = (aString) ->
		initWithCandidate BigInt.str2bigInt aString,BASE

	initWithCandidate = (n)->
		chart.series[0].setData []
		chart.series[1].setData []
		chart.series[2].setData []
		cumulatedRuntime = 0
		steps = 0
		candidate = n
		candidate_minus_1 = BigInt.addInt candidate, -1


	probability = (steps) ->
		Math.pow 4,-steps



from and to are bigInts too

	bigIntRandBetween = (from, to) ->

		diff = BigInt.addInt(BigInt.sub(to, from),1)
		bits = BigInt.bitSize diff
		rand = BigInt.randBigInt bits
		rand =  BigInt.mod rand, diff
		return BigInt.add rand, from


	doStep = (event, template)->
		steps++
		a = bigIntRandBetween BIG_INT_2, candidate_minus_1

		startTime = getTime()
		result = BigInt.millerRabin candidate,a
		endTime = getTime()
		runtime = endTime - startTime
		cumulatedRuntime += runtime
		switch result
			when RESULT_COMPOSITE 
				resultText = "composite"
				probabilityForComposite = 1
			when RESULT_PROBABLY_PRIME
				resultText = "probablyPrime"
				probabilityForComposite = probability steps

		$(template.find(".victim")).text BigInt.bigInt2str a,BASE
		$(template.find(".result")).text resultText
		
		chart.series[0].addPoint probabilityForComposite
		chart.series[1].addPoint runtime
		chart.series[2].addPoint cumulatedRuntime



	initChart = (template)->
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
			title: "Sortings"
			xAxis:
			#	type: "logarithmic"
				minorTickInterval: 0.1
				title:
					text: "n"
			yAxis: [ 
				(opposite: true, title: text: "probability")
				(title: text: "time taken in ms")
				]
				
					
		chart = $chart.highcharts()

		chart.addSeries name: "probability of composite", data:[],yAxis:0
		chart.addSeries name: "runtime", data:[],yAxis:1
		chart.addSeries name: "cumulated runtime", data:[],yAxis:1


	Template.millerRabin.rendered = ->
		initChart this

	Template.millerRabin.events = 
		"click .step": doStep
		"change .candidate": (event)->
			initWithStringCandidate $(event.target).val()


	

