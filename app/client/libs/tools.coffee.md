	
### Helpers

	rand_between = (from,to) ->
		Math.random() * (to - from) + from

	shuffle = (a) ->
		# From the end of the list to the beginning, pick element `i`.
		for i in [a.length-1..1]
			# Choose random element `j` to the front of `i` to swap with.
			j = Math.floor Math.random() * (i + 1)
			# Swap `j` with `i`, using destructured assignment
			[a[i], a[j]] = [a[j], a[i]]
		# Return the shuffled array.
		a

	log = _.bind console.log, console

	bigIntRandBetween = (from, to) ->

		diff = BigInt.addInt(BigInt.sub(to, from),1)
		bits = BigInt.bitSize diff
		rand = BigInt.randBigInt bits
		rand =  BigInt.mod rand, diff
		return BigInt.add rand, from

	chunkString = (str, len) ->
		_size = Math.ceil(str.length/len)
		_ret  = new Array(_size)
		_offset = 0
  
		for _i in [0..._size]
			_offset = _i * len;
			_ret[_i] = str.substring(_offset, _offset + len);
		_ret

### export
	
	@Tools = 
		chunkString: chunkString
		shuffle: shuffle
		rand_between: rand_between
		log: log
		bigIntRandBetween:bigIntRandBetween