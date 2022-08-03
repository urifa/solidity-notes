# Solidity Notes

Quick Git reference with most used commands.

## Ethereum Virtual Machine (EVM)

## Contract Structure

## Data Location

There are three location types in which EVM stores data: Storage, Memory and Calldata

### Storage

This is the location where state variables are stored, and the lifetime is limited to the lifetime of the contract. You can modify its value, but their location is permanent, and every change is registered on the blockchain.

### Memory

This is the temporary location for variables declared inside a function, and their lifetime is limited to an external function call. You cannot access variables stored in `memory` from anywhere else other than from inside the function in which has beed declared.

### Calldata

This is a non-modifianble and non-persistent area where function arguments are stored. It acts like `memory`, in terms of its dependance on the function execution.

## Data Types

### Value Types

With value types, you can have an independent copy whenever a variable of value type is used.

#### Booleans

#### Integers

#### Fixed Point Numbers

#### Address

#### Enums

#### Contract Types

#### Function Types

### Reference Types

Values of reference type can be modified through multiple different names, so when using a reference type, you need to explicirly provide the data location where the type is stored: `storage`, `memory` or `calldata`.

#### Arrays

#### Structs

### Mappging Types

## Variables

### State Variables

### Local Variables

### Global Variables

## Functions

### Function Parameters

### Return Variable

## Events

### Event Definition

### Event Call

## Interfaces


