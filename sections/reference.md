# Reference

Once loaded you can access the zero.js object on on `window.zjs`.

## zero.js interface and types

```ts
interface IZeroJsApi {
  /**
   * A list of commands to execute when the zero.js api is ready. Once ready
   * commands pushed onto this array are executed immediately.
   */
  cmd: Command[]

  /**
   * Sets the publisher id to use for sync'ing line items and reporting events. This is required and
   * must be called before interacting with zero.js.
   *
   * @param config:IZeroJsConfig on-site configuration options
   * @returns void
   */
  init: (config: Config) => void

  /**
   * Register an event listener against a specific event.
   *
   * @param event: EventType the event to listen for
   * @param listener: Listener the function to callback when the specified event occurs
   * @returns void
   */
  on: (event: EventType, handler: EventHandler) => void

  /**
   * Run pre-emptive ad server targeting against the given ad units.
   *
   * @param units: AdUnit[] a list of ad units for demand path optimisation
   * @return PrematchResult an object containing misses and hits.
   */
  prematch: (units: AdUnit[]) => { misses: AdUnit[], hits: AdUnit[] }

  /**
   * Delete all zero.js state data.
   */
  reset: () => void
}

interface Config {
  id: PubId
}

type Command = () => void

type AdUnit = {
  code: string
}

export type EventHandler = <T>(event: EventBody<T>) => void

export type EventBody<P> = {
  ts: number
  type: EventType
  payload: P | undefined
}

export enum EventType {
  PlanLoaded = "PLAN_LOADED",
  PlanFetched = "PLAN_FETCHED",
  PlanSaved = "PLAN_SAVED",
  PrematchStarted = "PREMATCH_STARTED",
  PrematchFinished = "PREMATCH_FINISHED",
  AdUnitsTagged = "AD_UNITS_TAGGED",
  AdServed = "AD_SERVED",
  AdViewed = "AD_VIEWED",
  QueuedMatchedUnitTagging = "QUEUED_MATCHED_UNIT_TAGGING",
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
