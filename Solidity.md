---
layout: single
title: Solidity with CryptoZombies
---

## 1. Contracts
* Solidity's code is encapsulated in contracts. A `contract` is the fundamental building block of Ethereum applications — all variables and functions belong to a contract.

## 2. Version Pragma
* All solidity source code should start with a `version pragma` — a declaration of the version of the Solidity compiler this code should use. This is to prevent issues with future compiler versions potentially introducing changes that would break your code.

## 3. State Variables
* State variables are permanently stored in contract storage. This means they're written to the Ethereum blockchain.

## 4. `uint`
* In Solidity, uint is actually an alias for `uint256`, a 256-bit unsigned integer. You can declare `uint` with less bits — `uint8`, `uint16`, `uint32`, etc.. But in general you want to simply use `uint`. There's also an `int` data type for signed integers.

## 5. `public`
* `public` is an access modifier. It means that Solidity automatically creates a function that allows you to access the variable from outside of the contract.
```solidity
// example public array
Person[] public people;
```

## 6. Arrays
* There are two types of arrays in Solidity: fixed arrays and dynamic arrays:
    * Fixed arrays: `uint[3]`
    * Dynamic arrays: `uint[]`
* `array.push()` returns a uint of the new length of the array

## 7. Functions
* A function declaration in solidity looks like the following:

```solidity
function eatHamburgers(string memory _name, uint _amount) public {}
```

* Functions may have `Visibility Modifiers`. Note that we're specifying the function visibility as `public`. We're also providing instructions about where the `_name` variable should be stored - in `memory`.
* In addition to **`public`** and **`private`**, `Solidity` has two more types of visibility for functions: **`internal`** and **`external`**.
* `internal` is the same as `private`, except that it's also accessible to contracts that inherit from this contract.
* `external` is similar to `public`, except that these functions can **ONLY** be called outside the contract — they can't be called by other functions inside that contract.
* In Solidity you can return **more than one** value from a function.
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

* **View Functions** (`State Modifier`): If a function is only reading data from the blockchain, you can mark it as `view`. This will save you some gas. `view` functions don't cost any gas when they're called externally by a user. **NOTE** that if a `view` function is called internally from another function in the same contract that is not a view function, it will still cost gas. This is because the other function creates a transaction on Ethereum, and will still need to be verified from every node. So view functions are only free when they're called externally.:

```solidity
function sayHello() public view returns (string memory) {}
```

* **Pure Functions** (`State Modifier`): If a function doesn't read from the blockchain at all (i.e., it only depends on its parameters), you can mark it as `pure`. This will save you even more gas:

```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

## 8. Keccak256 and Typecasting
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

## 9. Events
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

## 10. Accounts
* In ETH blockchain, each account has an address, which you can think of like a bank account number. It's a unique identifier that points to that account.
* An `address` is owned by a specific `user` (or a `smart contract`).
* In crypto-zombies tutorial, when a user creates new zombies by interacting with our app, we set ownership of those zombies to the Ethereum address that called the function.

## 11. Mappings
* A mapping is a data structure that maps keys to values.

```solidity
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

## 12. msg.sender
* **NOTE**: In Solidity, there are certain global variables that are available to all functions. One of these is `msg.sender`, which refers to the `address` of the person (or smart contract) who called the current function. 
* **NOTE**: In Solidity, function execution always needs to start with an external caller. A contract will just sit on the blockchain doing nothing until someone calls one of its functions. So there will always be a `msg.sender`.

## 13. require()
* `require()` is a built-in function in Solidity that checks if a condition is true. If the condition is false, it will throw an error and stop the execution of the function.

## 14. Inheritance

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

// contract can inherit from multiple contracts
contract SatoshiNakamoto is NickSzabo, HalFinney {
  // Omg, the secrets of the universe revealed!
}
```

* `BabyDoge` inherits from `Doge`. That means if you compile and deploy `BabyDoge`, it will have access to both `catchphrase()` and `anotherCatchphrase()` (and any other `public` functions we may define on `Doge`).
* This can be used for logical inheritance (such as with a subclass, a Cat is an Animal). But it can also be used simply for organizing your code by grouping similar logic together into different contracts.

## 15. Imports
When you have multiple files and you want to import one file into another, Solidity uses the `import` keyword:

```solidity
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

## 16. Storage vs Memory (Data location)
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

* **Storage is Expensive**: Writing to `storage` is one of the most expensive operations in Solidity. In order to keep costs down, you want to avoid writing data to storage except when absolutely necessary. Sometimes this involves seemingly inefficient programming logic — like rebuilding an array in `memory` every time a function is called instead of simply saving that array in a variable for quick lookups.

![Diagram Description](images/data-locations-in-solidity.svg)

## 17. Interfaces
* An interface is a type in Solidity which is used to specify the functions an object must implement, without defining the implementation of those functions.
* For our contract to talk to another contract on the blockchain that we don't own, first we need to define an interface. We declare the functions we want to use from the other contract in this interface so that our contract knows what the other contract's functions look like, how to call them, and what sort of response to expect.
* Once we've defined an interface as:

```solidity
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

* We can use it in a contract as follows:

```solidity
contract MyContract {
  address NumberInterfaceAddress = 0xab38...
  // ^ The address of the FavoriteNumber contract on Ethereum
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // Now `numberContract` is pointing to the other contract

  function someFunction() public {
    // Now we can call `getNum` from that contract:
    uint num = numberContract.getNum(msg.sender);
    // ...and do something with `num` here
  }
}
```

## 18. OpenZeppelin
* `OpenZeppelin` is a collection of open-source smart contracts that provide common patterns and best practices for building secure and scalable Ethereum applications.
* `Ownable` is a popular contract from `OpenZeppelin` that provides a simple way to restrict access to a contract's functions to only the contract's owner. `Ownable` contract basically does the following:
  * When a contract is created, its constructor sets the owner to `msg.sender` (the person who deployed it)
  * It adds an `onlyOwner` modifier, which can restrict access to certain functions to only the `owner`.
  * It allows you to transfer the contract to a new `owner`

## 19. Function Modifiers (`modifier`)
* A function modifier looks just like a function, but uses the keyword `modifier` instead of the keyword `function`. And it can't be called directly like a function can — instead we can attach the modifier's name at the end of a function definition to change that function's behavior:

```solidity
contract Ownable {
  ...
  modifier onlyOwner() {
    require(isOwner());
    _;
  }
  ...
  function renounceOwnership() public onlyOwner {
    emit OwnershipTransferred(_owner, address(0));
    _owner = address(0);
  }
}
```

* When you call `renounceOwnership`§, the code inside `onlyOwner` executes first. Then when it hits the `_`; statement in `onlyOwner`, it goes back and executes the code inside `renounceOwnership`.
* Function modifiers can also take **arguments**:

```solidity
// A mapping to store a user's age:
mapping (uint => uint) public age;

// Modifier that requires this user to be older than a certain age:
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}

// Must be older than 16 to drive a car (in the US, at least).
// We can call the `olderThan` modifier with arguments like so:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // Some function logic
}
```

* The `olderThan` modifier takes arguments just like a function does. And that the `driveCar` function passes its arguments to the modifier.

## 20. Gas
* Gas is a measure of how much computational power a transaction uses. It's a way to limit the amount of resources a transaction can use, which helps prevent abuse and ensures that the network remains stable.
* Normally there's no benefit to using sub-types of `uint` (`uint8`, `uint16`, `uint32`, etc.) §because Solidity reserves `256` bits of storage regardless of the `uint` size. For example, using `uint8` instead of `uint` (`uint256`) won't save you any gas. But there's an exception to this: inside `structs`. If you have multiple `uint` inside a `struct`, using a smaller-sized `uint` when possible will allow Solidity to pack these variables together to take up less storage.
* You'll also want to cluster identical data types together (i.e. put them next to each other in the struct) so that Solidity can minimize the required storage space.

## 21. Passing structs as arguments
You can pass a `storage pointer` to a `struct` as an argument to a `private` or `internal` function. This way we can pass a `reference` to our zombie into a function instead of passing in a zombie ID and looking it up:

```solidity
function _doStuff(Zombie storage _zombie) internal {
  // do stuff with _zombie
}
```

## 22. The `payable` Modifier
* The `payable` modifier allows a function to receive Ether. This is useful for functions that need to pay for gas or for functions that need to receive payments from users. e.g:

```solidity
contract OnlineStore {
  function buySomething() external payable {
    // Check to make sure 0.001 ether was sent to the function call:
    require(msg.value == 0.001 ether);
    // If so, some logic to transfer the digital item to the caller of the function:
    transferThing(msg.sender);
  }
}
```

* The ETH will get stored in the contract, which you own and can later withdraw.

## 23. Withdraws
* After you send Ether to a contract, it gets stored in the contract's Ethereum account, and it will be trapped there — unless you add a function to withdraw the Ether from the contract:

```solidity
contract GetPaid is Ownable {
  function withdraw() external onlyOwner {
    address payable _owner = address(uint160(owner()));
    _owner.transfer(address(this).balance);
  }
}
```

* We're using `owner()` and `onlyOwner` from the `Ownable` contract.
* You cannot transfer Ether to an address unless that address is of type `address payable`.
* You can transfer Ether to that address using the `transfer` function., and `address(this).balance` will return the total balance stored on the contract.
* You can use `transfer` to send funds to any Ethereum address. For example, you could have a function that transfers Ether back to the `msg.sender` if they overpaid for an item:

```solidity
uint itemFee = 0.001 ether;
msg.sender.transfer(msg.value - itemFee);
```

## 24. Tokens on Ethereum
* A **token** on Ethereum is basically just a smart contract that follows some common rules — namely it implements a standard set of functions that all other token contracts share, such as `transferFrom(address _from, address _to, uint256 _amount)` and `balanceOf(address _owner)`.
* Internally the smart contract usually has a mapping, `mapping(address => uint256)` balances, that keeps track of how much balance each address has.
* So basically a token is just a contract that keeps track of who owns how much of that token, and some functions so those users can transfer their tokens to other addresses.
* **Why does it matter?**: Since all `ERC20` tokens share the same set of functions with the same names, they can all be interacted with in the same ways. This means if you build an application that is capable of interacting with one `ERC20` token, it's also capable of interacting with any `ERC20` token. That way more tokens can easily be added to your app in the future without needing to be custom coded. You could simply plug in the new token contract address, and boom, your app has another token it can use.
* **Other token standards**: Another token standard that's a much better fit for crypto-collectibles like CryptoZombies — and they're called `ERC721` tokens. `ERC721` tokens are not interchangeable since each one is assumed to be unique, and are not divisible. You can only trade them in whole units, and each one has a unique ID. So these are a perfect fit for making our zombies tradeable. **NOTE**: Using a standard like `ERC721` has the benefit that we don't have to implement the auction or escrow logic within our contract that determines how players can trade / sell our zombies. If we conform to the spec, someone else could build an exchange platform for crypto-tradable `ERC721` assets, and our `ERC721` zombies would be usable on that platform.
* **NOTE**: When implementing a token contract, the first thing we do is copy the interface to its own Solidity file and import it.

## 25. Library in Solidity
* A **library** is a collection of functions that can be used in multiple contracts. Libraries are useful for reusing code across multiple contracts, and they can also be used to create utility functions that are not specific to any particular contract.
* A library is a special type of contract in Solidity. One of the things it is useful for is to attach functions to native data types. The `SafeMath` library from `OpenZeppelin` has 4 functions — `add`, `sub`, `mul`, and `div`. And now we can access these functions from `uint256` as follows:

```solidity
using SafeMath for uint256;

uint256 a = 5;
uint256 b = a.add(3); // 5 + 3 = 8
uint256 c = a.mul(2); // 5 * 2 = 10
```

* For our purposes, libraries allow us to use the `using` keyword, which automatically tacks on all of the library's methods to another data type.
* `assert` is similar to `require`, where it will throw an error if false. The difference between `assert` and `require` is that `require` will refund the user the rest of their gas when a function fails, whereas `assert` will not. So most of the time you want to use `require` in your code; `assert` is typically used when something has gone horribly wrong with the code (like a `uint` overflow).

## 26. Comments
* Single line (`//`) and multi-line comments (`/* */`) are both supported.
* The standard in the Solidity community is to use a format called `natspec`, which looks like this:

```solidity
/// @title A contract for basic math operations
/// @author H4XF13LD MORRIS 💯💯😎💯💯
/// @notice For now, this contract just adds a multiply function
contract Math {
  /// @notice Multiplies 2 numbers together
  /// @param x the first uint.
  /// @param y the second uint.
  /// @return z the product of (x * y)
  /// @dev This function does not currently check for overflows
  function multiply(uint x, uint y) returns (uint z) {
    // This is just a normal comment, and won't get picked up by natspec
    z = x * y;
  }
}
```

* `@notice` explains to a user what the contract / function does. `@dev` is for explaining extra details to developers. `@param` and `@return` are for describing what each parameter and return value of a function are for. All tags are optional. But at the very least, leave a `@dev` note explaining what each function does.
