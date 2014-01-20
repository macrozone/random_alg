simple sample, taken from pseudo code on stack overflow
it only works on ints
See this [link on stackoverflow](http://stackoverflow.com/questions/17063753/can-someone-explain-this-miller-rabin-primality-test-pseudo-code-in-simple-terms)
for the details


	ProbablyPrime = true
	Composite = false

	isStrongPseudoprime = (n, a) ->
		d = n - 1
		s = 0
		while d % 2 == 0
			d = d / 2
			s = s + 1
		t = powerMod(a, d, n)
		if t == 1 
			return ProbablyPrime
		while s > 0
			if t == n - 1
				return ProbablyPrime
			t = (t * t) % n
			s = s - 1
		return Composite

	isPrime = (n, k) ->
		for i in [1..k]
			a = _.random 2, n-1
			if isStrongPseudoprime(n, a) == Composite
				return Composite
		return ProbablyPrime

	powerMod = (b, e, m) ->
		x = 1
		while e > 0
			if e % 2 == 1
				x = (b * x) % m
			b = (b * b) % m
			e = Math.floor e/2 # integer division
		return x


export it to global namespace

	@MillerRabin = 
		isStrongPseudoprime: isStrongPseudoprime
		isPrime: isPrime