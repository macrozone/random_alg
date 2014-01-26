
	COLOR_PROBABILITY = "#AA4643"
	COLOR_RUNTIME = "#89A54E"
	BIG_INT_2 = BigInt.int2bigInt 2,1
	BASE = 10
	RESULT_COMPOSITE = false
	RESULT_PRIME = true


	# Simple Prime test with TestDivision

Idea: loop through all numbers from 2 and the Squareroot of n
and check if n can be divided through that number. If so, n is not a prime
	
	
	BIG_INT_2 = BigInt.int2bigInt 2,1
	BIG_INT_3 = BigInt.int2bigInt 3,1

	@isPrime1 = (n) ->
		for i in [2..Math.sqrt n]
			if n % i == 0
				return false
		return true



slightly faster Implementation from
http://stackoverflow.com/a/2385999/1463534


	@isPrime2 = (n) ->
		if(n < 2) then return false
		if(n == 2 || n == 3) then return true
		if(n%2 == 0 || n%3 == 0) then return false
		sqrtN = Math.sqrt(n)+1

		for i in [6...sqrtN] by 6 
			if(n%(i-1) == 0 || n%(i+1) == 0) then return false;
		
		return true;

mainly the same algoritm as above, but with BigInt

	@isPrimeBigInt = (n) ->
		if BigInt.greater BIG_INT_2, n then return false
		if BigInt.equals n, BIG_INT_2 or BigInt.equals n, BIG_INT_3 then return true
		if BigInt.modInt(n,2) == 0 or BigInt.modInt(n,3) == 0 then return false
		
		i = BigInt.int2bigInt 6,1
		while BigInt.greater n, (BigInt.mult i,i)
			a = BigInt.mod n, (BigInt.addInt i,-1)
			b = BigInt.mod n, (BigInt.addInt i,1)

			if BigInt.isZero a or BigInt.isZero b then return false
			i = BigInt.addInt i, 6

		return true



	
		

## The experiment

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

	runWithStringCandidate = (aString) ->
		aString = aString.replace(/\D/g,'');
		runWithCandidate.call @, BigInt.str2bigInt aString,BASE
	
	runWithCandidate = (n)->
		startTime = getTime()
		result = isPrimeBigInt n
		endTime = getTime()
		runtime = endTime - startTime
		size = BigInt.bitSize n
	#	@chart.series[0].addPoint [size, runtime]
		switch result
			when RESULT_COMPOSITE 
				resultText = "composite"
				
			when RESULT_PRIME
				resultText = "prime"

		Session.set "candidateString", BigInt.bigInt2str n,BASE
		
		Session.set "latestRuntime", runtime
		Session.set "latestResult", resultText

		

	
	Template.primeStats.candidate = -> Session.get "candidateString"
	Template.primeStats.candidateLength = -> Session.get("candidateString")?.length
	Template.primeStats.latestRuntime = -> Session.get("latestRuntime")+"ms"

	Template.primeStats.latestResult = -> Session.get "latestResult"
	
	
	Template.deterministicPrime.events = 
		"click .run": (event,template)->

			runWithStringCandidate.call template.data, $(template.find(".candidate")).val()
		

	Template.deterministicPrime.rendered = ->
		_.defaults @,data: steps: 0, n:1,nStep:1
		#initChart this