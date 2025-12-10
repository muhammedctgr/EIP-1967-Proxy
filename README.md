## EIP-1967

**Ethereum Improvement Proposal (now ERC)-1967.**

The need to regularly utilize storage to reference things in implementation (specifically the implementation address) led to the desire for EIP-1967: Standard Proxy Storage Slots. This proposal would allocate standardized slots in storage specifically for use by proxies.

This SmallProxy example contains a lot of Yul. Yul is a sort of in-line Assembly that allows you to write really low-level code. Like anything low-level it comes with increased risk and severity of mistakes, it's good to avoid using Yul as often as you can justify.

Now, within SmallProxy we're importing Proxy.sol from our good friends OpenZeppelin. Looking at the code, we can get a better idea of how things are actually being handled.

There are really only 2 functions in this contract (ignoring the virtual _implementation function). We have _delegate, fallback/_fallback. The fallback functions simply route unrecognized call data to the _delegate function which then routes the call to an implementation contract.

In SmallProxy.sol, we only have 2 functions, setImplementation and _implementation, the aforementioned virtual function.

This virtual function is returning the implementation address and showcases the need for our next topic...

Upgrading SmallProxy.sol
Now, let's demonstrate how upgrading this protocol would work. Add another contract to SmallProxy.sol:

```
contract ImplementationB {
    uint256 public value;
​
    function setValue(uint256 newValue) public {
        value = newValue + 2;
    }
}
```

Selector Clashes
One quick final note on function selector clashes, in our example here, SmallProxy.sol only really has one function setImplementation, but if the implementation contract also had a function called setImplementation, it would be easy to see how this conflict could occur. Were this the case, it would be impossible to call the setImplementation function contained by the Implementation contract, because it would always be processed by the proxy.

Wrap Up
Hopefully this minimalistic example has shed some light on the power of upgradeable proxies. This kind of power should also give you pause and make you consider the effects of trusting this degree of centrality to the protocol developers.