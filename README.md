# Solidity Notes

Quick Git reference with most used commands.

## Smart Contracts and Solidity 

Smart contracts are programs which govern the behaviour of accounts within the Ethereum state, and represent the fundamental building block of Ethereum applications. A contract in the sense of Solidity is a collection of code (its functions) and data (its state) that resides at a specific address on the Ethereum blockchain.

There are two kinds of accounts in Ethereum which share the same address space: **external accounts**, which are controlled by public-private key pairs (i.e. humand), and **contract accounts**, which are controlled by the code stored together with the account.

The address of an external account is determined from the public key while the address of a contract is determined at the time the contract is created and deployed (it is derived from the contract creator address and the number of transactions sent from that address).

The smart contract address is determined during its deployment and derives from the contract creator address and the number of transactions sent from that address. Every contract re-deployment results in a new contract address. This address will be visible on the Blockchain, and its code will be inmutable, just like a normal transaction.

Deploying a contracts is similar to signing a transaction, and the address who deploys the contracts will spend some amount of Gas during its creation, decreasing their Ether balance as well.

If the contract source code needs to be modified, a new contract deployment will be needed, so this will result in a different contract address (the old contracts will continue existing on its own contract address).

Contracts users also can modify the state while interacting with the contract. Every function inside the contract that modifies the state of the blockchain results in a new transaction on the blockchain, with a Gas cost associated. Gas consumption depends on how computationally expensive is the action performed.

Solidity is an object-oriented, high-level language for implementing smart contracts, and is designed to target the Ethereum Virtual Machine or EVM, which is the runtime environment for smart contracts in Ethereum.

## Ethereum Virtual Machine (EVM)

The EVM is the standard for how to deploy smart contracts in Ethereum' like blockchains. Some examples of EVM compatible blockchains are Avalanche, Fantom and Polygon. This means that you can write your solidity code and deploy it to these blockchains.

The EVM is completely isolated, which means that code running inside the EVM has no access to network, filesystems or other processes.

Both account types existing in Ethereum are treated equally by the Ethereum, and every one of them has a store mapping 256-bit words to 256-bit words called **storage**, as we will see next.

### Data Area Location

The EVM can access and store data in six areas: **storage**, **memory**, **calldata**, **stack**, **code** and **logs**, although when you declare a variable, only can only specify three of these areas:  storage, memory and calldata.

#### Storage

Each account has a data area called **storage**, which is persistent between function calls and transactions. This is the location where state variables are stored by default, and the lifetime is limited to the lifetime of the contract. You can modify its value, but their location is permanent, and every change is registered on the blockchain.

Storage is a key-vakue store that maps 256-bit words to 256-bit words. It is not possible to enumerate storage from within a contract, it is comparatively costly to read and even more to initialise and modify.

#### Memory

Memory is is a temporary location for variables declared inside a function, and their lifetime is limited to an external function call. You cannot access variables stored in memory from anywhere else other than from inside the function in which has beed declared.

Memory is linear and can be addressed at byte level, but reads are limited to a width of 256 bits.

#### Calldata

Calldata is a non-modifianble and non-persistent area where function arguments are stored. It acts like memory, in terms of its dependance on the function execution, but, as opposed to memory, data stored in calldata cannot be modifyed.

Contracts can call other contracts or send Ether to noncontract accounts by the means of message calls. Messahe calls are similar to transactions, in that they have a source, a target, data payload, Ether, gas, and return data. In fact, every transaction consists of a top-level message call which in turn can create further message calls. A contract can decide how much of its remaining gas should be sent with the inner message call and how much it wants to retain.

The called contract (which can be the same as the caller) will receive a freshly cleared instance of memory and has access to the call payload - which will be provided in a separate area called the **calldata**.

#### Stack

The EVM is not a register machine but a stack machine, so all computations are performed  on a data area called the **stack**. Access to the stack is limited to the top end in the following way: it is possible to copy one of the topmost 16 elements to the top of the stack or swap the topmost element with one of the 16 elements below it.

The stack has a maximum size of 1024 elements and contains 256 bits. It is possible to move stack elements to storage or memory in order to get deeper sccess to the stack, but it is not possible to just access arbitrary elements deeper in the stack without first removing the top of the stack.

## Data Types

Solidity is a statically typed language, which means that the type of each variable (state or local) needs to be specified. Solidity provides several elementary types which can be combined to form complex types.

### Value Types

Value types are called this way because variables of these types will always be passed by value, i.e. they are always copied when they are used as function arguments or in assignments. With value types, you can have an independent copy whenever a variable of value type is used.

#### Booleans

Boolean types are indicated by `bool` keyword, and accept only two possible values: `true` and `false`. 

You can declare a variable with a boolean type  using this syntax: `bool <variable_name>`, and can assign it to a specific value or leave it undefined (which takes the value `false` by default). These are some examples:

```
bool bool1;
bool bool2 = true;
```

#### Integers

Integer types can be signed or unsigned. Signed integer types are indicated by `int` keyword, while unsigned unsigned ones are indicated by `uint` keyword. Integers in solidity are restricted by a certain range, and different ranges are available from `int8` or `uint8` to `int256` or `uint256`. 

You can declare a variable with an integer data type using this syntax: `int <variable_name>` for a signed one and `uint <variable_name>` for an unsigned one. You can also assign it to a specific value or leave it undefined. These are some examples: 

```
int128 a;  // int128 ranges from -2 ** 127 to 2 ** 127 - 1 
int256 b;  // int256 ranges from -2 ** 255 to 2 ** 255 - 1
uint8 c;  // uint8 ranges from 0 to 2 ** 8 - 1
uint256 d;  // uint256 ranges from 0 to 2 ** 256 - 1
uint e = 450;  // uint is an alias for uint256
```

#### Address

Address types are indicated by `address` or `address payable` keywords, and hold 20 bytes values, which is the size of an Ethereum address. The difference between the two types is that `address payable` is an address you can send Ether to, while you are not supposed to send Ether to a plain `address`.

You can declare a variable with an address type using this syntax: `address <variable_name>` for addresses, and `address payable <variable_name>` for payable addresses. These are some examples:

```
address addr1;  
address payable addr2 = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;
```

Address types can be explicitly converted to and from a contract type. To convert a contract instance to its corresponding contract address you can use the following syntax: `address(<ContractName>)`. This is an example:

```
address addr3 = address(this)  // Address of the current contract instance;
```

Address types come with some members. The most important ones are `<address>.balance`, which queries the balance for the given address, `<address payable>.transfer(<amount>)` which transfers a given amount of Ether to that address, and `<address payable>.send(<amount>)`, which does the same but returning the value "false" on failure. These are some examples:

```
addr1.balance;  // Query the balance in Wei of addr1
addr2.transfer(uint256 1000);  // Send 1000 Wei to addr2. Forwards 2300 gas stipend
addr2.send(uint256 1000);  // Send 1000 Wei to addr2. Forwards 2300 gas stipend
```

#### Contract Types

Every contract defines its own type. You can implicitly convert contracts to contracts they inherit from.

Contracts are declared using the `contract` keyword, followed by the name of the contract. The syntax is the following: `contract <ContractName> { // Code}`. Example:

```
contract ContractTest {
    uint a;
}
```

Once you declare a contract, it will result on a type on its own, so you can declare a variable containing an instamce of that contract, indicating the name of the contract as the data type for the variable. Example:

```
ContractTest contractTest;
```

Contract types can be explicitly converted to and from the address type. To convert an address to its corresponding contract instance you can use the following syntax: `<ContractName>(<ContractName Address>)`. Note that the address passed in should correspond to a specific instance of the contract. This is an example, assuming we have deployed ContractTest, and there is an instance of ContractTest in address 0x9D7f74d0C41E726EC95884E0e97Fa6129e3b5E99.

```
ContractTest contractTest = ContractTest(0x9D7f74d0C41E726EC95884E0e97Fa6129e3b5E99);
```

The members of contract types are the external functions of the contract including any state variable marked as `public`.

We will cover contracts in more detail later in the *Contracts* section.

#### Function Types

Function types are the type of functions. 

Functions can be internal (can only be called inside the current contract) and external (consist of an address and a function signature, and they can be passed via and returned from external function calls). You can mark a function as internal or external using the `internal` and `external` keywords, but by default function types are internal.

Function types also accept these parameter types: `pure`, `view` and `payable`.

In this example, we declare a function named "eat", that accept a `uint` parameter. We declare it as a `public` and `view` function, and it returns a uint256 value:

```
function eat(uint) public view returns (uint256) {
    // ...
}
```

We will cover functions in more detail later in the *Functions* section.

### Reference Types

Values of reference type can be modified through multiple different names, so when using a reference type, you need to explicitly provide the data location where the type is stored: `storage`, `memory` or `calldata`. It is not only relevant for persistency of data, but also for the semantics of assignments. 

- Assignments between `storage` and `memory` always create an independent copy.
- Assignments from `memory` to `memory` only create references, so changes to one memory variable are also visible in all other memory variables referring to the same data.
- Assignments from `storage` to a local storage variable also only assign a reference.

Reference type values in Solidity are arrays, structs and mappings. The data location needs to be specified when passing one of these data types as parameters in functions. Data location, in fact, can only be specified for these data types.

#### Arrays

Arrays are data structures that holds a list of other types. Arrays can have a compile-time fixed size, or a dynamic size. Array elements can be of any type, including mapping or struct. 

You can declare a variable with an array data type indicating the data type of their elements with brackets followed by the name of the array. The syntax is the following: `<element_data_type>[] <variable_name>` for fixed size arrays and `<element_data_type>[<n>] <variable_name>` for dynamic arrays. These are some examples:

```
uint[3] public arr1 = [1, 2, 3];
uint[] public arr2;
```

Memory arrays with dynamic length can be created using the `new` operator. As opposed to storage arrays, it is not possible to resize memory arrays.

Array data types come with some members. The most important ones are `<array>.length`, which prints the length of the given array, `<array>.push(<el>)` which appends a given element at the end of the array, and `<array>.pop()`, which removes an element from the end of the array. These are some examples:

```
arr1.length;  // Query the length of arr1
arr2.push(1);  // Append the value 1 at the end of arr2
arr2.pop();  // Remove an element from the end of the array
```

Both `<array>.push(<el>)` and `<array>.pop()` are only available for dynamic storage arrays and have a gas cost.

#### Structs

Struct data types provides a way of grouping together related data. In all functions, a struct type is assigned to a local variable with data location `storage`. This does not copy the struct but only stores a reference so that assignments to members of the local variable actually write to the state.

Structs are declared using the `struct` keyword, followed by the name of the struct. The syntax is the following: `struct <StructName> { // Parameters}`. Struct parameters are declared as other variables. This is an example:

```
struct Book {
    string name;
    string writter;
    uint id;
}
```

Once you declare an struct, it will result on a type on its own, so you can declare variables containing instance of that struct, indicating the name of the struct as the data type for the variable. This is an example:

```
Book book1 = Book("Mastering Etheruem", "Aantonop", 1001);
Book book2;
```

You can query for the different variables of a struct instance using the dot notation: `<variable_name>.<parameter_name>`. This is an example:

```
book1.name;
```

#### Mappings

Mapping type is a reference type that stores the data in a key-value pair where a key, which can be of any data type, is mapped to a single value. 

Mappings are declared using the syntax `mapping(keyType => ValueType) <mapping_name>`. This is an example:

```
mapping(address => uint) public balances;  // Declare a mapping called balances to map an address to a certain amount
```

You can query a value associated to some key in a mapping using the syntax `<mapping_name>[<key>]`. This is an example:

```
balances[msg.sender] = 1000;  // Set the value 1000 to the address "msg.sender" in balances mapping
```

## State Variables

A variable is a placeholder for data which can be manipulated at runtime. Variables allow users to retrieve and chage the stored information. State variables are stored in the contract storage area. They are stored in a compact way such that multiple values sometimes use the same storage slot.

Except for dynamically-sized arrays and mappings, data is stored contiguously item after item starting with the first state variable, and for each variable, a size in bytes is determined according to its type.

Dynamically-sized arrays an mappings, due to their unpredictable size, cannot be stored "in between" the state variables preceding and following them. Instead, they are considered to occupy  only 32 bytes  and the elements they contain are stored starting at a different storage slot that is computed using a Keccak-256 hash.

There are cases where you will need to explicitly declare is a variable is stored in storage or in memory using the keywords `storage` and `memory`, namely when dealing with structs and arrays within functions.

### State Variable Visibility

State variables can be declared as **public**, **internal** or **private**.

- **Public state variables** are declared using the `public` keyword after the data type. They are similar to internal ones, but differ in that the compiler automatically generates getter functions for them, which allows other contracts to read their values. When used within the same contract, the external access (e.g. `this.x`) invokes the getter, while internal access (e.g. `x`) gets the variable value directly from storage. Setter functions are not generated so other contracts cannot directly modify their values.
- **Internal state variables** are declared using the `internal` keyword after the data type, although this is not needed, as it is the default visibility level for state variables. They can only be accessed from within the contract they are defined in and in derived contracts. They cannot be accessed externally.
- **Private state variables** are declared using the `private` keyword after the data type. They are like innternal ones but they are not visible in derived contracts.

## Global Variables and Functions

There are special variables and functoins which always exist in the global namespace and are mainly used to provide information about the blockchain or are general-use utility functions. These are the most used global variables and functions.

Parameters of the global variable `block`:

```
block.basefee;  // Current block's base fee (uint)
block.chainid;  // Current chain id (uint)
block.difficulty;  // Current block difficulty (uint)
block.gaslimit;  // Current block gas limit (uint)
block.number;  // Current block number (uint)
block.timestamp;  // Current block timestamp as seconds since unix epoch (uint)
```

Parameters of the global variable `msg` (they can change for every external function call):

```
msg.data;  // Complete calldata (bytes calldata)
msg.sender;  // Sender of the message in the current call (address)
msg.value;  // Number of wei sent with the message (uint)
```

Parameters of the global variable `tx`:

```
tx.gasprice;  // Gas prive of the transaction (uint)
tx.origin;  // Sender og the transaction (address)
```

ABI encoding and decoding functions:

```
abi.decode(bytes memory encodedData, (...)) returns (...); // ABI-decodes the given data. Types are given in parentheses
abi.encode(...) returns (bytes memory);  // ABI-encodes the given data
abi.encodePacked(...) returns (bytes memory);  // Packed encoding of the given arguments
```

Cryptographic functions:

```
keccak256(bytes memory) returns (bytes32);  // Compute the Keccak-256 hash of the input
sha256(bytes memory) returns (bytes32);  // Compute the SHA-256 hash of the input
ripemd160(bytes memory) returns (bytes20);  // Compute RIPEMD-160 hash of the input
```

Error handling functions:

```
assert(bool <condition>);  // Causes panic error and state change reversion if the condition is not met. To be used for internal errors
require(bool <condition>, string memory <message>);  // Reverts if the condition is not met. To be used for external components
revert(string memory <reason>);  // Abort execution and revert state changes
```

Contract related variables and fucntions:

```
this;  // Instance of the current contract, explicitly convertible to address with `address(this)` (current contract's type)
selfdestruct(address payable <recipient>);  Destroy the current contract sending its funds to the given address
```

## Contracts

Contracts in Solidity are similar to classes in object-oriented languages, and they consist a data type for themselves. Each contract can contain declarations of State Variables, Functions, Function Modifiers, Events, Errors, Struct Types and Enum Types.

Contracts are declared using the keyword `contract`, followed by the name of the contract. The syntax is the following:

```
contract <ContractName> {
    //...
}
```

Contracts are composable, meaning they can interact with each other. When you want a contract to interact with another contracts, or to inherit from another contract, the current contract needs to know the source code of the contract to interact with, or to inherit from. To do this, you can have coth contracts on the same file, or import one contract' file into the other.

### Import Contract

Importing the path of one contracts' file to another is like copying their source code, so makes the same effect of having both contracts o the same file. The import statement is usually added after the pragma version of the current contract, and has the following syntax:

```
import "<contract1-file-path>";
```

### Deploy Contract from another Contract

You can create a function inside a contract that actually deploys another contract, and then interact with it. To do that, you can use the keyword `new` followed by the name of the contract you want to deploy. The syntax is the following:

```
new <ContractName>()
```

In this example, we have a first contract, named "StorageFactory" that will be used to deploy another contract, named "SimpleStorage", so it will be able to interact with it.

SimpleStorage is a contract that has two functions, one to store a number, and another to retrieve that number.

SimpleStorage contract code:

```
contract SimpleStorage {

    uint256 num;
    
    // Declared as a virtual function to be overridable by a child contract
    function store(uint256 _num) public virtual {
        num = _num;
    }
    
    function retrieve() public view returns (uint256) {
        return num;
    }
}
```

StorageFactory is a contract that has a function that deploys the SimpleStorage contract, and keeps track of every SimpleStorage contract being deployed on an array. Then, it has two other functions that pick one of the SimpleStorage contracts being deployed from the array, and call functions store() and retrieve() from it. 

StorageFactory contract code:

```
import "./SimpleStorage";

contract StorageFactory {

    // Array of ContractStorge contract type objects 
    SimpleStorage[] public simpleStorageArray;
    
    // Function to deploy SimpleStorage contract and add to the array
    function createSimpleStorage() public {
        // Deploy SimpleStorage contract and save it to state variable storage
        SimpleStorage simpleStorage = new SimpleStorage();
        
        // Add the contract to simpleStorageArray
        simpleStorageArray.push(simpleStorage);
        
    // Function tha calls function store() from SimpleStorage contract
    function sfStore(uint256 _simpleStorageIndex, uint256 _simpleStorageNum) public {
    
        // Select Storage contract from the array of Storage contract objects 
        SimpleStorage simpleStorage = simpleStorageArray[_simpleStorageIndex];
        
        // Call function store() from SimpleStorage contract object
        simpleStorage.store(_simpleStorageNum);
    }
    
    // Function that calls function retrieve() from Storge contract
    function sfGet(uint256 _simpleStorageIndex) public view returns(uint256) {
        // Select Storage contract from the array of Storage contract objects
        SimpleStorage simpleStorage = simpleStorageArray[_simpleStorageIndex];
        
        // Call function retrieve() from SimpleStorage contract object
        return simpleStorage.retrieve();
    }
}
```

### Contract Inheritance

Contracts can inherit from other contracts. This means that when you compile and deploy the current contract, it will have access to all parent contract functions. The scope of inheritance in Solidity is limited to `public` and `internal` modifiers only.

Contract inheritance is indicated during the contract declaration, adding the `is` keyword after the current contract, followed by the name of the contract it inherits from. This is the syntax:

```
contract <ChildContractName> is <ContractNameParent> {
    //...
}
```

From the child contract, you have access to all parent contract functions, in addition to new functions created directly on the child contract. Furthermore, it is also possible to override some functions from the parent contract. In this case, functions that are going to be overridden by a child contract must be declared as `virtual` in the parent contract, and functions that are going to override a parent function must use the leyword `override`.

In this example, we add another contract named "ExtraStorage" that inherits from the "SimpleStorage" contract of the previous example. ExtraStorageContract has function named store() that overrides the function store() from the SimpleStorage contract.

ExtraStorage contract code:

```
contract ExtraStorage is SimpleStorage {
    
    function store(uint256 _num) public override {
        num = _num + 5;
    }
}
```

## Functions

Functions consist a data type for themselves. 

Functions are declared using the keyword `function`, followed by the name of the function and all parameters and return parameters. Function code is inside curly brackets. The syntax is the following:

```
function <function-name>() {
    // ...
}
```

### Function Local Variables

A function creates its own scope, meaning that variables declared inside a function are only visible in the context of that function. This variable cannot be called from outside this function. Data area location can be `storage`, `memory` or `calldata` for local variables in functions, but it only have to be specified for array (including strings), struct and mapping types.

In this example, we declare a function called "func", with three local variables declared inside it:

```
function func() {
    uint a = 2;
    uint b = 4;
    string memory message;
}
```

### Function Parameters

Functions take typed parameters as input. They are declared the same way as variables, and the name of the unused parameters can be omitted. Function parameters can be used as any other local variable and they can also be assigned to. Data area location must be `memory` or `calldata` for parameters in functions, but it only have to be specified for array (including strings), struct and mapping types.

In this example, we declare a function called "func" passing three parameters to it, specifying the data type for each parameter. Inside the function we declare three local variables, assigning to them the values passed as parameters:

```
function func(uint _a, uint _b, string memory _message) {
    uint a = _a;
    uint b = _b;
    string memory message = _message;
}
```

### Function Visibility

Solidity knows two kinds of function calls: external ones that do create an actual EVM message call, and internal ones that do not. Furthermore, internal functions can be made inaccessible to derived contracts. This gives rise to four types of visibility functions. 

- **External functions** are declared using the `external` keyword. They are part of the contract interface, which means they can be called form other contracts and via transactions, but cannot be called internally.
- **Public functions** are declared using the `public` keyword. They are part of the contract interface, and can be either be called internally or via message calls.
- **Internal functions** are declared using the `internal` keyword. They can only be accessed from within the current contract or contracts deriving from it, but cannot be accessed externally. They are not exposed to the outside through the contract's ABI.
- **Private functions** are declared using the `private` keyword. They are like internal ones, but they are not visible in derived contracts.

The visibility specifier on a function is given after the parameter list and before the return list.

In this example, we declared the same function of the previous example, but adding the keyword `public` as a visible specifier:

```
function func(uint _a, uint _b, string memory _message) public {
    uint a = _a;
    uint b = _b;
    string memory message = _message;
}
```

### State Mutability

Functions can also de defined as view functions or pure functions. View and pure functions don't modify the state of the blockchain. When called alone, they don't result in a new transaction on the blockchain, so there is no Gas associated.

- View functions are declared using the `view` keyword. They promise not to modify the state of the blockchain. When called, Getter methods are automatically marked as `view`.
- Pure functions are declared using the `pure` keyword. They promise not to read from or modify the state of the blockchain. It should be possible to evaluate a pure function at compile-time given only its inputs and `msg.data`, but without any knowledge of the current blockchain state.

The `view` and `pure` keywords are usually given after the parameter list and visibility specifier, and before the return list.

In this example, we declare same function of the previous example, but adding the `pure` keyword to declare it as a pure function, as it does not modify the state (all variables assigned are declared inside the function, so they are not state variables, but only exists in the scope of the function):

```
function func(uint _a, uint _b, string memory _message) public pure {
    uint a = _a;
    uint b = _b;
    string memory message = _message;
}
```

### Return Variables

Function return variables are declared with the same syntax of function parameters, and after the `returns` keyword, although the names of return variables can be omitted. Return variables can be used as any other local variable and they are initialized with their default value and have that value until they are (re-)assigned. Data area location must be `memory` or `calldata` for return parameters in functions, but it only have to be specified for array (including strings), struct and mapping types.

In this example, we declare the same function of the previous example, but adding a return variable:

```
function func(uint _a, uint _b, string memory _message) public pure returns (uint) {
    uint a = _a;
    uint b = _b;
    string memory message = _message;
    return a + b;
}
```

## Constructors

A constructor is an optional function that is automatically executed upon contract creation and where you can run contract initialisation code.

Before the constructor code is executed, state variables are initialised to their specified value if you initialise them inline, or their default value of you do not.

After the constructor has run, the final code of the contract is deployed to the blockchain. It includes all functions that are part of the public interface and all functions that are reachable from there through function calls, but it does not include the constructor code or internal functions that are only called from the constructor.

Constructors are declared using the keyword `constructor`, and constructor code is inside curly brackets. The syntax is the following:

```
constructor() {
    // ...
}
```

Constructors can accept parameters, which are values that the user who deploys the contract will need to specify at the contract deployment.

In this example, we declare a constructor that accepts a parameter, and initialize two state variables according to the address of the user who deploys the contract and the value they pass as a parameter.

```
address public owner;
uint public amount;

constructor(uint _amount) {
    owner = msg.sender;
    amount = _amount;
}
```

## Modifiers

Modifiers can be used to change the behaviour of functions in a declarative way. For example, you can use a modifier to automatically check a condition prior to executing the function. If the function does not meet the modifier requirement, an exception is thrown, and the function execution stops.

It is only possible to use modifiers defined in the current contract or its base contracts. Modifiers can also be defined in libraries but their use is limited to functions of the same library. Modifiers cannot implicitly access or change the arguments and return values of functions they modify. Their values can only be passed to them explicitly at the point of invocation.

Modifiers are declared using the keyword `modifier`, followed by the name of the modifier. Modifier code is inside curly brackets. The syntax is the following:

```
modifier <modifier-name>() {
    // ...
    _
}
```
In this example, we define a modifier that checks 

Once declared, modifiers can be added in the declaration of a function


## Events

### Event Definition

### Event Call

## Interfaces


