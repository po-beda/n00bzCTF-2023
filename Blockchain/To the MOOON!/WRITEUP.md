# To the MOOON!

After analyzing the source code, we come to the conclusion that in order to get the flag, we need to make `price > priceToBeat`:

```c
function solve() public {
    require(price > priceToBeat, "You have... NOT gone to the moon");
    challIsSolved = true;
}
```

The `price` variable changed by `setPrice` and it directly depends on the `supply` value. If we increase its value, then we will solve the task. This is possible using this algorithm:

1. Make "buy" transact with 1 amount and 110 value. Supply value will be overflown due to big decrease (> 10)

```c
uint _tokensBought = msg.value/price;
balanceOf[msg.sender] += _tokensBought;
supply -= _tokensBought;
```

2. Make "setPrice" transact to update `price` value
3. Make "solve" transact and get hash

Now, the flag can be obtained by the algorithm in the task description
