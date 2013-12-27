	
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

### Integrate


	integrate = (f, a, b, from_y, to_y, n) ->
		
		below = 0
		area = (b-a) * (to_y - from_y)
		
		for i in [1..n]
			x = rand_between a,b
			y = rand_between from_y, to_y
			if y < f(x)
				below++

		return area * below / n

			
				




### export
	
	@M = 
		rand_between: rand_between
		approx_pi:approx_pi
		integrate:integrate