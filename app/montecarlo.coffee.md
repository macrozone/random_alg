	
### Helpers



	rand_between = (from,to) ->
		Math.random() * (to - from) + from

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


	approx_pi_stepwise = (callback) ->
		steps = 0
		inside = 0
		round = ->
			steps++
			x = Math.random()
			y = Math.random()
			if x*x+y*y <=1
				inside++

			pi = 4 * inside / steps

			running = callback(x,y,pi)
			if running then _.defer round

		_.defer round

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