Once your full node is up and running, you need to apply 
it to make it eligible as a masternode.
[This page](/get-started/run-node/) shows a tutorial for beginners to run a full node.

## Getting sufficient TAO
As 50'000 TAO are required to apply: 

* If you plan to run your node on testnet, you need to 
fill out the following [form](https://docs.google.com/forms/d/183UxYRET9I183L7lFHCredjaTd9oj4kmf4UdH7eLNNs):
to get 50k TAO for testnet.

<iframe src="https://docs.google.com/forms/d/e/1FAIpQLSf0BiG8Rs5v4ItkwykgWUXsavLRZNA9W_EHTDis7klk5mNJnw/viewform?embedded=true" width="640" height="900" frameborder="0" marginheight="0" marginwidth="0">Loading...</iframe>

Our team will then review your request and send you the required funds.

*Note: Those testnet TAO are only usable in testnet, they have absolutely no trading value*

* If you plan to run your node on mainnet, you need to acquire at least 50k TAO on 
exchanges that list TAO. 
Please refer to [this page](https://tao.network/about-us/) for which exchanges listing TAO.

## Applying to become a masternode
You can now apply by going to the Shifu page for 
[testnet](https://master.testnet.tao.network) or [mainnet](https://shifu.tao.network).
Login with the wallet that contains your newly received 50'000 TAO.

On the top right corner, click on "Become a Candidate".

Enter the amount of TAO you want to deposit (minimum 50'000).

Enter your coinbase address. This is the public key of the account that your masternode is using.
If your are running your node with `taomn`, you can simply run `taomn inspect` to get it.

Confirm with apply and proceed to make the payement.

Your full node will now be listed on Shifu.
People can view its details and vote for it.

If your node is in the top 150 most voted nodes, it will be promoted 
as a masternode and will start producing blocks at the next epoch.

## Resigning your masternode
In case you want to stop your node, you need to resign it from Shifu first 
in order to retrieve your locked funds.
Access Shifu for [testnet](https://master.testnet.tao.network) or 
[mainnet](https://shifu.tao.network), go to your candidate detail page, 
and click the `Resign` button.
Your funds will be available to withdraw 1440 epochs (around 30 days) after the resignation.

After resigning successfully, you can stop your node. If you ran it with `taomn`, simply run:
```
taomn remove
```

At this point, your masternode is completly terminated.
