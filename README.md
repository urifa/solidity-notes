# Solidity Notes

Quick Git reference with most used commands.

## Smart Contracts

A smart contract is the fundamental building block of Ethereum applications. A contract in the sense of Solidity is a collection of code (its functions) and data (its state) that resides at a specific address on the Ethereum blockchain.

## Ethereum Virtual Machine (EVM)

The Ethereum Virtual Machine or EVP is the runtime environment for smart contracts in Ethereum.  It is completely isolated, which means that code running inside the EVM has no access to network, filesystems or other processes.

There are two kinds of accounts in Ethereum which share the same address space: **external accounts**, which are controlled by public-private key pairs (i.e. humand), and **contract accounts, which are controlled by the code stored together wit the account.

The address of an external account is determined from the public key while the address of a contract is determined at the time the contract is created (it is derived from the creator address and the number of transactions sent from that address).

Both account types are treated equally by the EVM, and every one of them has a store mapping 256-bit words to 256-bit words called **storage**.

## Data Location

The EVM has three areas where it can store data: **storage**, **memory** and **stack**.

### Storage

Each account has a data area called **storage**, which is persistent between function calls and transactions. This is the location where state variables are stored by default, and the lifetime is limited to the lifetime of the contract. You can modify its value, but their location is permanent, and every change is registered on the blockchain.

Storage is a key-vakue store that maps 256-bit words to 256-bit words. It is not possible to enumerate storage from within a contract, it is comparatively costly to read and even more to initialise and modify.

### Memory

The second data area is called **memory**, and is is a temporary location for variables declared inside a function, and their lifetime is limited to an external function call. You cannot access variables stored in memory from anywhere else other than from inside the function in which has beed declared.

Memory is linear and can be addressed at byte level, but reads are limited to a width of 256 bits.

### Stack

The EVM is not a register machine but a stack machine, so all computations are performed  on a data area called the **stack**. Access to the stack is limited to the top end in the following way: it is possible to copy one of the topmost 16 elements to the top of the stack or swap the topmost element with one of the 16 elements below it.

The stack has a maximum size of 1024 elements and contains 256 bits. It is possible to move stack elements to storage or memory in order to get deeper sccess to the stack, but it is not possible to just access arbitrary elements deeper in the stack without first removing the top of the stack.

### Calldata

Contracts can call other contracts or send Ether to noncontract accounts by the means of message calls. Messahe calls are similar to transactions, in that they have a source, a target, data payload, Ether, gas, and return data. In fact, every transaction consists of a top-level message call which in turn can create further message calls. A contract can decide how much of its remaining gas should be sent with the inner message call and how much it wants to retain.

The called contract (which can be the same as the caller) will receive a freshly cleared instance of memory and has access to the call payload - which will be provided in a separate area called the **calldata**. Calldata is a non-modifianble and non-persistent area where function arguments are stored. It acts like memory, in terms of its dependance on the function execution.

## Solidity Source File Layout

### License Identifier

Every source file should start with a comment indicating its license.

```
// SPDX-License-Identifier: MIT
```
In this case, you are using the MIT license.

### Pragmas

A Solidity source code should start with the `pragma` keyword, which is used to enable certain compiler features or checks. Tipically is used as a declaration of the version of the Solidity compiler this code should use.

The version pragma is used as follows:

```
pragma solidity ^0.5.2;
```
This indicates that the source file does not compile with a compiler earlier than version 0.5.2, and it does not work on a compiler starting from version 0.6.0.

```
pragma solidity >=0.5.0 <0.6.0;
```
This indicates that the source file will compile with any compiler version in the range of 0.5.0 to 0.6.0 (exclusive).

### Contract Declaration

Solidity's code is encapsulated in `contract`. Contracts in Solidity are similar to classes in object-oriented languages. Each contract can contain declarations of State Variables, Functions, Function Modifiers, Events, Errors, Struct Types and Enum Types.

Contracts declaration follows this pattern:

```
contract Animal {
    // ...
}
```
Declare a contract named "Animal".

Contracts can inherit from other contracts. This means that when you compile and deploy the current contract, it will have access to all parent contract functions. The scope of inheritance in Solidity is limited to `public` and `internal` modifiers only.

Functions from a parent contract that are going to be overridden by a child contract must be declared as `virtual`, and functionns that are going to override a parent function must use the leyword `override`.

Contract inheritance is indicateed by `is` keyword, as you can see in the following example:

```
contract Cat is Animal {
    // ...
}
```
Declare a contract named "Cat" which inherits from the contract "Animal".

## Variables

A variable is a placeholder for data which can be manipulated at runtime. Variables allow users to retrieve and chage the stored information. 

### State Variables

State variables are stored in the contract storage area. They are stored in a compact way such that multiple values sometimes use the same storage slot.

Except for dynamically-sized arrays and mappings, data is stored contiguously item after item starting with the first state variable, and for each variable, a size in bytes is determined according to its type.

Dynamically-sized arrays an mappings, due to their unpredictable size ¡, cannot be stored "in between" the state variables preceding and following them. Instead, they are considered to occupy  only 32 bytes  and the elements they contain are stored starting at a different storage slot that is computed using a Keccak-256 hash.

There are cases where you will need to explicitly declare is a variable is stored in storage or in memory using the keywords `storage` and `memory`, namely when dealing with structs and arrays within functions.

### Local Variables

Local variables are declared 

### Global Variables

## Data Types

Solidity is a statically typed language, which means that the type of each variable (state or local) needs to be specified. Solidity provides several elementary types which can be combined to form complex types.

### Value Types

Value types are called this way because variables of these types will always be passed by value, i.e. they are always copied when they are used as function arguments or in assignments. With value types, you can have an independent copy whenever a variable of value type is used.

#### Booleans

Boolean types are indicated by `bool` keyword, and accept only two possible values: `true` and `false`.

These are some example for boolean declarations. You can declare the variable, and also assign it to a specific value.

```
bool gameStart;  // Unassigned bool variables are equal to false by default
bool gameOver= true;
```

#### Integers

Integer types signed, indicated by `int` keyword, or unsigned, indicated by `uint` keyword. Integers in solidity are restricted by a certain range, and different ranges are available from `int8` or `uint8` to `int256` or `uint256`. 

These are some examples for both signed and non-signed integer declarations. 

```
int128 a;  // int128 ranges from -2 ** 127 to 2 ** 127 - 1 
int256 b;  // int256 ranges from -2 ** 255 to 2 ** 255 - 1
uint8 c;  // uint8 ranges from 0 to 2 ** 8 - 1
uint256 d;  // uint256 ranges from 0 to 2 ** 256 - 1
uint e = 450;  // uint is an alias for uint256
```

#### Address

Address type is indicated by `address` or `address payable` keywords, and  holds a 20 byte value, which is the size of an Ethereum address. The difference between the two types is that `address payable` is an address you can send Ether to, while you are not supposed to send Ether to a plain `address`.

These are some examples for address declarations:

```
address addr1;  
address payable addr2 = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;
```

#### Contract Types

Every contract defines its own type. You can implicitly convert contracts to contracts they inherit from. Contracts can be explicitly converted to and from the `address` type.

You can declare a local variable of contract type, and call functions on that contract. The members of contract types are the external functiobs of the contract including any state variable marked as `public`.

In this example, we use the contract "Animal" declared before, to declare a local variable of this contract type named "animal":

```
Animal animal;
```

#### Function Types

Function types are the type of functions. They come in two flavours: internal and external functions. Internal functions can only be called inside the current contract, and external functions consist of an address and a function signature, and they can be passed via and returned from externa function calls.

You can mark a function as internal or external using the `internal` and `external` keywords, but by default function types are internal, so the `internal` keyword can be omitted. Function types accept these parameter types: `pure`, `view` and `payable`.

In this example, we declare a function named "eat", that accept a `uint` parameter, that is public, and returns nothing:

```
function eat(uint) public {
    // ...
}
```

### Reference Types

Values of reference type can be modified through multiple different names, so when using a reference type, you need to explicirly provide the data location where the type is stored: `storage`, `memory` or `calldata`.

#### Arrays

#### Structs

### Mappging Types

## Functions

Functions consist a data type for themselves.

### Function Parameters

Functions take typed parameters as input

### Visibility Specifier

Solidity knows two kinds of function calls: external ones that do create an actual EVM message call, and internal ones that do not. Furthermore, internal functions can be made inaccessible to derived contracts. This gives rise to four types of visibility functions. 

- External functions, declared using the `external` keyword, are part of the contract interface, which means they can be called form other contracts and via transactions, but cannot be called internally.
- Public functions, declared using the `public` keyword, are part of the contract interface, and be either be called internally or via message calls.
- Internal functions, declared using the `internal` keyword, can only be accessed from within the current contract or contracts deriving from it, but cannot be accessed externally. They are not exposed to the outside through the contract's ABI.
- Private functions, declared using the `private` keyword, are like internal ones, but they are not visible in derived contracts.

The visibility specifier is given after the parameter list.

### State Mutability

Functio

- View functions, declared using the `view` keyword, enforces the state in the blockchain to be unmodified as part of the EVM execution. Getter methods are automatically marked as `view`.
- Pure functions, declared using the `pure` keyword, 

### Return Variable


## Events

### Event Definition

### Event Call

## Interfaces


