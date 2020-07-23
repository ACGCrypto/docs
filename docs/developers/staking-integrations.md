Masternodes and stakers is stored and governared in [Tao Validator smart contract](https://scan.tao.network/address/0x0000000000000000000000000000000000000088):

- Smart Contract Code: [Tao Validator](https://github.com/Tao-Network/shifu/blob/master/contracts/TaoValidator.sol)
- Smart Contract ABI: [TaoValidatorAbi.json](https://raw.githubusercontent.com/taoblockchain/shifu/master/abis/TaoValidatorAbi.json)

Tao Validator Smart Contract Interface:
```javascript
// apply a new masternode candidate
function propose(address _candidate) external payable;

// Deposit to stake/vote for a candidate
function vote(address _candidate) external payable;

// Unstake/unvote for a candidate
function unvote(address _candidate, uint256 _cap) public;

// Resign a candidate
function resign(address _candidate) public;

// Withdraw after unvote, resign
function withdraw(uint256 _blockNumber, uint _index) public;

function getCandidates() public view returns(address[]);

function getCandidateCap(address _candidate) public view returns(uint256);

function getCandidateOwner(address _candidate) public view returns(address);

function getVoterCap(address _candidate, address _voter) public view returns(uint256);

function getVoters(address _candidate) public view returns(address[]);

function isCandidate(address _candidate) public view returns(bool);

function getWithdrawBlockNumbers() public view returns(uint256[]);

function getWithdrawCap(uint256 _blockNumber) public view returns(uint256);

```

Tao provides RPC APIs. So you can use Web3 library to directly call the functions in the smart contract.

You can follow the steps below to interact with the smart contract by using Web3 library and NodeJS.

## Init Web3 provider
At the first step, you need init Web3 provider by connecting Tao Fullnode RPC endpoint.

```javascript
const Web3 = require('web3')
const web3 = new Web3('https://rpc.tao.network')
const chainId = 88
```

For testnet/mainnet details, you can get network information [here](https://docs.tao.network/general/networks/)
## Unlock wallet
You need to unlock the wallet before staking for the nodes
#### Example
```javascript
// Unlock wallet by private key
const account = web3.eth.accounts.privateKeyToAccount(pkey)
const owner = account.address
web3.eth.accounts.wallet.add(account)
web3.eth.defaultAccount = owner
```

## Init Web3 Tao Validator Contract

```javascript
const validatorAbi = require('./TaoValidatorAbi.json')
const address = '0x0000000000000000000000000000000000000088'
const validator = new web3.eth.Contract(validatorAbi,
        address, {gasPrice: 250000000, gas: 2000000 })
```

Note: you can get TaoValidatorAbi.json [here](https://raw.githubusercontent.com/taoblockchain/shifu/master/abis/TaoValidatorAbi.json)

## Propose/Apply a candidate
Masternode owner need to have at least 100000 TAO to apply a fullnode to become a Masternode candidate. So make sure you have > 100000 TAO in your masternode owner wallet to deposit to the smart contract and pay transaction fee.

You can apply a masternode candidate by call `propose` function from the smart contract

#### Example
```javascript
// Masternode coinbase address
const coinbase = "0xf8ac9d5022853c5847ef75aea0104eed09e5f402"

validator.methods.propose(coinbase).send({
    from : owner,
    value: '100000000000000000000000', // 100000 TAO
    gas: 2000000,
    gasPrice: 250000000,
    chainId: chainId
})
.then((result) => {
    console.log(result)
}).catch(e => console.log(e))
```

You can refer to [Staking Tao script](https://gist.github.com/thanhson1085/7a6471ea0d6c0d6321a0454789d6266c)
## Stake/Vote a candidate
You can stake at least 100 TAO for a node by calling `vote` function from the smart contract.

#### Example
Stake 500 TAO to a node.
```javascript
validator.methods.vote(coinbase).send({
    from: owner,
    value: '500000000000000000000', // 500 TAO
    gas: 2000000,
    gasPrice: 250000000,
    chainId: chainId
})
.then((result) => {
    console.log(result)
}).catch(e => console.log(e))
```

## Unstake/Unvote a candidate
You can unstake by calling `unvote` function from the smart contract

```javascript
const cap = '500000000000000000000' // unvote 500 TAO

validator.methods.unvote(coinbase, cap).send({
    from : owner,
    gas: 2000000,
    gasPrice: 250000000,
    chainId: chainId
})
.then((result) => {
    console.log(result)
}).catch(e => console.log(e))
```

## Resign a candidate

```javascript
validator.methods.resign(coinbase).send({
    from : owner,
    gas: 2000000,
    gasPrice: 250000000,
    chainId: chainId
})
.then((result) => {
    console.log(result)
}).catch(e => console.log(e))
```

## Withdraw TAO
You need to wait for 48 epochs (if unvote), 30 days (if resign) to unlock your TAO staked

#### Example
```javascript
// get highest block number
web3.eth.getBlockNumber().then(blockNumber => {
    return validator.methods.getWithdrawBlockNumbers().call({
        from: owner
    }).then((result) => {
        let map = result.map(it, idx => {
            it = it.toString()
            if (parseInt(it) < blockNumber && it != "0") {
                return validator.methods.withdraw(it, idx).send({
                    from : owner,
                    gas: 2000000,
                    gasPrice: 250000000,
                    chainId: chainId
                })
            }
        })
        return Promise.all(map)
    })
}).then((result) => {
    console.log(result)
}).catch(e => console.log(e))
```

## Get list candidates
You can get list candidates from [RPC endpoint](https://apidocs.tao.network/?shell#eth_getcandidates):
```
curl https://rpc.tao.network \
    -X POST \
    -H "Content-Type: application/json" \
    -d '{"jsonrpc":"2.0","method":"eth_getCandidates","params": ["latest"],"id":1}'

```
Or [get list candidates from Shifu](https://apidocs.tao.network/?shell#shifu-apis-candidates):
```
curl -X GET https://shifu.tao.network/api/candidates \
  -H 'Accept: application/json'
```

