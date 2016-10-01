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

Performing mathematical operations on and between data types (scalar, vector, colors, etc) is a necessity in game programming.

## Goals

- Provide a generic API to allow the use of any data type to...
 - be used in mathematical operations:
  - add, subtract, multiple, divide, scale, & interpolate
  - custom unary and binary operations
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
- Provide a definition which utilizes `out` variables to keep mathematical operations from instantiating new instances.
- Provide a definition which promotes efficient use of the data type.
- Provide structures that are composed of multiple data types
 - Range
 - 

## Concepts

### Data Type

### Calculator

### Range

###

## Design

## Core API

## Additional API

## Implementation
