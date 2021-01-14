# Web3j Beacon Node API Client

The [Beacon Node API client](https://github.com/web3j/web3j-eth2) is a thin library that will allow you to interact fluently with the new Eth2 nodes using the methods available in the [Eth2.0-API specification](https://ethereum.github.io/eth2.0-APIs/), including event subscriptions. As a Web3j library we've kept everything as simple as possible for users to get them on their way quickly.

In this chapter we'll guide you through the few steps required to get your application interacting with any Eth2 Beacon Chain Node.

## Getting started

First, you’ll need to add the library to your project, either using Maven:

```xml
<dependency>
    <groupId>org.web3j.eth2</groupId>
    <artifactId>beacon-node-api</artifactId>
    <version>1.0.0</version>
</dependency>
```

or Gradle:

```groovy
implementation 'org.web3j.eth2:beacon-node-api:1.0.0'
```

At the moment of writing this, the first released version 1.0.0 corresponds to the Eth2 specification v0.12.2. Check the project [releases](https://github.com/web3j/web3j-eth2/releases) page for the last available version.

Once you have added the library to your project, you can create a client instance pointing to a particular Beacon Chain node in a familiar Web3j-esque way:

```java
var service = new BeaconNodeService("http://...");
var client = BeaconNodeClientFactory.build(service);
```

That’s it! At this point, you can start calling the node endpoints, for instance to retrieve the head block:

```java
client.getBeacon().getBlocks().findById(NamedBlockId.HEAD);
```

Or to retrieve all current attester slashings:

```java
client.getBeacon().getPool()
    .getAttesterSlashings()
    .findAll();
```

If you don’t know a Beacon Chain node where you can point your client code, in the next section we'll guide you through a few simple steps to get a test network up and running.

## Start a test network

In this section we’ll be using the Teku Beacon Chain implementation to start a local test network where to point your client code (before this, make sure you are running a [Docker](https://www.docker.com/products/docker-desktop) environment).

First, clone the Teku project in a local folder:

```shell
$ git clone https://github.com/ConsenSys/teku.git
```

Then start the Beacon Chain local network (check the [Teku docs](https://github.com/ConsenSys/teku/tree/master/test-network) for more information on the Docker Testnet):

```shell
$ teku/test-network/launch.sh
Recreating test-network_teku1_1       ... done
Starting test-network_prometheus_1    ... done
Starting test-network_node-exporter_1 ... done
Recreating test-network_teku4_1       ... done
Recreating test-network_teku2_1       ... done
Recreating test-network_teku3_1       ... done
Starting test-network_grafana_1       ... done
```

You’ll get a bunch of logs, but once your network has completely started you’ll be able to point to any of the four nodes at ports `19601`, `19602`, `19603` and `19604`.

## Listen for events

Let’s create a client and listen to some events happening in the test network:

```java
var service = new BeaconNodeService("http://localhost:19601/");
var client = BeaconNodeClientFactory.build(service);

// We want to receive at least one event
var latch = new CountDownLatch(1);

// At the moment we are interested in any topic
var topics = EnumSet.allOf(BeaconEventType.class);

// Then subscribe to each event
client.getEvents().onEvent(topics, event -> {
    System.out.println("Received event: " + event);
    latch.countDown();
});

// Wait for the event
latch.await();
```

When running this code, you should be able to see some logs showing the interaction between your client and the local node:

```
00:15:47.744 [jersey-client-async-executor-0] DEBUG org.web3j.eth2.api.BeaconNodeService - 2 * Sending client request on thread jersey-client-async-executor-0
2 > GET http://localhost:19601/eth/v1/events?topics=head%2Cblock%2Cattestation%2Cvoluntary_exit%2Cfinalized_checkpoint%2Cchain_reorg
2 > Accept: text/event-stream

00:15:51.420 [jersey-client-async-executor-0] DEBUG org.web3j.eth2.api.BeaconNodeService - 2 * Client response received on thread jersey-client-async-executor-0
2 < 200
2 < Cache-Control: no-cache
2 < Connection: close
2 < Content-Type: text/event-stream;charset=utf-8
2 < Date: Thu, 10 Dec 2020 23:15:47 GMT
2 < Server: Javalin
event: head
data: {"slot":"33207","block":"0x1829e81553bb76ed92a7ae2d671e018b219b7eeee0cea80fac12b2a7d6924826","state":"0xe7339bff5fbb2a30f039a900532cb936e6afbd40ef1fd41cb645687659d8833a","epoch_transition":false,"previous_duty_dependent_root":"0xd34981d5ab59d4d091992099cedea95236dfcc2252f55f53e282ee847fb426e8","current_duty_dependent_root":"0x626db3e484c19fa8f0c57ca6c390bfa8a61313564d61f4c68764239a4e0c966e"}
```

As you can see, when running this example the first event we received was a head event. This happens after a validator committee attests for a head block at every slot (typically every few seconds).

You can easily modify the above example to work with other topics such as epoch finality checkpoints, validator attestations or new blocks by specifying different sets of [Beacon event types](https://github.com/web3j/web3j-eth2/blob/master/beacon-node-api/src/main/kotlin/org/web3j/eth2/api/schema/BeaconEventType.kt).
