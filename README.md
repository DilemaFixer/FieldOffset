# fieldoffset

// Todo: chack code in HtmlPuzzles

Go package for calculating field offsets in structs using pointer arithmetic and Go's type system. Provides compile-time field offset calculation with caching for efficient access to nested struct fields.

## Overview

This package uses pointer arithmetic and Go's built-in type reflection to calculate memory offsets to struct fields, essentially doing the compiler's job at runtime. It leverages Go's constant type table to get type information and find the offset path to fields specified by dot-notation paths like "First.Second.EndPoint".

## Types

```go
type ChachedPathStep struct {
    Offset    uintptr
    IsPointer bool
    FieldName string
    FieldType reflect.Type
    Childrens map[string]*ChachedPathStep
}
```

## Main API

```go
func FindOffsetForField(obj any, path []string) (uintptr, []uintptr, error)
```

Returns:
- `uintptr` - Final offset to the target field
- `[]uintptr` - Array of offsets to pointer dereferences along the path
- `error` - Error if field path is invalid

## Internal Functions

### Core Logic
```go
func findOffsetForField(_type reflect.Type, path []string) (uintptr, []uintptr, error)
```

### Caching System
```go
func checkFieldOffsetInCache(path []string) (uintptr, *ChachedPathStep, []uintptr, bool)
func cacheMiss() (uintptr, *ChachedPathStep, []uintptr, bool)
func cacheToGlobal(name string, isPointer bool, offset uintptr, _type reflect.Type) *ChachedPathStep
func cacheAsChildren(parent *ChachedPathStep, name string, isPointer bool, offset uintptr, _type reflect.Type) *ChachedPathStep
```

### Path Processing
```go
func cutPathPartThetExistInChache(step *ChachedPathStep, path []string) []string
```

## Global Cache

```go
var g_PathsChache = make(map[string]*ChachedPathStep, 0)
var g_CachedBreanchesCount uint = 0
```

## Usage Example

```go
type Inner struct {
    Value int
}

type Outer struct {
    First *Inner
}

obj := Outer{}
path := []string{"First", "Value"}
offset, pointerOffsets, err := FindOffsetForField(obj, path)
```

## Features

- **Pointer arithmetic**: Direct memory offset calculation
- **Caching**: Hierarchical caching system for performance
- **Pointer handling**: Tracks pointer dereferences in nested structures  
- **Type safety**: Uses Go's reflection system for type validation
- **Path navigation**: Supports dot-notation field paths
