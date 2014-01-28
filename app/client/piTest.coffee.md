

	
### PI
approx pi

	approx_pi = (n) ->
		inside = 0
		for i in [1..n]
			x = Math.random()
			y = Math.random()
			if x*x+y*y <=1
				inside++
		return 4 * inside / n


### stepwise variant with callback to draw the result

	approx_pi_stepwise = ->
		_.defaults @, callback: Tools.log, steps: 0, inside: 0, roundsPerInterval: 1
		round = =>
			@steps++
			x = Math.random()
			y = Math.random()
			if x*x+y*y <=1
				@inside++
			pi = 4 * @inside / @steps

			running = @callback x,y,pi
			if running then doNextRound()

		doNextRound = =>
			if @steps % @roundsPerInterval == 0
				_.defer round
			else 
				round()
		round()

### Integration as a sample

	integrate = (f, from_x, to_x, from_y, to_y, n) ->
		below = 0
		area = (to_x-from_x) * (to_y - from_y)
		for i in [1..n]
			x = rand_between to_x,from_x
			y = rand_between from_y, to_y
			if y < f(x)
				below++
		return area * below / n




	drawQuarterCircle = (drawContext, r) ->
		drawContext.beginPath()
		drawContext.arc(0,r,r,0,2*Math.PI)
		drawContext.stroke()

	


	Template.piTest.events = 
		"click .start": (event, template)->
			template.context = {} unless template.context?
			template.context.running = !template.context.running

			$canvas = $(template.find "canvas")
			canvas = $canvas.get 0
			r = $canvas.width()
			drawContext = canvas.getContext "2d"
			drawQuarterCircle drawContext, r
			
			template.context.callback = (x,y,pi) ->
				$(template.find ".steps").text @steps
				$(template.find ".pi").text pi
				drawContext.fillRect(x*r,y*r,1,1)
				return @running

			approx_pi_stepwise.call template.context


