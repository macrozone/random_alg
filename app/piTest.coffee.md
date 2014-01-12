

	drawQuarterCircle = (context, r) ->

		context.beginPath()
		context.arc(0,r,r,0,2*Math.PI)
		context.stroke()

	if Meteor.isClient
		Template.piTest.events = 
			"click .start": ->
				$canvas = $(".piTest canvas")
				r = $canvas.width()
				canvas = $canvas.get 0
				context = canvas.getContext "2d"
				drawQuarterCircle context, r
				steps = 0
				MAX_STEPS = 10000
				callback = (x,y,pi) ->
					$(".piTest .steps").text steps
					$(".piTest .pi").text pi
					steps++
					context.fillRect(x*r,y*r,1,1)
					return steps < MAX_STEPS

				M.approx_pi_stepwise callback