## 1. Web3 Providers: `Infura`
* Web3 providers are used to connect to the Ethereum network from your application. They provide a way to interact with the Ethereum blockchain and send transactions. You could host your own Ethereum node as a provider. However, there's a third-party service that makes your life easier so you don't need to maintain your own Ethereum node in order to provide a DApp for your users — `Infura`.
* `Infura` is a service that maintains a set of Ethereum nodes with a caching layer for fast reads, which you can access for free through their API. You can set up Web3 to use `Infura` as your web3 provider as follows:

```js
var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
```

* However, since our DApp is going to be used by many users — and these users are going to WRITE to the blockchain and not just read from it — we'll need a way for these users to sign transactions with their private key (`MetaMask`).

## 2. MetaMask
* Metamask is a browser extension for Chrome and Firefox that lets users securely manage their Ethereum accounts and private keys, and use these accounts to interact with websites that are using `Web3.js`.
* Metamask uses `Infura's` servers under the hood as a `web3 provider`, just like we did above — but it also gives the user the option to choose their own `web3` provider. So by using Metamask's `web3` provider, you're giving the user a choice, and it's one less thing you have to worry about in your app.
* **Using Metamask's web3 provider**:
  * Metamask injects their `web3` provider into the browser in the global JavaScript object `web3`. So your app can check to see if `web3` exists, and if it does use `web3.currentProvider` as its provider.
  * Here's some template code provided by Metamask for how we can detect to see if the user has Metamask installed, and if not tell them they'll need to install it to use our app:
  
  ```js
  window.addEventListener('load', function() {

    // Checking if Web3 has been injected by the browser (Mist/MetaMask)
    if (typeof web3 !== 'undefined') {
      // Use Mist/MetaMask's provider
      web3js = new Web3(web3.currentProvider);
    } else {
      // Handle the case where the user doesn't have web3. Probably
      // show them a message telling them to install Metamask in
      // order to use our app.
    }

    // Now you can start your app & access web3js freely:
    startApp()

  })
  ```

  * You can use this boilerplate code in all the apps you create in order to require users to have Metamask to use your DApp.
  * There are other private key management programs your users might be using besides MetaMask, such as the web browser `Mist`. However, they all implement a common pattern of injecting the variable web3, so the method we describe here for detecting the user's web3 provider will work for these as well.
* **Getting the user's account in MetaMask**:
  * MetaMask allows the user to manage multiple accounts in their extension. We can see which account is currently active on the injected web3 variable via:

    ```js
    var userAccount = web3.eth.accounts[0];
    ```

  * Because the user can switch the active account at any time in MetaMask, our app needs to monitor this variable to see if it has changed and update the UI accordingly. We can do that with a setInterval loop as follows:

    ```js
    var accountInterval = setInterval(function() {
    // Check if account has changed
    if (web3.eth.accounts[0] !== userAccount) {
        userAccount = web3.eth.accounts[0];
        // Call some function to update the UI with the new account
        updateInterface();
    }
    }, 100);
    ```

## 3. Talking to Contracts
Web3.js will need 2 things to talk to your contract: its `address` and its `ABI`.

### 3.1 Contract Address (`address`)
After you finish writing your smart contract, you will compile it and deploy it to Ethereum. After you deploy your contract, it gets a fixed address on Ethereum where it will live forever. You'll need to copy this address after deploying in order to talk to your smart contract.

### 3.2 `ABI`
* `ABI` stands for **Application Binary Interface**. Basically it's a representation of your contracts' methods in JSON format that tells Web3.js how to format function calls in a way your contract will understand.
* When you compile your contract to deploy to Ethereum, the Solidity compiler will give you the `ABI`, so you'll need to copy and save this in addition to the contract address.
* Once you have your contract's address and ABI, you can instantiate it in Web3 as follows:

```js
// Instantiate myContract
var myContract = new web3js.eth.Contract(myABI, myContractAddress);
```

## 4. Calling Contract Functions

`Web3.js` has two methods we will use to call functions on our contract: `call` and `send`.

### 4.1 `call`
The `call` method is used to read data from a contract without sending a transaction. It's useful for reading data from a contract without changing the state of the blockchain. `call` is used for `view` and `pure` functions. It only runs on the local node, and won't create a transaction on the blockchain. Using `Web3.js`, you would call a function named `myMethod` with the parameter `123` as follows:

```js
myContract.methods.myMethod(123).call();
```

### 4.2 `send`
* `send` will create a transaction and change data on the blockchain. You'll need to use `send` for any functions that aren't `view` or `pure`.
* **Note**: sending a transaction will require the user to pay gas, and will pop up their Metamask to prompt them to sign a transaction. When we use Metamask as our web3 provider, this all happens automatically when we call `send()`, and we don't need to do anything special in our code.
* Using Web3.js, you would send a transaction calling a function named myMethod with the parameter 123 as follows:

```js
myContract.methods.myMethod(123).send();
```

* Our array of zombies is `public`. In Solidity, when you declare a variable `public`, it automatically creates a public `getter` function with the same name. Here's how we would write a JavaScript function in our front-end that would take a zombie id, query our contract for that zombie, and return the result:

```js
function getZombieDetails(id) {
  return cryptoZombies.methods.zombies(id).call()
}

// Call the function and do something with the result:
getZombieDetails(15)
.then(function(result) {
  console.log("Zombie 15: " + JSON.stringify(result));
});
```

#### 4.2.1 Differences b/w `send` and `call`:

* `send`ing a transaction requires a `from` address of who's calling the function (which becomes `msg.sender` in your Solidity code). We'll want this to be the user of our DApp, so MetaMask will pop up to prompt them to sign the transaction.
* `send`ing a transaction costs gas.
* There will be a significant delay from when the user sends a transaction and when that transaction actually takes effect on the blockchain. Thus we'll need logic in our app to handle the asynchronous nature of this code.
* Here's an example of how we could call  function in Web3.js using MetaMask:

```js
function createRandomZombie(name) {
  // This is going to take a while, so update the UI to let the user know
  // the transaction has been sent
  $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
  // Send the tx to our contract:
  return cryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
      $("#txStatus").text("Successfully created " + name + "!");
      // Transaction was accepted into the blockchain, let's redraw the UI
      getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
      // Do something to alert the user their transaction has failed
      $("#txStatus").text(error);
  });
}
```

* `receipt` will fire when the transaction is included into a block on Ethereum, which means our zombie has been created and saved on our contract.
* `error` will fire if there's an issue that prevented the transaction from being included in a block, such as the user not sending enough gas. We'll want to inform the user in our UI that the transaction didn't go through so they can try again.
* You can optionally specify `gas` and `gasPrice` when you call `send`, e.g. `.send({ from: userAccount, gas: 3000000 })`. If you don't specify this, MetaMask will let the user choose these values.

#### 4.2.2 `send`ing `payable` functions
In our DApp, we set `levelUpFee = 0.001 ether`, so when we call our `levelUp function`, we can make the user send 0.001 Ether along with it using the following code:

```js
cryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })
```

## 5. Subscribing to Events
* In Web3.js, you can subscribe to an event so your web3 provider triggers some logic in your code every time it fires:

```js
cryptoZombies.events.NewZombie()
.on("data", function(event) {
  let zombie = event.returnValues;
  // We can access this event's 3 return values on the `event.returnValues` object:
  console.log("A new zombie was born!", zombie.zombieId, zombie.name, zombie.dna);
}).on("error", console.error);
```

* **Note** that this would trigger an alert every time **ANY** zombie was created in our DApp — not just for the current user.
* **Using `indexed`**
  * In order to filter events and only listen for changes related to the current user, our Solidity contract would have to use the `indexed` keyword, like we did in the `Transfer` event of our `ERC721` implementation:

  ```solidity
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  ```

  * In this case, because `_from` and `_to` are `indexed`, that means we can filter for them in our event listener in our front end:

  ```js
  // Use `filter` to only fire this code when `_to` equals `userAccount`
  cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
  .on("data", function(event) {
    let data = event.returnValues;
    // The current user just received a zombie!
    // Do something here to update the UI to show it
  }).on("error", console.error);
  ```

* **Querying past events**
  * We can even query past events using getPastEvents, and use the filters fromBlock and toBlock to give Solidity a time range for the event logs: 

  ```js
  cryptoZombies.getPastEvents("NewZombie", { fromBlock: 0, toBlock: "latest" })
  .then(function(events) {
    // `events` is an array of `event` objects that we can iterate, like we did above
    // This code will get us a list of every zombie that was ever created
  });
  ```

  * Because you can use this method to query the event logs since the beginning of time, this presents an interesting use case: **Using events as a cheaper form of storage.**
  * The tradeoff here is that events are not readable from inside the smart contract itself. But it's an important use-case to keep in mind if you have some data you want to be historically recorded on the blockchain so you can read it from your app's front-end. For example, we could use this as a historical record of zombie battles — we could create an event for every time one zombie attacks another and who won.


