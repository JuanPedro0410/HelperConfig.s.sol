# HelperConfig.s.sol

```
import {MockV3Aggregator} from "../test/mocks/MockV3Aggregator.sol";
import {Script} from "forge-std/Script.sol";
```
Here I import the tools for the contract. The MockV3Aggregator is a contract that simulate the pricefeed on a local blockchain.
The script has some tools to make script contracts.

```
contract HelperConfig is Script {
}
```

Create the contract that inherits from Script.sol contract

```
	NetworkConfig public activeNetworkConfig;

    uint8 public constant DECIMALS = 8;
    int256 public constant INITIAL_PRICE = 2000e8;
```

Create the variables for ther contract. the first one is a variable of type networkConfig that will we used to store the network that is used.
The 2 last variables store 2 parameters for a function.


```
    struct NetworkConfig {
        address priceFeed; // ETH/USD pricefeed address
    }
```

create the type NetworkConfig that when you create a variable or store something in, you must to pass an address called priceFeed. That this will we what stores the function.


```
    constructor() {
        if (block.chainid == 11155111) {
            activeNetworkConfig = getSepoliaEthConfig();
        } else {
            activeNetworkConfig = getOrCreateAnvilEthConfig();
        }
    }
```

in the constructor you check if the chainId of the network is the chainId of sepolia, if it is correct makes activeNetworkConfig equals to getSepoliaEthConfig() that returns a network config
else it execute getOrCreateAnvilEthConfig() that deploy or if it has been deployed run the code, of a contract that simulate a pricefeed on a local enviroment and returns the network local config

```
    function getSepoliaEthConfig() public pure returns (NetworkConfig memory) {
        NetworkConfig memory sepoliaConfig = NetworkConfig({
            priceFeed: 0x694AA1769357215DE4FAC081bf1f309aDC325306
        });
        return sepoliaConfig;
    }
```

Create the function getSepoliaEthConfig() that is public pure that is like view but do maths and returns something of type NetworkConfig.
Create a variable type Networkconfig that store a NetworkConfig Struct that has the priceFeed of ETH/USD of SEPOLIA NETWORK.
return the struct sepoliaConfig.


```
	function getOrCreateAnvilEthConfig() public returns (NetworkConfig memory) {
        if (activeNetworkConfig.priceFeed != address(0)) {
            return activeNetworkConfig;
        }
        // 1. Deploy the mocks
        // 2. Return the mock address

        vm.startBroadcast();
        MockV3Aggregator mockPricefeed = new MockV3Aggregator(
            DECIMALS,
            INITIAL_PRICE
        );
        vm.stopBroadcast();

        NetworkConfig memory anvilConfig = NetworkConfig({
            priceFeed: address(mockPricefeed)
        });
        return anvilConfig;
    }
```

Create a function very similar to getSepoliaEthConfig() but this one is not pure because execuate a transaction to deploy MockV3Aggregator.
use an if to verificate if the variable activeNetworkConfig.priceFeed is not 0, that if it is not 0 the contract has been previusly deployed so you dont have to deploy it again.
use vm.startbroadcast/stopbroadcast to execute a transaction in.
deploy the MockV3Aggregator and pass like parameters the constants DECIMALS AND INITIAL_PRICE.
Create a new variable called anvil config that stores a Struct that has the pricefeed in. The pricefeed is the address(mockpricefeed) because you deployed the contract locali and has a variable that stores it (mockpricefeed). 
