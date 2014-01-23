	
### Helpers

	rand_between = (from,to) ->
		Math.random() * (to - from) + from

	Log: _.bind console.log, console

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


	approx_pi_stepwise = (context) ->
		context = _.defaults context, callback: Log, steps: 0, inside: 0, roundsPerInterval: 10
		round = ->
			context.steps++
			x = Math.random()
			y = Math.random()
			if x*x+y*y <=1
				context.inside++
			pi = 4 * context.inside / context.steps

			running = context.callback(x,y,pi)
			if running then doNextRound()

		doNextRound = ->
			if context.steps % context.roundsPerInterval == 0
				_.defer round
			else 
				round()
		round()

### Integrate

	integrate = (f, from_x, to_x, from_y, to_y, n) ->
		below = 0
		area = (to_x-from_x) * (to_y - from_y)
		for i in [1..n]
			x = rand_between to_x,from_x
			y = rand_between from_y, to_y
			if y < f(x)
				below++
		return area * below / n



### export
	
	@M = 
		rand_between: rand_between
		approx_pi:approx_pi
		approx_pi_stepwise: approx_pi_stepwise
		integrate:integrate