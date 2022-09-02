# Getting Started

## Prerequisites

1. A [Google Ad Manager](https://admanager.google.com/intl/en_uk/home/) account
1. A working understanding of Google Ad Manager (their [Ad selection whitepaper](https://support.google.com/admanager/answer/1143651?hl=en) is a good place to start)
1. A working understanding of [Google Publisher Tag](https://developers.google.com/publisher-tag/guides/get-started)
1. A working understanding of your site's header bidding configuration (ie [Prebid.js](https://docs.prebid.org/), [APS](https://aps.amazon.com/aps/index.html), etc)
1. A Glimpse Zero account ([contact us if you don't have one](mailto:support@glimpsezero.io))

## Basic setup

There are three steps to get up and running:

1. Integrating our library on your page (only done once)
1. Setting up your Sponsorship Line Item in GAM (done for each Sponsorship)
1. Setting up targetting in Glimpse Zero (done for each Sponsorship)

### Page integration

1. Include the zero.js tag in `<head>` tags:

  ```html
  <script async src="https://cdn.glimpsezero.io/scripts/zero.js"></script>
  ```

  > ℹ️ You can import a specific version by appending the SEMVER to src url: `zero-1.0.1.js`. If you use `zero.js`  then you're always getting the latest and greatest.

1. Initialised the zero.js command queue:

  ```ts
  window.zjs = window.zjs || {}
  window.zjs.cmd = window.zjs.cmd || []
  ```

1. Initialise zero.js with your unique sync id:

  ```ts
  window.zjs.cmd.push(() => {
    zjs.init({
      id: "demo"
    })
  })
  ```

1. Next, run the prematch process and pass the unmatched ad units to your header bidding setup:

> ℹ️ The default setup expects the adUnitPath to be on a 'path' key. For example units[].path = "/1234567/environment-728x90". If you use a different key this can be set in the initialisation by passing in `adUnitPathKey`. For example: `{ id: "demo", adUnitPathKey: "code" }`.

  ```ts
  const units = [ /* ... */ ]

  window.zjs.cmd.push(() => {
    zjs.init({
      id: "demo"
    })
    const { misses } = zjs.prematch(units)
    runHeaderBidding(misses)
  })
  ```

1. Finally, in the event all units are prematched misses will be an empty list and so prebid won't run 🥳 In this instance, and depending on your unique configuration, you may need to call `googletag.pubads().refresh()`:

  ```ts
  const units = [ /* ... */ ]

  window.zjs.cmd.push(() => {
    zjs.init({
      id: "demo"
    })

    const { misses } = zjs.prematch(units)
    if(misses.length === 0) {
        console.log("All units prematched no prebid to run")
        googletag.cmd.push(() => {
          googletag.pubads().refresh()
        })
    } else {
      runHeaderBidding(misses)
    }
  })
  ```

Bringing this together we get:

> ℹ️ The setup below is for demonstration purposes. As setups will differ from pub to pub.

```html
<script async src="https://www.googletagservices.com/tag/js/gpt.js"></script>
<script async src="https://publisher.com/scripts/prebid.js"></script>
<script async src="https://cdn.glimpsezero.io/scripts/zero.js"></script>
<script>
  const adUnits = [
    {
      div: "unit-1",
      path: "/1234567/environment-728x90",
      sizes: [[728, 90]]
      mediaTypes: {
        banner: {
          sizes: [[728, 90]],
        },
      },
      bids: [
        {
          bidder: "glimpse",
        },
      ],
    },
    // ...
  ]

  // Setup Google Publisher Tag
  window.googletag = window.googletag || {}
  window.googletag.cmd = window.googletag.cmd || []

  googletag.cmd.push(() => {
    adUnits.forEach(({ path, sizes, div }) => {
      googletag
        .defineSlot(path, sizes, div)
        .addService(googletag.pubads())
    })
    googletag.pubads().disableInitialLoad()
    googletag.pubads().enableSingleRequest()
    googletag.enableServices()
  })

  // Setup Glimpse Zero
  window.zjs = window.zjs || {}
  window.zjs.cmd = window.zjs.cmd || []

  window.zjs.cmd.push(() => {
    zjs.init({
      id: "demo"
    })

    const { misses } = zjs.prematch(units)
    if(misses.length === 0) {
        console.log("All units prematched skipping header bidding")
        googletag.cmd.push(() => {
          googletag.pubads().refresh()
        })
    } else {
      runHeaderBidding(misses)
    }
  })

  function runHeaderBidding(units) {
    // Header bidding logic
  }
</script>
```

### Google Ad Manager

In the interest of brevity, this walk through overlooks many values you'll want to set in practice: rate, frequency capping, etc.

1. Create a **Sponsorship** line item to prematch.

  ![line item type](./assets/getting-started-line-item-type.png)

1. Set it's goal to **100%**.

  ![delivery goal](./assets/getting-started-delivery-goal.png)

1. Set inventory targeting to target your desired inventory.

  ![inventroy targeting](./assets/getting-started-target-inventory.png)

1. Target a custom key value to represent when this line item should fire. For example, below we're activating this line item when the key `gz` is present with a value of `true`.

  ![custom targeting](./assets/getting-started-target-custom.png)

1. Add creatives to your line item to match your inventory.

1. Enable your line item.

### Glimpse Zero Portal

Our self-service portal and automatic GAM syncing isn't ready for prime time yet. So, for now, you'll need to provide us with your **Sponsorship** details so we can load it into our system. Generally we'll need to know:

- When it starts and ends
- Blackout or day parting details
- Frequency capping details
- What slots it targets
- What custom targeting key value you've used

### Testing

And that's it, 🥳 However, just to be safe, it's always a good idea to test. It may take a few minutes for the line item to go live. Once it does, you can enable debugging by running `localStorage.debug="glimpse*"` and should see the following process on a page:

1. On the first load zero.js realises it has no targeting loaded and passes all units through to your `runActions` setup.
2. In the background zero.js will fetch targeting information from our servers and save it to the local storage key `gp_zjs_config`.
3. On a subsequent load zero.js will divide your units into matched and unmatched. It will tag matched units with the provided key values and pass the unmatched units, if any, through to `runAuctions`.
4. When runActions completes GPT will tag slots and send an add call to Google Ad Manager.
5. The response should have your Sponsorship line item against the matched slot, and the other line items should fire as you would expect them to (for example, triggering a Prebid line item).

If you have any issues reachout to [support@glimpsezero.io](mailto:support@glimpsezero.io).

## What next?

[👈 Back (introduction)](./introduction.md) | [Next (reference) 👉](./reference.md)
