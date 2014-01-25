
	BIG_INT_2 = BigInt.int2bigInt 2,1
	COLOR_PROBABILITY = "#AA4643"
	COLOR_RUNTIME = "#89A54E"
	BASE = 10

	steps = 0
	$chart = null
	chart = null
	candidate = null
	candidate_minus_1 = null
	cumulatedRuntime = 0


	RESULT_COMPOSITE = 0
	RESULT_PROBABLY_PRIME = 1
	Session.set "mr_candidateString", ""
	Session.set "mr_latestResult", ""
	Session.set "mr_latestRuntime", ""
	Session.set "mr_cumulatedRuntime", ""
	Session.set "mr_witness", ""
	Session.set "mr_probabilityForComposite", ""
	Session.set "mr_steps", 0

	getTime = -> new Date().getTime()

	initWithStringCandidate = (aString) ->
		aString = aString.replace(/\D/g,'');
		initWithCandidate BigInt.str2bigInt aString,BASE

	initWithCandidate = (n)->
		serie.setData [] for serie in chart.series
		chart.series[0].addPoint 1
		chart.series[1].addPoint 0
	
		cumulatedRuntime = 0
		steps = 0
		candidate = n
		Session.set "mr_candidateString", BigInt.bigInt2str n,BASE
		Session.set "mr_latestResult", ""
		Session.set "mr_latestRuntime", ""
		Session.set "mr_cumulatedRuntime", ""
		Session.set "mr_witness", ""
		Session.set "mr_probabilityForComposite", ""
		Session.set "mr_steps", 0
		candidate_minus_1 = BigInt.addInt candidate, -1

	

	probability = (steps) ->
		Math.pow 4,-steps



from and to are bigInts too

	


	doStep = (event, template)->
		steps++
		a = Tools.bigIntRandBetween BIG_INT_2, candidate_minus_1
		
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
		Session.set "mr_cumulatedRuntime", cumulatedRuntime
		Session.set "mr_latestRuntime", runtime
		Session.set "mr_latestResult", resultText
		Session.set "mr_witness", BigInt.bigInt2str a,BASE
		Session.set "mr_probabilityForComposite", probabilityForComposite
		Session.set "mr_steps", steps
		chart.series[0].addPoint probabilityForComposite
		chart.series[1].addPoint cumulatedRuntime



	initChart = (template)->
		$chart = $(template.find(".chart")).highcharts
			chart:
				height: 400
				zoomType: 'x'
			legend:
				layout: "vertical"
				itemStyle:
					paddingTop: "8px"
					paddingBottom: "8px"
			type: "bar"
			title: "Miller rabin"
			xAxis:
			#	type: "logarithmic"
				tickInterval: 1
				title:
					text: "steps"
			yAxis: [ 
				(min: 0,  opposite: true, title: text: "time taken in ms", style: color: COLOR_RUNTIME),
					
				(min: 0, title: text: "probability", style: color: COLOR_PROBABILITY)
				]
				
					
		chart = $chart.highcharts()

		chart.addSeries name: "probability of composite", data:[],yAxis:1, color: COLOR_PROBABILITY 
		chart.addSeries name: "cumulated runtime", data:[],yAxis:0, color: COLOR_RUNTIME
		#chart.addSeries name: "runtime", data:[],yAxis:1
	
	Template.millerRabin.sample = -> Samples["1000"]

	Template.millerRabinStats.latestRuntime = -> Session.get("mr_latestRuntime")+"ms"
	Template.millerRabinStats.cumulatedRuntime = -> Session.get("mr_cumulatedRuntime")+"ms"
	Template.millerRabinStats.numberOfTests = -> Session.get "mr_steps"
	Template.millerRabinStats.latestResult = -> Session.get "mr_latestResult"
	Template.millerRabinStats.candidate = -> Session.get "mr_candidateString"
	Template.millerRabinStats.candidateLength = -> Session.get("mr_candidateString")?.length
	Template.millerRabinStats.witness = -> Session.get "mr_witness"
	Template.millerRabinStats.probabilityForComposite = -> 100* (Session.get "mr_probabilityForComposite")+"%"
	Template.millerRabinStats.probabilityForPrime = -> 
		prob = 1 - Session.get "mr_probabilityForComposite" if Session.get("mr_probabilityForComposite") > 0
		100* prob + "%"
	Template.millerRabinChart.rendered = ->
		initChart this

	Template.millerRabin.events = 
		"click .step": doStep
		"change .candidate": (event)->
			initWithStringCandidate $(event.target).val()


	

