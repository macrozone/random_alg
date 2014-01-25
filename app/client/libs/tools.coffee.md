	
### Helpers

	rand_between = (from,to) ->
		Math.random() * (to - from) + from

	log = _.bind console.log, console

	bigIntRandBetween = (from, to) ->

		diff = BigInt.addInt(BigInt.sub(to, from),1)
		bits = BigInt.bitSize diff
		rand = BigInt.randBigInt bits
		rand =  BigInt.mod rand, diff
		return BigInt.add rand, from

### export
	
	@Tools = 
		rand_between: rand_between
		log: log
		bigIntRandBetween:bigIntRandBetween