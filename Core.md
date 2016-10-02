# OpenGDS Core 0.1.0

Contributors:
- [Philip Diffenderfer (ClickerMonkey)](http://github.com/ClickerMonkey)

Contents:
- [Introduction](#introduction)
- [Definition](#definition)
- [Problem Statement](#problem-statement)
- [Goals](#goals)
- [Concepts](#concepts)
- [Design](#design)
- [Core API](#core-api)
- [AdditionalAPI](#additional-api)
- [Implementation](#implementation)

## Introduction

This document, referred to as the "OpenGDS Core Specification" or just "Core" hereafter, describes the OpenGDS Core API: what it is, how it acts, and what is required to implement it.

## Definition

Core addresses the mathematical foundation necessary for game programming and dependent specifications.

## Problem Statement

Performing mathematical operations on and between data types (scalar, vector, colors, etc) is a necessity in game programming. These data types with other values can also be used to define geometric structures which are used in other areas such as particle systems, collision detection, spatial databases, steering behaviors, and animation. Core addresses these operations and structures.

## Goals

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
  - determine if a value lies inside or outside the structure.
  - calculate signed distance and normal a value is to the structure.
- Provide an API which utilizes `out` variables to keep mathematical operations from instantiating new instances.
- Provide an API which promotes efficient use of data types.

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

#### Calculator< T >

An abstract class that contains all the methods mentioned in [Goals](#calculator-goal). Implementations of Core may not have any calculator implementations since they're tied to specific data types which are tied to other libraries. There will most likely be a library of calculators for any type of library that needs to interface with Core and all other OpenGDS. Calculators are the only classes that need to be created to use any OpenGDS.

The calculator has a static factory method which takes some input that describes a data type and returns the proper calculator. The calculator has a static method to also register a calculator to a data type description.

#### UnaryOperation< S >

A function which takes a component of type `S` and returns a value of type `S`. These functions are passed to the calculator to process each component of a value to generate a new value. Examples of unary operations: `abs`, `floor`, `ceil`, `round`, `sin`, etc.

#### BinaryOperation< S >

A function which takes two components of type `S` and returns a component of type `S`. These functions are passed to the calculator to process each component of two values to generate a new value. Examples of binary operations: `avg`, `min`, `max`, etc.

#### Geometry< T >

An interface that contains all methods mentioned in [Goals](#geometry-goal).

#### Bounds< T >

An implementation of the Geometry interface that satisfies the [Bounds](#bounds) concept.

#### Range< T >

An implementation of the Geometry interface that satisfies the [Range](#range) concept.

#### Line< T >

An implementation of the Geometry interface that satisfies the [Line](#line) concept.

#### Sphere< T >

An implementation of the Geometry interface that satisfies the [Sphere](#sphere) concept.

#### Plane< T >

An implementation of the Geometry interface that satisfies the [Plane](#plane) concept.

## API

This API describes the design showing all variables on a class, methods in an interface or class, and the argument names and types for functions and methods.

The following expressions are used in the API:

- `methodName(): type`: Describes a method with returns a value with the given type.
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

### Calculator< T >

##### `ZERO: T`
  An instance of the data type with components with values of 0.
##### `ONE: T`
  An instance of the data type with components with values of 1.
##### `create(): T`
  Creates a new instance of T with components with values of 0.
##### `create( component:S ): T`
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
##### `scale( out:T, scale:float ): T`
  Multiples the components of `out` by `scale` and returns `out`.
##### `add( out:T, amount:T ): T`
  Adds the components of `amount` to `out` and returns `out`.
##### `adds( out:T, amount:T, scale:float ): T`
  Adds the components of `amount` multiplied by `scale` to `out` and returns `out`.
##### `sub( out:T, amount:T ): T`
  Subtracts the components of `amount` from `out` and returns `out`.
##### `mul( out:T, scale:T ): T`
  Multiplies the components of `out` by the components of `scale` and returns `out`.
##### `div( out:T, denominator:T ): T`
  Divides the components of `out` by the components of `denominator` and returns `out`. If a component of `denominator` is zero the component in `out` will be `zero`.
##### `interpolate( out:T, start:T, end:T, delta:float ): T`
  Sets `out` to the value between `start` and `end` given a `delta` value. When `delta` is 0 `out` will be set to `start`, when `delta` is 1 `out` will be set to `end`, and when `delta` is set to 0.5 then `out` will be set to a value halfway between `start` and `end` (etc).
##### `contain( out:T, min:T, max:T ): T`
##### `contains( out:T, min:T, max:T ): bool`
##### `random( out:T, min:T, max:T, ?randomizer:function ): T`
##### `distance( a:T, b:T ): float`
##### `distanceSq( a:T, b:T ): float`
##### `length( value:T ): float`
##### `lengthSq(valuea:T ): float`
##### `normal( out:T, vector:T ): float`
##### `isFinite( value:T ): bool`
##### `isZero( value:T, ?epsilon:float=0.00001 ): bool`
##### `isEqual( a:T, b:T, ?epsilon:float=0.00001 ): bool`
##### `dot( a:T, b:T ): float`
##### `lengthen( out:T, length:float ): T`
##### `clamp( out:T, min:float, max:float ): T`
##### `sizeof( value:T ): size`
##### `write( value:T, to:Output ): size`
##### `read( out:T, from:Input ): T`
##### `getDimensions(): int`
##### `getComponentCount(): int`
##### `getComponent( value:T, index:int ): S`
##### `setComponent( value:T, index:int, component:S ): T`
##### `slerpDirect( out:T, start:T, end:T, delta:float ): T`
##### `slerpNormal( out:T, start:T, end:T, delta:float ): T`
##### `slerp( out:T, start:T, end:T, delta:T, angle: T ): T`
##### `closest( out:T, start:T, end:T, point:T ): T`
##### `intercept( shooter:T, speed:float, targetPosition:T, targetVelocity:T ): float`
##### `distanceFromLine( start:T, end:T, point:T ): float`
##### `inView( origin:T, direction:T, fovCos:float, point:T ): bool`
##### `isCircleView( origin:T, direction:T, fovTan:float, fovCos:float, center:T, radius:float, entirely:bool ): bool`
##### `cubicCurve( out:T, delta:float, p0:T, p1:T, p2:T, p3:T, matrix[4][4]:float )`
##### `parametricCubicCurve( out:T, delta:float, points[]:T, matrix[4][4]:float, weight:float )`

### Geometry< T >

##### `hasRadius(): bool`
##### `getRadius(): float`
##### `getCenter(): T`
##### `getDistance( point:T ): float`
##### `getDistanceAndNormal( point:T, normal:T ): float`
##### `isInside( point:T ): bool`
##### `isOutside( point:T ): bool`
##### `random( out:T, ?maxDistanceFromCenter:float=infinity, ?randomizer:function ): T`

### Bounds< T > : Geometry< T >

##### `min: T`
##### `max: T`
##### `center: T`
##### `thickness: float`

### Range< T > : Geometry< T >

##### `min: T`
##### `max: T`
##### `center: T`
##### `thickness: float`

### Line< T > : Geometry< T >

##### `start: T`
##### `end: T`
##### `center: T`
##### `thickness: float`

### Sphere< T > : Geometry< T >

##### `center: T`
##### `radius: float`

### Plane< T > : Geometry< T >

##### `origin: T`
##### `normal: T`
##### `thickness: float`

## Additional API

### Calculator< T >

##### `setPool( pool[]:T )`
##### `recycle( value:T ): bool`
##### `min( out:T, a:T, b:T ): T`
##### `max( out:T, a:T, b:T ): T`
##### `avg( out:T, a:T, b:T ): T`
##### `abs( out:T ): T`
##### `floor( out:T ): T`
##### `ceil( out:T ): T`
##### `round( out:T ): T`

## Implementation
