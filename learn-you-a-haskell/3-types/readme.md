# Types

## Believe the type

- type inference is supported
- `::` is read as "has type of"
- explicit types are capitalized
- `:t` can inspect the type of an expression
- `->` denotes function return type

```haskell
removeJeffreyCrocker st = [ c | c <- st, not (elem c "JeffreyCrocker") ]
-- :t removeJeffreyCrocker
removeJeffreyCrocker :: [Char] -> [Char]

addThree x y z = x + y + z
-- :t addThree
addThree :: Num a => a -> a -> a -> a
```

## Common types

- `String` is synonymous with `[Char]`
- `Int` is integer bounded by machine word size
- `Integer` is unbounded integer
- `Float` is real floating point with single precision
- `Double` is real floating point with double precision
- `Bool` is `True` or `False`
- `Char` is single character

## Type variables

Types that are not capitalized are type variables.

```haskell
-- let's make a fst for 3 element tuples
fstOfThree :: (a,b,c) -> a
fstOfThree (a,_,_) = a
fstOfThree (17, 'c', "Last") -- 17
```

## Typeclasses 101

- typeclasses define an interface
- a type that supports the interface is "part of that typeclass"
- typeclass contraints are part of a function's type
- `=>` separates the contraints from the arguments

```haskell
-- :t (==)
(==) :: Eq a => a -> a -> Bool
-- type variable `a` is contrained to be part of the `Eq` typeclass
```

### Some basic typeclasses

- `Eq` is for equality via `==`, `/=`
- `Ord` is for ordering via `<`, `>`, `<=`, `>=`
    - `compare` takes two `Ord` of same type and returns an `Ordering`
    - `Ordering` is a type that can be `GT`, `LT`, `EQ`
- `Show` is for string presentation via `show`
- `Read` is opposite of `Show`, parsing a string to a value
    - error if ambiguous
    - explicit types remove abiguity, `read "5" :: Int`
- `Enum` is for sequential enumeration
    - `() Bool Char Ordering Int Integer Float Double` are `Enum`
    - enables range lists, `[LT .. GT]`
    - `succ` to get successive item in enumeration
    - `pred` to get predecessor item in enumeration
- `Bounded` is for bounded types
    - `minBound` to get type's minimum bound
    - `maxBound` to get type's maximum bound
- `Num` is for numeric types
- `Integral` is for whole numbers
    - `fromIntegral` is a convenience function for converting integrals to numerics
- `Floating` is for floating precision numbers