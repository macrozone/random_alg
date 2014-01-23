# Simple Prime test with TestDivision

Idea: loop through all numbers from 2 and the Squareroot of n
and check if n can be divided through that number. If so, n is not a prime


	@SimpleIsPrime = (n) ->
		for i in [2..Math.sqrt n]
			if n % i == 0
				return false
		return true