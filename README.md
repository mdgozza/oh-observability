# Oh, Observability

Oh is a Typescript library for reporting structured non-critical log data to observability & analytics platforms.

**Type Safe:** Oh uses the power of Typescript augmented by the conventions pioneered by [CloudEvents](https://cloudevents.io/) to help deliver consistently shaped logs; Perfect for analysing, reporting, alerting, and dashboarding real-time insights of any running application.

**Frictionless:** Setup once, use anywhere. Oh can conveniently be imported from any location in the codebase and carry with it the conventions of the repository through typescript types. Oh will never negatively impact your runtime.

# Installation

Oh has been designed for gradual adoption from the start, and you can use as little or as much Oh as you need.

`npm install oh-observability`

# Usage

### Starting to use Oh is as easy as 1,2,3.

1. Setup Oh with the Destination Plugin(s) of your choice.
2. Add your Custom Events to the Event Registry
3. Start sending structured events!

# The Destination Plugin System

Oh can be configured to send its log events to any Observability ( or Logging! ) Platform Simply use one of the built in plugins, or create your own!

```import {Oh} from 'oh-observability'
import {NewRelicDestination} from 'oh-observability/plugins'

const newRelicDestination = new NewRelicDestination({
  newRelicApiKey: 'abc123',
  useNewRelicSdk: true
})

Oh.registerDestinationPlugin(newRelicDestination)
```

# The Event Registry

Oh is highly configurable and helps to enable minimal footprint api usage through its event registry

When registering events with the registry, Oh will automatically convert custom event types to be fully [CloudEvents](https://cloudevents.io/) spec v1 compliant.

### Registering an Event with the Event Registry

```
import {Oh} from 'oh-observability'

Oh.registerCustomEvent({
  eventName: 'softAllocationProductAllocationLog',
  description: 'Production Allocation Events that happen as a part of the Soft Allocation System',
  type: 'com.softAllocation.allocate.log',
  source: 'com.softAllocation.calculateProductETADates',
  dataContentType: 'json'
})
```

### With event registry

```
import {Oh} from 'oh-observability'

async function calculateProductETADates(products: Product[]): ProductETA[] {

  return products.map(product => {
    if(product.isAvailableNow){
      const eta = 'now';

      Oh.recordEvent({
        type: 'softAllocationProductAllocationLog',
        data: {
          sku: product.sku,
          nextPO: product.nextPO,
          openOrderCount: product.openOrderCount,
          eta
        }
      })

      return {
        ...product,
        eta
      }
    }
    // ...
  })

}
```

### Without event registry setup

```
import {Oh} from 'oh-observability'

async function calculateProductETADates(products: Product[]): ProductETA[] {

  return products.map(product => {
    if(product.isAvailableNow){
      const eta = 'now'

      Oh.recordCustomEvent({
        type: 'com.softAllocation.allocate.log',
        source: 'com.softAllocation.calculateProductETADates',
        id: uuidv4(),
        time: Date.now(),
        datacontenttyoe: "application/json",
        data: JSON.stringify({
          sku: product.sku,
          nextPO: product.nextPO,
          openOrderCount: product.openOrderCount,
          eta
        })
      })

      return {
        ...product,
        eta
      }
    }
    // ...
  })

}
```
