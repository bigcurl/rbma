# RBMA

# List of included function
* SimpleMA(n) - Moving Average over the last n day/data.
* EMA(a) - Exponential Moving Average with "a" as the alpha parameter.
* EMA(a) - Exponential Moving Average over an irregular timed events serie with "a" as the alpha parameter.
* NDayEMA -	Exponential Moving Average over n day/data as defined in wikipedia. Equivalent to using EMA(a) with a = 2/(n+1).
* CMA - Cumulative Moving Average.

# Usage

Every function is provided as a class, it can either recursively compute the moving average as new value are given or be initialized to continue from a certain point provided you give the necessary information. Let see:
##Exponential Moving Average (EMA)
###Recursive
every time you compute the ema with a new event value, you get it. I included only the first and last one here.

````ruby
e = Ema.new(a: 0.3)
e.compute(current: 11) # => 11
e.compute(current: 13) # => ...
e.compute(current: 15) # => ...
e.compute(current: 9) # => ...
e.compute(current: 8) # => ...
e.compute(current: 8) # => ...
e.compute(current: 7) # => ...
e.compute(current: 9) # => ...
e.compute(current: 10) # => 9.257...
````
###Non recursive
````ruby
e = Ema.new(a: 0.3)
e.compute(current: 11) # => ...
e.compute(current: 13) # => ...
e.compute(current: 15) # => ...
e.compute(current: 9) # => ...
e.compute(current: 8) # => ...
e.compute(current: 8) # => ...
e.compute(current: 7) # => ...
# let say the process breaks here, and you need to continue from here
d = e.compute(current: 9)
e = Ema.new(a: 0.3)
e.compute(current: 10, previous: d) # => 9.257
````

##Irregular timed events serie Exponential Moving Average (IEMA)
###Recursive
````ruby
i = IEma.new(g: 60)
i.compute(t: 0, current: 17) # => ...
i.compute(t: 1, current: 15) # => ...
i.compute(t: 3, current: 12) # => ...
i.compute(t: 5, current: 11) # => ...
i.compute(t: 9, current: 13) # => ...
````
###Non Recursive
````ruby
i = IEma.new(g: 60)
i.compute(t: 0, current: 17) # => ...
i.compute(t: 1, current: 15) # => ...
prev = i.compute(t: 3, current: 12)
j = IEma.new(g: 60)
j.compute(t: 5, current: 11, tprev: 3, prev: prev) # => 16.61...
````
##SimpleMA
###Recursive
````ruby
s = SimpleMa.new(n: 5, empty: "-")
s.compute(current: 2) # => "-"
s.compute(current: 3) # => "-"
s.compute(current: 5) # => "-"
s.compute(current: 3) # => "-"
s.compute(current: 3) # => 3.2
...
````
###Non recursive
Non recursive for SimpleMA is not very interesting
````ruby
lasts = [2,3,5,3]
s = SimpleMa.new(n: 5, empty: "-")
assert s.compute(current: 3, lasts: lasts ) == 3.2
````

##Cumulative moving average
###Recursive
````ruby
c = Cma.new()
c.compute(current: 1) # => ...
c.compute(current: 2) # => ...
c.compute(current: 21) # => ...
c.compute(current: 10) # => ...
c.compute(current: 31) # => ...
c.compute(current: 28) # => ...
c.compute(current: 5) # => ...
c.compute(current: 2) # => 12.5
...
````
###Non recursive
Non recursive for SimpleMA is not very interesting
````ruby
c = Cma.new()
c.compute(current: 1) # => ...
c.compute(current: 2) # => ...
c.compute(current: 21) # => ...
c.compute(current: 10) # => ...
c.compute(current: 31) # => ...
c.compute(current: 28) # => ...
d = c.compute(current: 5)
c = Cma.new()
c.compute(current: 2, last: d, count: 7) # => 12.5
````

##NDayEMA
N days EMA is just an alias of EMA, you can specify the alpha in days/unit of time, instead
````ruby
days = 6
n = NDayEma.new(n: days)
n.a == 2.0/(days+1) # => true
````