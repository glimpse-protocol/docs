# Reference

Once loaded you can access the zero.js object on on `window.zjs`.

## zero.js interface and types

```ts
export interface IZeroJs {
  /**
   * A list of commands to execute when the zero.js api is ready. Once ready
   * commands pushed onto this array are executed immediately.
   */
  cmd: Command[]

  /**
   * Sets the publisher id to use for sync'ing line items and reporting events. This is required and
   * must be called before interacting with zero.js.
   *
   * @param id:string your unique 6 character publisher id provided by glimpse zero
   * @returns void
   */
  setPubId: (id: string) => void

  /**
   * Register an event listener against a specific event.
   *
   * @param event: ZerojsEventType the event to listen for
   * @param listener: Listener the function to callback when the specified event occurs
   * @returns void
   */
  onEvent: (event: ZerojsEventType, listener: Listener) => void

  /**
   * Run pre-emptive ad server targeting against the given ad units.
   *
   * @param units: AdUnit[] a list of ad units for demand path optimisation
   * @return [Unmatched[], Matched[]] a tuple of unmatched and matched ad units
   */
  pretarget: (units: AdUnit[]) => [Unmatched[], Matched[]]
}

export type Command = () => void

export type AdUnit = {
  code: string
}

export type Listener = <P>(event: ZerojsEvent<P>) => void

export type ZerojsEvent<P> = {
  type: ZerojsEventType
  payload?: P
}

export enum ZerojsEventType {
  ConfigUpdated = "configUpdated",
  AuctionRun = "auctionRun",
  AdUnitsTargeted = "adUnitsTargeted",
  AdRequested = "adRequested",
  AdServed = "adServed",
  AdViewed = "adViewed"
}
```

## Local Storage

The following keys are used by Zerojs to facilitate different functionalities

| key               | description                                        |
| ----------------- | -------------------------------------------------- |
| `gp_zjs_config`   | used to store publisher config                     |
| `gp_zjs_report`   | used to store metrics and event data for reporting |
| `gp_zjs_freq_cap` | used to store data for frequency capping           |

## Endpoint Calls

The following HTTPS endpoints are used by Zerojs

| endpoint                                                       | description                                                       |
| -------------------------------------------------------------- | ----------------------------------------------------------------- |
| `https://api.glimpsezero.io/edge/v1/sync/state/:pubId/:digest` | sync and update publisher config if the config digest has changed |
| `https://api.glimpsezero.io/edge/v1/sync/events/:pubId`        | report impression metrics and events                              |

## What Next?

[👈 Back (getting started)](./getting-started.md) | [Next (faq) 👉](./faq.md)
