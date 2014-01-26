	
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
		rand_between: rand_between
		log: log
		bigIntRandBetween:bigIntRandBetween