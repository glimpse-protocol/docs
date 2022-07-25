# Zerojs API reference

## `zjs.setPubId(id: string)`

Setting the publisher id on the page. This step is required and should be executed before other api calls.

**Example**

```js
zjs.cmd.push(() => {
  zjs.setPubId("a9j3")
})
```

**Parameters**

| Parameter    | Description                                        |
| ------------ | -------------------------------------------------- |
| `id: string` | The unique publisher id provided by our web portal |


## `zjs.go(adUnits: AdUnit[])`

Initiate the inventory decarb process. Return a list of unfilled ad units and decarbed ad units.

**Example**

```js
const adUnits = [
  {
    code: "/1234567/environment-728x90"
  }, 
  // ...
]

zjs.cmd.push(() => {
  const [unfilled, deCarbed] = zjs.go(adUnits)

  // proceed to open bidding with the unfilled units
  // zerojs will handle the delivering of the deCarbed units
})
```

**Parameters**

| Parameter           | Description                                        |
| ------------------- | -------------------------------------------------- |
| `adUnits: AdUnit[]` | A list of ad units for conducting carbon reduction |

| Return                                   | Description                                           |
| ---------------------------------------- | ----------------------------------------------------- |
| `unfilled: AdUnit[], deCarbed: AdUnit[]` | a list of unfilled and deCarbed ad units respectively |



## `zjs.onEvent(event: EventType, listener: Listener)`

Register an event listener for a specific event.

**Example**

```js
zjs.cmd.push(() => {
  zjs.onEvent("adRequested", (adUnit) => {
    console.log(`deCarbed ad unit requested: `, adUnit)
  })
})
```

**Parameters**

| Parameter            | Description                                          |
| -------------------- | ---------------------------------------------------- |
| `event: EventType`   | The type of the event to listen to                   |
| `listener: Listener` | A listener function to callback when an event occurs |

**List of EventTypes**

```
configUpdated
auctionRun
adUnitsTargeted
adRequested
adServed
adViewed
```