# Starting out

## Ready, set, go!

### The **G**lasgow **H**askell **C**ompiler

- GHC interpreter available via `ghci`
- change prompt via `:set prompt "NEWPROMPT "`
- load file via `:l filename.hs`
- open/close multiline commands using `:{` and `:}`

### Essential operations

```haskell
2 + 15 -- addition results in 17
49 * 100 -- multiplication results in 4900
1892 - 1472 -- subtraction results in 420
5 / 2 -- division results in 2.5, interestingly rational, not integer
50 * (100 - 4999) -- parentheses imply evaluation as expected
5 * -3 -- parsing error: "cannot mix * and prefix - in same infix expression"
5 * (-3) -- yields -15 because * and prefix - are not in same infix expression
True && False -- False, and note boolean constants are capitalized
True || False -- True
not False -- True, and note "not" instead of "!"
5 == 5 -- True
5 /= 5 -- False, and note "/=" instead of "!="
```

## Baby functions

### Operations as functions

Operations are functions! Traditionally we use *infix* notation, with our operators sandwiched between the arguments. But this limits us to one or two arguments per operation or function. For this and other reasons, *prefix* functions are the norm in haskell.

```haskell
min 9 10 -- 9
9 `min` 10 -- functions with two arguments can also be used infix
succ 9 -- `succ` is short for successive/successive
       -- maybe part of the language's iterator concept?
```

### If expressions

- Haskell is built with expressions
- `if` is also an expression
- all expressions must return a value
- Q.E.D. `if` must return a value and `else` is required

```haskell
doubleSmallNumber x = if x > 100  
                        then x  
                        else x*2 
```

## Enter the list

- lists hold elements of the same type
- strings are lists of characters
- lists are compared element by element

### List functions

- `++` concatenates lists
- `:` prepends element to a list
- `!!` access list element by index
- `head` returns the first element in a list
- `tail` returns a list without its head element
- `last` returns last element in a list
- `init` returns a list without its last element
- `length` returns length of a list
- `null` checks for the null/empty list
- `reverse` reverses a list
- `take` returns number of elements from head of list
- `drop` returns remaining list after number of elements
- `maximum` returns maximum element in list
- `minimum` returns minimum element in list
- `sum` returns sum of a list
- `product` returns product of a list
- `elem` tests if provided item is element in list

```haskell
let lostNumbers = [4,8,15,16,23,42] -- Lost number list
let first = "Jeff" -- equivalent to ['J','e','f','f']?
first ++ " " ++ "Crocker" -- "Jeff Crocker"
'<':first -- "<Jeff"
1:2:3:[] == [1,2,3] -- True
lostNumbers !! 3 -- 16
[1] < [2,0] -- True
head lostNumbers -- 4
tail lostNumbers -- [8,15,16,23,42]
last lostNumbers -- 42
length lostNumbers -- 6
null lostNumbers -- False
reverse first -- "ffeJ"
take 3 lostNumbers -- [4,8,15]
drop 3 lostNumbers -- [16,23,42]
minimum lostNumbers == head lostNumbers -- True
maximum lostNumbers == last lostNumbers -- True
sum lostNumbers -- 108
product lostNumbers -- 7418880
elem 0 lostNumbers -- False
```

## Ranges

Range syntax enables convenient enumerated lists of values

```haskell
[1..5] -- [1,2,3,4,5], note terminal value is included
[2,4..20] -- [2,4,6,8,10,12,14,16,18,20], steps may be implied
[2,4..11] -- [2,4,6,8,10], 11 is not included due to step size
[5..1] -- [], null list because default step size of 1 gives empty range
[5,4..1] -- [5,4,3,2,1] , now with -1 step size, range is not empty
```

### Lazy infinities

- Haskell lazy evaluates expressions
- lists are expressions
- lists are lazily evaluated
- lists may be infinitely long
- `cycle` function loops infinitely over a given list
- `repeat` function loops inifinitely over a given element

```haskell
take 10 [13,26..] -- [13,26,39,52,65,78,91,104,117,130], 10 from infinite range
let lostNumbers = [4,8,15,16,23,42] -- Lost number list
take 10 (cycle lostNumbers) -- [4,8,15,16,23,42,4,8,15,16]
(take 8 (repeat "na")) ++ ["batman!"]
-- ["na","na","na","na","na","na","na","na","batman!"]
```

### List comprehensions

In mathematics, *set* comprehensions use the following components to define a set:

- input sets
    - e.g. natural numbers
- predicate
    - optional filtering predicate on the input set
    - e.g. `x <= 10` would filter the natural number input set>
- output function
    - the transform function on elements of the input set
    - e.g. `2*x + 1`

Haskell *list* comprehensions are similar:
```haskell
-- [output function | input sets, predicates ]
-- with the commas separating input sets and optional predicates
[2*x + 1 | x <- [1..10]] -- [3,5,7,9,11,13,15,17,19,21]
[2*x + 1 | x <- [1..10], mod (x*x) 2 == 0] -- [5,9,13,17,21]
[x | x <- lostNumbers, odd x] -- [15, 23], note input set was other list
-- multiple input sets may be used as well
[x*y | x <- lostNumbers, y <- [1..5]]
-- [4,8,12,16,20,8,16,24,...,210]
length [x*y | x <- lostNumbers, y <- [1..5]] -- 30
length lostNumbers * length [1..5] == 30 -- True
let removeJeffreyCrocker st = [ c | c <- st, not (elem c "JeffreyCrocker")]
removeJeffreyCrocker "This sentence just has too much me"
-- "This sntn just has t muh m"
```

## Tuples

- use when known number of elements
- types may be hetergenous
- `fst` returns first component of a pair tuple
- `snd` returns second component of a pair tuple
- `zip` returns list of paired elements from two other lists

```haskell
let rectangle = (12,3)
fst rectangle -- 12
snd rectangle -- 3
let triangle = (3,4,5)
fst triangle -- Error: Couldn't match expected type '(a,b)'
zip [1..] lostNumbers -- [(1,4),(2,8),(3,15),(4,16),(5,23),(6,42)]
```