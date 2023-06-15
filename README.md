# Uniswap V3 Core Java contract wrapper

This repository contains Web3j generated smart contract wrapper code to interact with the core smart contracts for the Uniswap V3 Protocol.
For the core smart contracts sources, see the [uniswap-v3-core](https://github.com/Uniswap/v3-core) repository.

## Maven
Add the repository to the repositories element of your project pom.xml file.
```xml
<repositories>
    <repository>
        <id>v3-periphery-repo</id>
        <url>https://maven.pkg.github.com/jclagache/v3-core/</url>
    </repository>
</repositories>
```

Add the package dependencies to the dependencies element of your project pom.xml file.
```xml
<dependencies>
 <dependency>
    <groupId>me.jclagache.amm.uniswap.v3</groupId>
    <artifactId>v3-core</artifactId>
    <version>1.0.1-SNAPSHOT</version>
  </dependency>
</dependencies>
```

## Gradle
Add the repository to your build.gradle file (Gradle Groovy) or build.gradle.kts file (Kotlin DSL) file.

Groovy:
```groovy
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/jclagache/v3-core")
    }
}
```
Kotlin:
```kotlin
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/jclagache/v3-core")
    }
}
```

Add the package dependencies to your build.gradle file (Gradle Groovy) or build.gradle.kts file (Kotlin DSL) file.

Groovy:
```groovy
dependencies {
    implementation 'me.jclagache.amm.uniswap.v3:v3-core:1.0.1-SNAPSHOT'
}
```
Kotlin:
```kotlin
dependencies {
    implementation("me.jclagache.amm.uniswap.v3:v3-core:1.0.1-SNAPSHOT")
}
```

## Usage
Refer to the [Uniswap V3 Smart Contracts](https://docs.uniswap.org/contracts/v3/overview) and [Web3j](https://docs.web3j.io) documentation.

```java
Web3j web3j = Web3j.build(new HttpService("<your_node_url>"));
TransactionManager transactionManager = new ReadonlyTransactionManager(web3j,"my_address");
//USDC/WETH 0.05 % pool
IUniswapV3PoolState contract = IUniswapV3PoolState.load("0x88e6A0c2dDD26FEEb64F039a2c41296FcB3f5640", web3j, transactionManager, new DefaultGasProvider());
//Filter for swap events
EthFilter filter = new EthFilter(DefaultBlockParameterName.LATEST, DefaultBlockParameterName.LATEST, "0x88e6A0c2dDD26FEEb64F039a2c41296FcB3f5640");
filter.addSingleTopic(EventEncoder.encode(IUniswapV3PoolEvents.SWAP_EVENT));
web3j.ethLogFlowable(filter).subscribe(log -> {
    List<Type> results = FunctionReturnDecoder.decode(log.getData(), IUniswapV3PoolEvents.SWAP_EVENT.getNonIndexedParameters());
    BigInteger token0Balance = ((Int256) results.get(0)).getValue();
    BigInteger token1Balance = ((Int256) results.get(1)).getValue();
    BigInteger sqrtPriceX96 = ((Uint160) results.get(2)).getValue();
    BigInteger liquidity = ((Uint128) results.get(3)).getValue();
    BigInteger tick = ((Int24) results.get(4)).getValue();
});
```