# OpenGDS Math 0.1.0

Contributors:
- [Philip Diffenderfer (ClickerMonkey)](http://github.com/ClickerMonkey)

Contents:
- [Introduction](#introduction)
- [Definition](#definition)
- [Problem Statement](#problem-statement)
- [Goals](#goals)
- [Concepts](#concepts)
  - [Data Type](#data-type)
  - [Dimension](#dimension)
  - [Component](#component)
  - [Calculator](#calculator)
  - [Calculator Registry](#calculator-registry)
  - [Geometry](#geometry)
    - [Bounds](#bounds)
    - [Range](#range)
    - [Line](#line)
    - [Sphere](#sphere)
    - [Plane](#plane)
- [Design](#design)
  - [Calculator](#calculator-t-)
  - [Calculator Registry](#calculatorregistry)
  - [Unary Operation](#unaryoperation-s-)
  - [Binary Operation](#binaryoperation-s-)
  - [Geometry](#geometry-t-)
  - [Bounds](#bounds-t-)
  - [Range](#range-t-)
  - [Line](#line-t-)
  - [Sphere](#sphere-t-)
  - [Plane](#plane-t-)
  - [OpenMath](#openmath)
- [API](#core-api)
  - [Calculator](#calculator-t--1)
  - [Calculator Registry](#calculatorregistry-1)
  - [Geometry](#geometry-t--1)
  - [Bounds](#bounds-t--1)
  - [Range](#range-t--1)
  - [Line](#line-t--1)
  - [Sphere](#sphere-t--1)
  - [Plane](#plane-t--1)
  - [OpenMath](#openmath-1)
- [AdditionalAPI](#additional-api)
  - [Calculator](#calculator-t--2)
  - [OpenMath](#openmath-2)
- [Implementation](#implementation)

## Introduction

This document, referred to as the "OpenGDS Math Specification" or just "Math" hereafter, describes the OpenGDS Math API: what it is, how it acts, and what is required to implement it.

## Definition

Math addresses the mathematical foundation necessary for game programming and dependent specifications.

## Problem Statement

Performing mathematical operations on and between data types (scalar, vector, colors, etc) is a necessity in game programming. In most APIs there's a tendency to place mathematical operations on the data type. This adds a lot of duplicate math across various data types which can cascade throughout the entire code base. This also can couple your codebase to other libraries. These data types with other values can be used to define geometric structures which are used in other areas such as particle systems, collision detection, spatial databases, steering behaviors, and animation. Math addresses these operations and structures.

## Goals

- Provide a way to allow an existing codebase to take advantage of OpenGDS. This is the main goal of Math and achieves this with Calculators and Geometry.
- Provide a way to reuse mathematical equations and algorithms across any data type.
- Provide an API which promotes efficient use of data types.
- Provide a generic Calculator API to allow the use of any data type to... <a name="calculator-goal"></a>
  - be used in mathematical operations:
    - add, subtract, multiple, divide, scale, & interpolation.
    - custom unary and binary operations.
  - be parsed from different data types into the desired data type.
  - be written/read to/from data streams.
  - be poolable (some data types are expensive to instantiate).
  - be copied to another data type.
  - be cloned to a new value.
  - be measured in length.
  - be measured in distance from another value.
  - be compared to another value or zero.
  - be clamped to a range of values.
  - be compared to a range of values.
  - be generated randomly from a range of values.
  - be validated to finite (values that aren't infinity or NaN).
  - be broken down into and built from scalar components.
  - be used in spatial operations to calculate...
    - spherical interpolation.
    - the value closest to a given value between a start and end value.
    - the interception time of a target with a bullet.
    - distance a given value is to start and end values.
    - if a value is in the "view" of a value with a direction and a field-of-view.
    - if a sphere is partially/wholy in the "view" of a value with a direction and a field-of-view.
    - a value on a parametrized cubic curve given a set of points, a matrix, and a weight.
- Provides the base equations necessary so new Calculators can easily be added that implement just a few core operations.
- Provide generic Geometric structures:
  - Bounds: a volumetric data structure that contains a `min` and `max` value.
  - Range: a linear data structure that exists between a `start` and `end` value.
  - Line: a linear data structure that exists on a `start` and `end` value.
  - Sphere: a spherical data structure that contains a `center` value and a `radius`.
  - Plane: a planar data structure that has `origin` and `normal` values.
- Provide a generic API for the geometric structures which... <a name="geometry-goal"></a>
  - estimate a radius.
  - determine if a value lies inside, outside, or intersects with the structure.
  - calculate signed distance and a normal value to the structure.
- Provide an API which utilizes `out` variables to keep mathematical operations from instantiating new instances.
- Provide a simple API for common math functions.

## Concepts

### Data Type

A value that is most often a scalar or composed of scalars. Examples of data types:

- Floating point numbers
- Integers
- Vectors
- Colors
- Quaternions
- Enumeration (constants/immutable types can be used, but only a subset of functionality can be provided)
- Skeletal Joint

### Dimension

A data type has a fixed number of dimensions - often 1d, 2d, 3d, or 4d. For example:

- `1d`: scalar types (floating point, integers), enumerations, strings.
- `2d`: points on a screen.
- `3d`: points in a 3d space, or an opaque color.
- `4d`: quaternion or colors with an alpha component.

### Component

A scalar value in a data type. Most data types have a scalar value for each dimension.

### Calculator

A service which performs the functions defined in [Goals](#calculator-goal).

### Calculator Registry

A service which takes a data type or a description of a data type and returns the estimated calculator. A description of a data type could be a string like "color" or an enumeration. Custom calculators can be added to the registry to be used by the application.

### Geometry

A structure which holds one or more values of a given data type and zero or more scalar values to define some geometry. These geometries exist in whatever dimension their data type exists in - allowing a single geometry definition to be used in 1d, 2d, 3d, 4d, or `n` dimensions. Geometries in `1d` are mostly indistinguishable from each other. A geometry has the functions defined in [Goals](#geometry-goal).

#### Bounds

A geometry which has a `minimum` and `maximum` value for a specific data type. A bound is a volumetric structure - meaning the values that exist between the `minimum` and `maximum` points lie anywhere inside the rectangular boundary. Bounds can also have a `thickness` scalar value to describe a geometry with rounded corners.

#### Range

A geometry which has a `start` and `end` value for a specific data type. A range is a linear structure - meaning the values that exist between the `start` and `end` points lie on a direct "line" *between* them. Ranges can also have a `thickness` scalar value to describe a geometry with rounded corners (capsule). A range is synonymous with line segment.

#### Line

A geometry which has a `start` and `end` value for a specific data type. A line is an infinitely linear structure - meaning the `start` and `end` values define only two points on an infinitely long line. Lines can also have a `thickness` scalar value to describe a rounded geometry (column).

#### Sphere

A geometry which has a `center` value and a scalar `radius` value. A sphere is a volumetric structure - meaning that values exist in the perimeter of the geometry. A point can represented as a sphere with zero radius.

#### Plane

A geometry which has an `origin` value and a `normal` value which define a plane (infinitely spanning surface). A plane is surface which spans infinitely in all directions perpendicular to `normal`. Planes can also have a `thickness` scalar value.

## Design

The design will be described using the following concepts. These concepts may directly translate to a language, or may be achieved in other ways.

| Concept | Definition |
|:----:| ---------- |
| Class | A template definition of the methods and variables in a particular kind of object. |
| Abstract | A class which has unimplemented methods OR the name of the method which is unimplemented. |
| Method | A procedure associated with an object. |
| Static Method | A procedure that is available on a class definition vs. a class instance. |
| Function | A procedure that can be passed as a parameter to another function or method and returns a result. |
| Interface | A set of methods that a class must implement. |
| < T > | The symbol which signifies a generic class, interface, or function which deals with a data type. |
| T | The argument type which represents the data type. |
| < S > | The symbol which signifies a generic class, interface, or function which deals with a component of a data type (ex: float, int, etc). |
| S | The argument type which represents the component type. |

### `Calculator< T >`

An abstract class that contains all the methods mentioned in [Goals](#calculator-goal). Implementations of Math may not have any calculator implementations since they're tied to specific data types which are tied to other libraries. There will most likely be a library of calculators for any type of library that needs to interface with Math and all other OpenGDS. Calculators are the only classes that need to be created to use any OpenGDS.

The calculator has a static factory method which takes some input that describes a data type and returns the proper calculator. The calculator has a static method to also register a calculator to a data type description.

### `CalculatorRegistry`

A static class that contains all registered calculators and is responsible for returning a calculator based on the data type or the name of the calculator.

### `UnaryOperation< S >`

A function which takes a component of type `S` and returns a value of type `S`. These functions are passed to the calculator to process each component of a value to generate a new value. Examples of unary operations: `abs`, `floor`, `ceil`, `round`, `sin`, etc.

### `BinaryOperation< S >`

A function which takes two components of type `S` and returns a component of type `S`. These functions are passed to the calculator to process each component of two values to generate a new value. Examples of binary operations: `avg`, `min`, `max`, etc.

### `Geometry< T >`

An interface that contains all methods mentioned in [Goals](#geometry-goal).

### `Bounds< T >`

An implementation of the Geometry interface that satisfies the [Bounds](#bounds) concept.

### `Range< T >`

An implementation of the Geometry interface that satisfies the [Range](#range) concept.

### `Line< T >`

An implementation of the Geometry interface that satisfies the [Line](#line) concept.

### `Sphere< T >`

An implementation of the Geometry interface that satisfies the [Sphere](#sphere) concept.

### `Plane< T >`

An implementation of the Geometry interface that satisfies the [Plane](#plane) concept.

### `OpenMath`

A collection of static functions to perform common mathematical operations.

## API

This API describes the design showing all variables on a class, methods in an interface or class, and the argument names and types for functions and methods.

The following expressions are used in the API:

- `methodName(): type`: Describes a method which returns a value with the given type.
- `variableName`: Describes a parameter with an unknown type passed to a method.
- `variableName[]`: Describes an array parameter with an unknown type passed to a method.
- `variableName:type`: Describes a parameter and its type passed to a method.
- `variableName[]:type`: Describes an array parameter and its types passed to a method.
- `?variableName:type=defaultValue`: Describes an optional parameter with or without a default value.
- `float`: The language variable type for floating point numbers.
- `int`: The language variable type for integer numbers.
- `bool`: The language variable type for boolean.
- `Output`: The language specific output stream that scalar types can be written to.
- `Input`: The language specific input stream that scalar types can be read from.
- `size`: The language variable type to express how much memory/space a data type takes up when its read/written from Input/Output.

### `Calculator< T >`

##### `ZERO: T`
  An instance of the data type with components with values of 0. This should be read-only if possible - otherwise this should never be passed to functions as an `out` parameter.
##### `ONE: T`
  An instance of the data type with components with values of 1. This should be read-only if possible - otherwise this should never be passed to functions as an `out` parameter.
##### `create(): T`
  Creates a new instance of T with components with values of 0. If pooling is enabled on the calculator, this may take from the pool.
##### `createUniform( component:S ): T`
  Creates a new instance of T with components with values of `component`.
##### `clone( source:T ): T`
  Creates a new instance of T with components with the same values in `source`.
##### `copy( out:T, source:T ): T`
  Copies the components from `source` into `out` and returns `out`.
##### `parse( input, ?defaultValue:T=0 ): T`
  Parses the given `input` and returns a new instance. If any components are missing in the given input then components form the `defaultValue` are used. If the input couldn't be parsed into a valid value then an appropriate language specific value will be returned OR an exception will be thrown.
##### `parseArray( input[], ?output[]:T, ?defaultValue:T=0 ): T[]`
  Parses the array of `input` and returns an array of parsed values. If an `input` value is invalid then the appropriate language specific value will be placed in the resulting array OR an exception will be thrown. Optionally if the language supports it, the `output` array can be passed to the method to avoid creating a new one.
##### `clear( out:T, ?component:S=0 ): T`
  Sets the components of `out` to `component` and returns `out`.
##### `unary( out:T, a:T, op:UnaryOperation<S> ): T`
  Sets the components of `out` to the value returned passing each component of `a`  to `op` and returns `out`.
##### `binary( out:T, a:T, b:T, op:BinaryOperation<S> ): T`
  Sets the components of `out` to the value returned passing each component of `a` and `b` to `op` and returns `out`.
##### `scale( out:T, value:T, scale:float ): T`                     TODO
  Multiples the components of `value` by `scale` and returns `out`.
##### `scalei( out:T, scale:float ): T`
  Multiples the components of `out` by `scale` and returns `out`.
##### `add( out:T, augend:T, addend:T ): T`                                   TODO
  Adds the components of `amount` to `out` and returns `out`.
##### `addi( out:T, amount:T ): T`
  Adds the components of `amount` to `out` and returns `out`.
##### `adds( out:T, augend:T, addend:T, scale:float ): T`                     TODO
  Adds the components of `amount` multiplied by `scale` to `out` and returns `out`.
##### `addsi( out:T, amount:T, scale:float ): T`
  Adds the components of `amount` multiplied by `scale` to `out` and returns `out`.
##### `sub( out:T, minuend:T, subtrahend:T ): T`                                   TODO    
  Subtracts the components of `amount` from `out` and returns `out`.
##### `subi( out:T, amount:T ): T`
  Subtracts the components of `amount` from `out` and returns `out`.
##### `mul( out:T, value:T, scale:T ): T`                           TODO
  Multiplies the components of `out` by the components of `scale` and returns `out`.
##### `muli( out:T, scale:T ): T`
  Multiplies the components of `out` by the components of `scale` and returns `out`.
##### `div( out:T, dividend:T, divisor:T ): T`                          TODO
  Divides the components of `out` by the components of `denominator` and returns `out`. If a component of `denominator` is zero the component in `out` will be `zero`.
##### `divi( out:T, divisor:T ): T`
  Divides the components of `out` by the components of `denominator` and returns `out`. If a component of `denominator` is zero the component in `out` will be `zero`.
##### `interpolate( out:T, start:T, end:T, delta:float ): T`
  Sets `out` to the value between `start` and `end` given a `delta` value. When `delta` is 0 `out` will be set to `start`, when `delta` is 1 `out` will be set to `end`, and when `delta` is set to 0.5 then `out` will be set to a value halfway between `start` and `end` (etc).
##### `contain( out:T, min:T, max:T ): T`
  Adjusts `out` to be contained between the area between `min` and `max` (if it's outside that area) and returns `out`.
##### `contains( point:T, min:T, max:T ): bool`
  Returns whether `point` exists (inclusively) in the area between `min` and `max`.
##### `random( out:T, min:T, max:T, ?randomizer:function ): T`
  Sets `out` to a random value in the area between `min` and `max` and returns `out`.
##### `distance( a:T, b:T ): float`
  Calculates the distance between `a` and `b`.
##### `distanceSq( a:T, b:T ): float`
  Calculates the squared distance between `a` and `b`.
##### `length( value:T ): float`
  Calculates the length of `value` - which is essentially the shortest distance from `value` to zero.
##### `lengthSq(valuea:T ): float`
  Calculates the squared length of `value` - which is essentially the shortest distance from `value` to zero squared.
##### `normal( out:T, vector:T ): float`
  Sets `out` to the normal of `vector` and returns the length of `vector`.
##### `normali( out:T, vector:T ): float`                       TODO
  Sets `out` to the normal of `vector` and returns the length of `vector`.
##### `isValue( value ): bool`  
  Determines whether `value` can be handled by this calculator.
##### `isFinite( value:T ): bool`
  Determines whether the given `value` is finite. False is returned if any of the scalar values in the value are Infinity or NaN.
##### `isZero( value:T, ?epsilon:float=0.00001 ): bool`
  Determines whether the given `value` is equal to zero within some `epsilon` threshold.
##### `isEqual( a:T, b:T, ?epsilon:float=0.00001 ): bool`
  Determines whether `a` equals `b` within some `epsilon` threshold.
##### `dot( a:T, b:T ): float`
  Calculates and returns the dot product between `a` and `b`.
##### `lengthen( out:T, value:T, length:float ): T`           TODO
  Sets the length of `out` to `length` and returns `out`.
##### `lengtheni( out:T, length:float ): T`
  Sets the length of `out` to `length` and returns `out`.
##### `clamp( out:T, value:T, min:float, max:float ): T`      TODO
  Sets the length of `out` between `min` and `max` if it lies outside that range - and returns `out`.
##### `clampi( out:T, min:float, max:float ): T`
  Sets the length of `out` between `min` and `max` if it lies outside that range - and returns `out`.
##### `sizeof( value:T ): size`
  Computes the size of `value` and returns it.
##### `write( value:T, to:Output ): size`
  Writes `value` to `to` and returns the size it took up.
##### `read( out:T, from:Input ): T`
  Reads `out` from `from` and returns `out`.
##### `getDimensions(): int`
  Returns the number of dimensions in the data type. A data type can have fewer dimensions than components (ex: homogeneous coordinates).
##### `getComponents(): int`
  Returns the number of components in the data type. Components are typically scalar values.
##### `getComponent( value:T, index:int ): S`
  Returns the component in `value` at the given `index`.
##### `setComponent( value:T, index:int, component:S ): T`
  Sets the component of `value` at the given `index` with the given `component` value and returns `value`.
##### `getDimension( value:T, index:int ): S`
  Returns the dimension in `value` at the given `index`.
##### `setDimension( value:T, index:int, dimension:S ): T`
  Sets the dimension of `value` at the given `index` with the given `dimension` value and returns `value`.
##### `slerpDirect( out:T, start:T, end:T, delta:float ): T`
  Sets `out` to the spherical interpolation `delta` between `start` and `end` on the shortest arc between the two points and returns `out`.
##### `slerpNormal( out:T, start:T, end:T, delta:float ): T`
  Sets `out` to the spherical interpolation `delta` between the normals `start` and `end` on the shortest arc between the two normals and returns `out`.
##### `slerp( out:T, start:T, end:T, delta:float, angle:float ): T`
  Sets `out` to the spherical interpolation `delta` between `start` and `end` on the arc with `angle` radians and returns `out`.
##### `delta( start:T, end:T, point:T ): float`
  Returns the value which describes the point on the infinitely long line between `start` and `end` that is perpendicular to the given `point`. If `point` is perpendicular to `start` then 0 is returned, if `point` is perpendicular to the average of `start` and `end` then 0.5 is returned, etc.
##### `closest( out:T, start:T, end:T, point:T, ?line:bool=false ): T`
  Sets `out` to the closest value on the line between `start` and `end` that is perpendicular to `point`. If `line` is false then the value returned will exist between `start` and `end`. If `line` is true then the value returned will exist on the infinite line that lies on `start` and `end` and is perpendicular to `point`.
##### `interceptionTime( shooter:T, speed:float, targetPosition:T, targetVelocity:T ): float`
  Calculates the time it would take a bullet to travel from `shooter` at `speed` to hit `targetPosition` which is moving `targetVelocity`. If its not possible to intersect the target with a bullet then a negative value will be returned.
##### `distanceFrom( start:T, end:T, point:T, ?line:bool=false ): float`
  Calculates the distance from `point` to the range or line between `start` and `end`.
##### `inView( origin:T, direction:T, fovCos:float, point:T ): bool`
  Determines whether `point` is in the conical view starting at `origin` with the `direction` normal and the inner angle of the cone `fovCos` passed in as the cosine of the desired angle.
##### `isCircleView( origin:T, direction:T, fovTan:float, fovCos:float, center:T, radius:float, entirely:bool ): bool`
  Determines whether the circle at `center` with `radius` is in the conical view starting at `origin` with the `direction` normal and the inner angle of the cone `fovCos` passed in as the cosine of the desired angle.
##### `cubicCurve( out:T, delta:float, p0:T, p1:T, p2:T, p3:T, matrix[4][4]:float, ?inverse:bool=false ): T`
  Sets and returns `out` as the point `delta` along the cubic curve between the four given points `p0`, `p1`, `p2`, and `p3` described by the given `matrix`.
##### `parametricCubicCurve( out:T, delta:float, points[]:T, matrix[4][4]:float, weight:float, ?inverse:bool=false, ?loop:bool=false ): T`
  Sets and returns `out` as the point `delta` along the cubic curve between the given `points` described by the given `matrix` and scaled by `weight`.

### `CalculatorRegistry`

##### `getFor( value ): Calculator<T>`
  Returns the calculator for the given `value`.
##### `get( nameOrType ): Calculator<T>`
  Returns the calculator for the given `nameOrType`.
##### `register( namesOrTypes[], calculator:Calculator<T> )`
  Adds the given `calculator` to the registry for the given array of `namesOrTypes`.

### `Geometry< T >`

##### `hasRadius(): bool`
  Determines whether this geometry can calculate a finite radius.
##### `getRadius(): float`
  Calculates the finite radius of this geometry. The radius and center of the geometry should fully encapsulate the geometry.
##### `getCenter( out:T ): T`
  Sets and returns `out` to the center of this geometry.
##### `getDistance( point:T ): float`
  Calculates and returns the signed distance between this geometry and the given `point`. If the `point` is inside this geometry a negative value is returned.
##### `getDistanceAndNormal( point:T, normal:T ): float`
  Calculates and returns the signed distance between this geometry and the given `point` as well as setting the `normal` which describes the direction from `point` to the edge of the geometry as a normalized value (-distance away where distance is the value returned).
##### `isInside( point:T, ?radius:float=0 ): bool`
  Determines whether `point` is entirely inside this geometry taking into account an optional `radius` which describes a sphere around `point`.
##### `isOutside( point:T, ?radius:float=0 ): bool`
  Determines whether `point` is entirely outside this geometry taking into account an optional `radius` which describes a sphere around `point`.
##### `isIntersecting( point:T, ?radius:float=0 ): bool`
  Determines whether `point` is partially or entirely inside this geometry taking into an optional `radius` which describes a sphere around `point`.
##### `random( out:T, ?maxDistanceFromCenter:float=infinity, ?randomizer:function ): T`
  Sets and returns `out` as a random value in this geometry which is no more than `maxDistanceFromCenter` away from the center using the `randomizer` function to generate a number between 0 and 1.

### `Bounds< T > : Geometry< T >`

##### `min: T`
  The minimum value which describes the bounds. The dimensions in this value must be less than or equal to the respective dimensions in `max`.
##### `max: T`
  The maximum value which describes the bounds. The dimensions in this value must be greater than or equal to the respective dimensions in `min`.
##### `thickness: float`
  The thickness of the edges of this bounds. A non-zero value adds rounded corners to the bounds.

### `Range< T > : Geometry< T >`

##### `start: T`
  The starting value which describes the range.
##### `end: T`
  The ending value which describes the range.
##### `thickness: float`
  The thickness of this range. A non-zero value adds rounded corners to the range creating a "pill".

### `Line< T > : Geometry< T >`

##### `start: T`
  The starting value which describes the line.
##### `end: T`
  The ending value which describes the line.
##### `thickness: float`
  The thickness of this line.

### `Sphere< T > : Geometry< T >`

##### `center: T`
  The center value of this sphere.
##### `radius: float`
  The radius of this sphere.

### `Plane< T > : Geometry< T >`

##### `origin: T`
  The origin of this plane.
##### `normal: T`
  The normal of this plane. The set of values perpendicular to this value describe the surface of the plane.
##### `thickness: float`
  The thickness of this plane.

### `OpenMath`

##### `gcd( a:int, b:int ): int`
  Calculates the greatest common divisor between `a` and `b`;
##### `factorial( a:int ): int`
  Calculates the factorial of `a`.
##### `choose( n:int, m:int ): int`
  Calculates the binomial coefficient of `n` and `m`. This is used in combinatorics where it gives the number of ways, disregarding order, that `n` objects can be chosen from among `m` objects.
##### `clamp( value:S, min:S, max:S ): S`
  Returns `min` if `value` is less than `min`, returns `max` if `value` is greater than `max`, or just returns `value`.
##### `divide( value:S, denominator: S ): S`
  Performs a safe division avoiding divide by zero errors. If `denominator` is zero, then zero is returned. Otherwise `value / denomator` is returned.
##### `equals( a:S, b:S, ?epsilon:S=0.00001 ): bool`
  Determines whether `a` is equal to `b` - or no more different than `epsilon`.
##### `quadraticFormula( a:S, b:S, c:S, noSolution:S ): S`
  Finds the solution to the given quadratic formula variables. If no solution is found, `noSolution` is returned.

## Additional API

### `Calculator< T >`

##### `setPool( pool[]:T )`
  Initializes the `pool` in the calculator with an already allocated array. The `pool` enables reuse of values that may be expensive to allocate. The create method of the calculator and all other methods which create a new value may take from this pool to avoid allocation.
##### `recycle( value:T ): bool`
  Adds the given value to the pool if it's been initialized and there's room.
##### `min( out:T, a:T, b:T ): T`
  Sets `out` to the minimum components from `a` and `b` and returns `out`. This is equivalent to passing a min function to the binary calculator method.
##### `max( out:T, a:T, b:T ): T`
  Sets `out` to the maximum components from `a` and `b` and returns `out`. This is equivalent to passing a max function to the binary calculator method.
##### `avg( out:T, a:T, b:T ): T`
  Sets `out` to the maximum components from `a` and `b` and returns `out`. This is equivalent to passing a max function to the binary calculator method. Calling interpolate with a delta of 0.5 may result in the same value - but this could be calculated faster.
##### `abs( out:T ): T`
  Sets the components of `out` to their absolute value and returns `out`. This is equivalent to passing a abs function to the unary calculator method.
##### `floor( out:T ): T`
  Sets the components of `out` to their floor value and returns `out`. This is equivalent to passing a floor function to the unary calculator method.
##### `ceil( out:T ): T`
  Sets the components of `out` to their ceiling value and returns `out`. This is equivalent to passing a ceil function to the unary calculator method.
##### `round( out:T ): T`
  Sets the components of `out` to their rounded value and returns `out`. This is equivalent to passing a round function to the unary calculator method.
##### `neg( out:T ): T`
  Sets the components of `out` to their negative value and returns `out`. This is equivalent to passing a negating function to the unary calculator method.

### `OpenMath`

##### `setRandomSeed( seed:int )`
  Sets a random seed which all subsequent random functions use to generate a random value.
##### `randomDelta(): float`
  Returns a random float between 0 (inclusive) and 1 (exclusive).
##### `random( min:float, max:float ): float`
  Returns a random float between `min` (inclusive) and `max` (exclusive).
##### `randomInt( min:float, max:float ): float`
  Returns a random integer between `min` (inclusive) and `max` (inclusive).

## Implementation

The following implementation example is pseudo-code and contains terms that might not be available in every language - but will have an equivalent way to express it.

This implementation example expects that the implementing language has the following functions:
- `Math.cos(a)`
- `Math.sin(a)`
- `Math.acos(a)`
- `Math.sqrt(a)`
- `Math.min(a, b)`
- `Math.max(a, b)`
- `Math.abs(a)`
- `Math.floor(a)`
- `Math.ceil(a)`
- `Math.round(a)`

### `CalculatorRegistry`

```java
class CalculatorRegistry {

  private Calculator[] calculators = empty collection of Calculators;
  private Map<Any, Calculator> calculatorMap = empty map;

  public static getFor( value ): Calculator {
    foreach ( calc in this.calculators ) {
      if (calc.isValue( value )) {
        return calc;
      }
    }
    return null or throw an error;
  }

  public static get( nameOrType ): Calculator {
    var calc = this.calculatorMap.get( nameOrType );
    if (calc != null) {
      return calc;
    }
    return null or throw an error;
  }

  public static register( namesOrTypes[], calculator:Calculator ) {
    foreach ( key in namesOrTypes ) {
      this.calculatorMap.put( key, calculator );
    }
    this.calculators.add( calculator );
  }

}
```

### `Calculator< T >``

```java
abstract class Calculator<T> {

  ZERO: T;

  ONE: T;

  TEMP0: T;
  TEMP1: T;

  pool[]: T;
  poolSize: int = 0;

  // Constructor
  public Calculator() {
    this.ZERO = this.createUniform( 0 );
    this.ONE = this.createUniform( 1 );
    this.TEMP0 = this.createUniform( 0 );
    this.TEMP1 = this.createUniform( 0 );
  }

  public create(): T {
    if (this.poolSize > 0) {
      return this.clear( this.pool[ --this.poolSize ], 0 );
    } else {
      return new T;
    }
  }

  public createUniform( component:S ): T {
    return this.clear( this.create(), component );
  }

  public setPool( pool[]:T ) {
    for (index in pool) {
      pool[ index ] = this.create();
    }
    this.pool = pool;
  }

  public recycle( value:T ): bool {
    if (this.poolSize < this.pool.length) {
      this.pool[ this.poolSize++ ] = value;
    }
  }

  public clone( source:T ): T {
    return this.copy( this.create(), source );
  }

  public abstract copy( out:T, source:T ): T
  // out.x = source.x

  public abstract parse( input, ?defaultValue:T=0 ): T

  public parseArray( input[], ?output[]:T, ?defaultValue:T=0 ): T[] {
    if (output == null) {
      output = new T[ input.length ];
    }
    foreach (index in input) {
      output[ index ] = this.parse( input[ index ], defaultValue );
    }
    return output;
  }

  public abstract clear( out:T, ?component:S=0 ): T
  // out.x = component

  public abstract unary( out:T, a:T, op:UnaryOperation<S> ): T
  // out.x = op( a.x )

  public abstract binary( out:T, a:T, b:T, op:BinaryOperation<S> ): T
  // out.x = op( a.x, b.x )

  public abstract adds( out:T, augend:T, addend:T, scale:float ): T
  // out.x = augend.x + addend.x * scale;

  public addsi( out:T, addend:T, scale:float ): T {
    // out.x = out.x + addend.x * scale;
    return this.adds( out, out, addend, scale );
  }

  public scale( out:T, value:T, scale:float ): T {
    // out.x = value.x * scale
    return this.adds( out, this.ZERO, value, scale );
  }

  public scalei( out:T, scale:float ): T {
    // out.x *= scale
    return this.adds( out, this.ZERO, out, scale );
  }

  public add( out:T, augend:T, addend:T ): T {
    // out.x = augend.x + addend.x;
    return this.adds( out, augend, addend, 1 );
  }

  public addi( out:T, amount:T ): T {
    // out.x += amount.x;
    return this.adds( out, out, amount, 1 );
  }

  public sub( out:T, minuend:T, subtrahend:T ): T {
    // out.x = minuend.x - subtrahend.x;
    return this.adds( out, minuend, subtrahend, -1 );
  }

  public subi( out:T, subtrahend:T ): T {
    // out.x -= subtrahend.x
    return this.adds( out, out, subtrahend, -1 );
  }

  public abstract mul( out:T, value:T, scale:T ): T
  // out.x = value.x * scale.x

  public muli( out:T, scale:T ): T {
    // out.x *= scale.x
    return this.mul( out, out, scale );
  }

  public abstract div( out:T, dividend:T, divisor:T ): T
  // out.x = OpenMath.divide( dividend.x, divisor.x )

  public divi( out:T, divisor:T ): T {
    // out.x = OpenMath.divide( out.x, denominator.x )
    return this.div( out, out, divisor );
  }

  public interpolate( out:T, start:T, end:T, delta:float ): T {
    // out.x = (end.x - start.x) * delta + start.x
    out = this.adds( out, this.ZERO, start, 1 - delta );
    out = this.adds( out, out, end, delta );
    return out;
  }

  public abstract contain( out:T, min:T, max:T ): T
  // out.x = OpenMath.clamp( out.x, min.x, max.x )

  public abstract contains( point:T, min:T, max:T ): bool
  // return point >= min && point <= max

  public abstract random( out:T, min:T, max:T, ?randomizer:function ): T
  // out.x = randomizer( min.x, max.x )

  public distance( a:T, b:T ): float {
    return Math.sqrt( this.distanceSq( a, b ) );
  }

  public distanceSq( a:T, b:T ): float {
    T diff = this.sub( this.TEMP0, a, b );
    return this.dot( diff, diff );
  }

  public length( value:T ): float {
    return Math.sqrt( this.lengthSq( value ) );
  }

  public lengthSq( value:T ): float {
    return this.dot( value, value );
  }

  public normal( out:T, vector:T ): float {
    float lsq = this.lengthSq( vector );
    this.copy( out, vector );
    if (lsq !== 1.0 && lsq != 0.0){
      this.scalei( out, 1 / Math.sqrt( lsq ) );
    }
    return out;
  }

  public abstract isValue( value ): bool

  public abstract isFinite( value:T ): bool

  public isZero( value:T, ?epsilon:float=0.00001 ): bool {
    return this.isEqual( value, this.ZERO, epsilon );
  }

  public abstract isEqual( a:T, b:T, ?epsilon:float=0.00001 ): bool
  // return OpenMath.equals( a.x, b.x, epsilon ) && ...

  public abstract dot( a:T, b:T ): float
  // return a.x * b.x + a.y * b.y + ...

  public lengthen( out:T, value:T, length:float ): T {
    return this.clamp( out, value, length, length );
  }

  public lengtheni( out:T, length:float ): T {
    return this.clampi( out, length, length );
  }

  public clamp( out:T, value:T, min:float, max:float ): T {
    float valueSq = this.lengthSq( value );
    if (valueSq != 0.0) {
      if (valueSq < min * min) {
        return this.scale( out, value, min / Math.sqrt( valueSq ) );
      }
      else if (valueSq > max * max) {
        return this.scale( out, value, max / Math.sqrt( valueSq ) );
      }
    }
    return this.copy( out, value );
  }

  public clampi( out:T, min:float, max:float ): T {
    return this.clamp( out, out, min, max );
  }

  public abstract sizeof( value:T ): size

  public abstract write( value:T, to:Output ): size

  public abstract read( out:T, from:Input ): T

  public abstract getComponents(): int

  public abstract getComponent( value:T, index:int ): S

  public abstract setComponent( value:T, index:int, component:S ): T

  public getDimensions(): int {
    return this.getComponents();
  }

  public getDimension( value:T, index:int ): S {
    return this.getComponent( value, index );
  }

  public setDimension( value:T, index:int, dimension:S ): T {
    return this.setComponent( value, index, dimension );
  }

  public slerpDirect( out:T, start:T, end:T, delta:float ): T {
    float startLength = this.length( start );
    float endLength = this.length( end );
    float lengthSq = startLength * endLength;
    float dot = this.dot( start, end );
    float angle = Math.acos( dot / lengthSq );
    return this.slerp( out, start, end, delta, angle );
  }

  public slerpNormal( out:T, start:T, end:T, delta:float ): T {
    float dot = this.dot( start, end );
    float angle = Math.acos( dot / lengthSq );
    return this.slerp( out, start, end, delta, angle );
  }

  public slerp( out:T, start:T, end:T, delta:float, angle:float ): T {
    float invDenom = OpenMath.divide( 1, Math.sin( angle ) );
    float d0 = Math.sin( (1 - delta) * angle ) * invDenom;
    float d1 = Math.cos( delta * angle ) * invDenom;

    out = this.scale( out, end, d1 );
    out = this.addsi( out, start, d0 );
    return out;
  }

  public delta( start:T, end:T, point:T ): float {
    T p0 = this.sub( this.TEMP0, end, start );
    T p1 = this.sub( this.TEMP1, start, point );

    float dot = this.dot( p0, p1 );
    float dsq = this.lengthSq( p0 );
    float delta = dot / dsq;
    return delta;
  }

  public closest( out:T, start:T, end:T, point:T, ?line:bool=false ): T {
    float delta = this.delta( start, end, point );
    if (!line) {
      delta = OpenMath.clamp( delta, 0, 1 );
    }
    return this.interpolate( out, start, end, delta );
  }

  public interceptionTime( shooter:T, speed:float, targetPosition:T, targetVelocity:T ): float {
    T direct = this.sub( this.TEMP0, targetPosition, shooter );

    float a = this.lengthSq( targetVelocity ) - ( shooterSpeed * shooterSpeed );
    float b = 2.0 * this.dot( targetVelocity, direct );
    float c = this.lengthSq( direct );

    return OpenMath.quadraticFormula( a, b, c, -1 );
  }

  public distanceFrom( start:T, end:T, point:T, ?line:bool=false ): float {
    float delta = this.delta( start, end, point );
    if (!line) {
      delta = OpenMath.clamp( delta, 0, 1 );
    }
    T projected = this.interpolate( this.TEMP0, start, end, delta );
    return this.distance( point, projected );
  }

  public inView( origin:T, direction:T, fovCos:float, point:T ): bool {
    T diff = this.sub( this.TEMP0, origin, point );
    return this.dot( diff, direction ) > fovCos;
  }

  public isCircleView( origin:T, direction:T, fovTan:float, fovCos:float, center:T, radius:float, entirely:bool ): bool {
    T circleToOrigin = this.sub( this.TEMP0, center, origin );

    float distanceAlongDirection = this.dot( circleToOrigin, direction );
    float coneRadius = distanceAlongDirection * fovTan;                       
    float distanceFromAxis = Math.sqrt( this.lengthSq( circleToOrigin ) - distanceAlongDirection * distanceAlongDirection );
    float distanceFromCenterToCone = distanceFromAxis - coneRadius;                              
    float shortestDistance = distanceFromCenterToCone * fovCos;                          

    if (entirely) {
        shortestDistance += radius;
    } else {
        shortestDistance -= radius;
    }

    return shortestDistance <= 0;
  }

  public cubicCurve( out:T, delta:float, p0:T, p1:T, p2:T, p3:T, matrix[4][4]:float, ?inverse:bool=false ): T {
    float d0 = 1.0;
    float d1 = delta;
    float d2 = d1 * d1;
    float d3 = d2 * d1;

    float[] ts = [
      inverse ? d3 : d0,
      inverse ? d2 : d1,
      inverse ? d1 : d2,
      inverse ? d1 : d3
    ];

    T temp = this.TEMP0;
    out = this.clear( out );

    for (i = 0; i < 4; i++) {
      temp = this.clear( temp );
      temp = this.addsi( temp, p0, matrix[ i ][ 0 ] );
      temp = this.addsi( temp, p1, matrix[ i ][ 1 ] );
      temp = this.addsi( temp, p2, matrix[ i ][ 2 ] );
      temp = this.addsi( temp, p3, matrix[ i ][ 3 ] );
      out = this.addsi( out, temp, ts[ i ] );
    }
    return out;
  }

  public parametricCubicCurve( out:T, delta:float, points[]:T, matrix[4][4]:float, weight:float, ?inverse:bool=false, ?loop:bool=false ): T {
    float n = points.length - 1;
    float a = delta * n;
    float i = OpenMath.clamp( Math.floor( a ), 0, n - 1 );
    float d = a - i;

    T p0 = (i == 0 ? (loop ? points[n] : points[0]) : points[i - 1]);
    T p1 = points[i];
    T p2 = points[i + 1];
    T p3 = (i == n - 1 ? (loop ? points[0] : points[n]) : points[i + 2]);

    out = this.cubicCurve( out, d, p0, p1, p2, p3, matrix, inverse );
    out = this.scalei( out, weight );
    return out;
  }

}
```
