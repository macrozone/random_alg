

	drawQuarterCircle = (drawContext, r) ->

		drawContext.beginPath()
		drawContext.arc(0,r,r,0,2*Math.PI)
		drawContext.stroke()

	

	running = false
	context = 
		steps: 0
		inside: 0

	Template.piTest.rendered = ->
		console.log this.find()

	Template.piTest.events = 
		"click .start": (event, template)->

			running = !running
			$canvas = $(template.find "canvas")
			canvas = $canvas.get 0
			r = $canvas.width()
			drawContext = canvas.getContext "2d"
			drawQuarterCircle drawContext, r
			
			
			context.callback = (x,y,pi) ->
				$(template.find ".steps").text context.steps
				$(template.find ".pi").text pi
				drawContext.fillRect(x*r,y*r,1,1)
				return running

			console.log
			M.approx_pi_stepwise context