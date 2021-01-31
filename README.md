# @schuelerkarriere/simple-map

A simple to use [mapbox](https://www.mapbox.com/) wrapper written for [schuelerkarriere.de](https://schuelerkarriere.de).

## Map with Markers

```vue
<template>
  <sk-map-with-marker access-token="token" :markers="markers"></sk-map-with-marker>
</template>

<script>
import { SkMapWithMarker } from '@schuelerkarriere/simple-map';

export default {
  components: { SkMapWithMarker },
  data: () => ({
    markers: [
      {           // [Longitude, Latitude]
        coordinates: [9.735603, 52.373920],
        title: "Hannover" // Optional
      },
    ]
  })
}
</script>
```
