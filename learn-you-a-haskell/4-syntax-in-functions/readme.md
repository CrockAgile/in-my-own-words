# Syntax in functions

## Pattern matching

### Functions

Functions may be defined many times with different argument patterns

```haskell
factorial :: (Integral a) => a -> a
factorial 0 = 1
factorial n = n * factorial (n-1) -- recursive definition
factorial 5 -- 120
-- I wonder if Haskell cares about tail recursive optimizations?
```

Non-exhaustive patterns may throw runtime exceptions

```haskell
nonExhaustive :: Bool -> Char
nonExhaustive True = 'T'

nonExhaustive False -- Error: Non-exhaustive patterns in function nonExhaustive
```

### Lists

Lists can used in pattern matching as well

```haskell
head' :: [a] -> a  
head' [] = error "Can't call head on an empty list, dummy!"  
head' (x:_) = x -- `x:xs` patterns only match against lists of length >= 1

-- remember [1,2,3] is equivalent to 1:2:3:[]
-- ding! null list `[]` is useful as definition of list termination
-- explains why `(x:y:_)` can't be rewritten, since it is unbounded pattern
```

### "*as patterns*"

Break variable up into elements for pattern matching **and** reference containing variable.

```haskell
capital :: String -> String  
capital "" = "Empty string, whoops!"  
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
```

### Match guards

Pattern matching may also use predicates as guards to matching a branch

```haskell
chitChatTemp :: (Ord a, Num a) => a -> String
chitChatTemp temp
    | temp <= 5 = "Boy it's a cold one"
    | temp >= 32 = "Boy it's a hot one"
    | otherwise = "... I heard tomorrow will be a different temperature" 
```

### Where bindings

Where bindings can be used for values common across patterns

```haskell
chitChatTempF :: (Fractional a, Ord a) => a -> String
chitChatTempF fahrenheit
    | temp <= 5 = "Boy it's a cold one"
    | temp >= 32 = "Boy it's a hot one"
    | otherwise = "... I heard tomorrow will be a different temperature"
    where temp = (fahrenheit - 32) * 5 / 9
```
### Let bindings

- let bindings are expressions
- expressions are valid anywhere?
- their format is `let <bindings> in <expression>`
- binding names are only visible for the following `in <expression>`

```haskell
-- reminder `if then else` is also an expression
if 25 > 0 then "I am aging" else "Time is flat circle"
-- "I am aging"

-- `let in` is an expression too
let sub5 x = x - 5 in (sub5 5, sub5 10, sub5 0)
-- (0,5,-5)

let a = 42; b = 13; c = 72 in a*b*c
-- 39312
let (a,b,c) = (42,13,72) in a*b*c
-- 39312
```

### In `case of` expressions

```haskell
case expression of pattern -> result  
                   pattern -> result  
                   pattern -> result 
```

Case of expressions can be used anywhere, not just function definitions.

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ (case xs of [] -> "empty."  
                                                [x] -> "a singleton list."   
                                                 xs -> "a longer list.")
```

Pattern matching in function definitions is equivalent.

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ what xs  
    where what [] = "empty."  
          what [x] = "a singleton list."  
          what xs = "a longer list."  
```