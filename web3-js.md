## 1. Web3 Providers: Infura
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

## 3. 
