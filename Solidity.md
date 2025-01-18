---
layout: single
title: Solidity with CryptoZombies
---

# 1. Contracts
* Solidity's code is encapsulated in contracts. A `contract` is the fundamental building block of Ethereum applications — all variables and functions belong to a contract.

# 2. Version Pragma
* All solidity source code should start with a `version pragma` — a declaration of the version of the Solidity compiler this code should use. This is to prevent issues with future compiler versions potentially introducing changes that would break your code.

# 3. State Variables
* State variables are permanently stored in contract storage. This means they're written to the Ethereum blockchain.

# 4. `uint`
* In Solidity, uint is actually an alias for `uint256`, a 256-bit unsigned integer. You can declare `uint` with less bits — `uint8`, `uint16`, `uint32`, etc.. But in general you want to simply use `uint`. There's also an `int` data type for signed integers.

# 5. `public`
* `public` is an access modifier. It means that Solidity automatically creates a function that allows you to access the variable from outside of the contract.
```solidity
// example public array
Person[] public people;
```

# 6. Arrays
* There are two types of arrays in Solidity: fixed arrays and dynamic arrays:
    * Fixed arrays: `uint[3]`
    * Dynamic arrays: `uint[]`
* `array.push()` returns a uint of the new length of the array

# 7. Functions
* A function declaration in solidity looks like the following:
```solidity
function eatHamburgers(string memory _name, uint _amount) public {}
```
* Note that we're specifying the function visibility as `public`. We're also providing instructions about where the `_name` variable should be stored - in `memory`.
* In addition to **`public`** and **`private`**, `Solidity` has two more types of visibility for functions: **`internal`** and **`external`**.
* `internal` is the same as `private`, except that it's also accessible to contracts that inherit from this contract.
* `external` is similar to `public`, except that these functions can **ONLY** be called outside the contract — they can't be called by other functions inside that contract.
* It's convention to start private function names and any function parameters with an underscore `_`:
```solidity
function _addToArray(uint _number) private {
  numbers.push(_number);
}
```
* **Return Values**: Functions can return values. You can specify the type of the return value by adding it after the parameter list and access modifier:
```solidity
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}
```
* **View Functions**: If a function is only reading data from the blockchain, you can mark it as `view`. This will save you some gas:
```solidity
function sayHello() public view returns (string memory) {}
```
* **Pure Functions**: If a function doesn't read from the blockchain at all (i.e., it only depends on its parameters), you can mark it as `pure`. This will save you even more gas:
```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

# 8. Keccak256 and Typecasting
* Keccak256 is a hash function used in Ethereum. It takes in a variable number of `bytes` and returns a 256-bit hash (32 bytes). It takes in just one parameter of type `bytes`. This means that we have to `"pack"` any parameters before calling keccak256:
```solidity
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256(abi.encodePacked("aaaab"));
```
* Typecasting is the process of converting one data type to another. In Solidity, we can typecast a variable by putting the type we want to convert to in `parentheses` before the variable:
```solidity
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```

# 9. Events
* Events allow you to emit log data to the Ethereum blockchain. You can subscribe to these events with client-side code. Events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen:
```solidity
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```
* Your app front-end could then listen for the event. A JavaScript implementation would look something like:
```javascript
YourContract.IntegersAdded(function(error, result) {
  // do something with the result
})
```

# 10. Accounts
* In ETH blockchain, each account has an address, which you can think of like a bank account number. It's a unique identifier that points to that account.
* An `address` is owned by a specific `user` (or a `smart contract`).
* In crypto-zombies tutorial, when a user creates new zombies by interacting with our app, we set ownership of those zombies to the Ethereum address that called the function.

# 11. Mappings
* A mapping is a data structure that maps keys to values.
```solidity
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

# 12. msg.sender
* **NOTE**: In Solidity, there are certain global variables that are available to all functions. One of these is `msg.sender`, which refers to the `address` of the person (or smart contract) who called the current function. 
* **NOTE**: In Solidity, function execution always needs to start with an external caller. A contract will just sit on the blockchain doing nothing until someone calls one of its functions. So there will always be a `msg.sender`.

# 13. require()
* `require()` is a built-in function in Solidity that checks if a condition is true. If the condition is false, it will throw an error and stop the execution of the function.

# 14. Inheritance
```solidity
contract Doge {
  function catchphrase() public returns (string memory) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string memory) {
    return "Such Moon BabyDoge";
  }
}
```
* `BabyDoge` inherits from `Doge`. That means if you compile and deploy `BabyDoge`, it will have access to both `catchphrase()` and `anotherCatchphrase()` (and any other `public` functions we may define on `Doge`).
* This can be used for logical inheritance (such as with a subclass, a Cat is an Animal). But it can also be used simply for organizing your code by grouping similar logic together into different contracts.

# 15. Imports
When you have multiple files and you want to import one file into another, Solidity uses the `import` keyword:
```solidity
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

# 16. Storage vs Memory (Data location)
* Variables can be stored either in `storage` or `memory`.
* `Storage` refers to variables stored permanently on the blockchain. `Memory` variables are temporary, and are erased between external function calls to your contract.
* Most of the time you don't need to use these keywords because Solidity handles them by default:
  * **`State variables`** (variables declared outside of functions) are by default `storage` and written permanently to the blockchain.
  * Variables declared inside functions are memory and will disappear when the function call ends.
  
  However, there are times when you do need to use these keywords, namely when dealing with structs and arrays within functions:
  ```solidity
  contract SandwichFactory {
    struct Sandwich {
      string name;
      string status;
    }

    Sandwich[] sandwiches;

    function eatSandwich(uint _index) public {
      // Sandwich mySandwich = sandwiches[_index];

      // ^ Seems pretty straightforward, but solidity will give you a warning
      // telling you that you should explicitly declare `storage` or `memory` here.

      // So instead, you should declare with the `storage` keyword, like:
      Sandwich storage mySandwich = sandwiches[_index];
      // ...in which case `mySandwich` is a pointer to `sandwiches[_index]`
      // in storage, and...
      mySandwich.status = "Eaten!";
      // ...this will permanently change `sandwiches[_index]` on the blockchain.

      // If you just want a copy, you can use `memory`:
      Sandwich memory anotherSandwich = sandwiches[_index + 1];
      // ...in which case `anotherSandwich` will simply be a copy of the
      // data in memory, and...
      anotherSandwich.status = "Eaten!";
      // ...will just modify the temporary variable and have no effect
      // on `sandwiches[_index + 1]`. But you can do this:
      sandwiches[_index + 1] = anotherSandwich;
      // ...if you want to copy the changes back into blockchain storage.
    }
  }
  ```
