# MultiCall Examples

We make multiple calls via a single API call to our infura node. 

Multicall
One of the most popular contracts to use multicall with is the Uniswap multicall contract.

This contract has functions such as tryAggregate that allow us to pass in a list of encoded functions and function inputs to call any other function on the blockchain.

```
function tryAggregate(bool requireSuccess, Call[] memory calls) public returns (Result[] memory returnData) {
        returnData = new Result[](calls.length);
        for(uint256 i = 0; i < calls.length; i++) {
            (bool success, bytes memory ret) = calls[i].target.call(calls[i].callData);
if (requireSuccess) {
                require(success, "Multicall2 aggregate: call failed");
            }
returnData[i] = Result(success, ret);
        }
    }
 ```
It takes as input an array of ``Call`` , which have an encoded address, function selector, and input parameters. The ``tryAggregate`` then simply iterates through this list making the calls to each contract.

We’ve now essentially wrapped a list of function calls into 1 function call, cutting down on our API calls

#Multicall in Brownie and JavaScript
``Brownie`` (a popular Pythonic smart contract development framework) has this multicall functionality built-in that we can use. JavaScript has some nice packages that work as well. We have a brownie example and JavaScript example for you to explore and try out yourself!

For example, if we wanted to get the last 50 rounds of a Chainlink data feed, without multicall we might have to do something like this:

```rounds = []
for round_id in range(latest_round, latest_round - 50, -1):
  round_data = price_feed.getRoundData(round_id)
  rounds.append(round_data)
  ```
  
With each time we call of ``price_feed.getRoundData`` being an API call.

To do this with ``brownie``, we just need to add 2 lines!

```
brownie.multicall(address="0x5BA1e12693Dc8F9c48aAD8770482f4739bEeD696")
with brownie.multicall:
  for round_id in range(latest_round, latest_round - 50, -1):
    round_data = price_feed.getRoundData(round_id)
    rounds.append(round_data)
   
 ```
We just need to pick the multicall contract address (in this case, we use the Uniswap V3 edition), and then add all the API calls that we want to make in our with ``brownie.multicall`` .

We can also plot the last 50 rounds of a Chainlink data feed with 1 API call to the blockchain!

![image](https://user-images.githubusercontent.com/90616509/216808514-993dd9cb-65b3-4ee4-8f39-ba103a167641.png)

                     ETH / USD Plot using matplotlib


The full source code for creating multicall requests is available in this GitHub Repository.

## Prerequisites

1. Please go through the [brownie install documenation](https://eth-brownie.readthedocs.io/en/stable/install.html)
2. Please have an environment varibale called `WEB3_INFURA_PROJECT_ID` set to your [infura project id](https://infura.io/). You can add it to a `.env` file and add `dotenv: .env` to the `brownie-config.yaml` file. 

## Quickstart

```
git clone https://github.com/Litvamhan/Multicall-assignment.git
cd multicall
```

# Simple Example

```bash
brownie run scripts/multicall.py
```

# Plotting Example

If you installed brownie with pipx, inject `matplotlib` into the environment. Otherwise, just run `pip install matplotlib`.

```
pipx inject eth-brownie matplotlib
```

*restart terminal*

```bash
brownie run scripts/multicall_plot.py
```
