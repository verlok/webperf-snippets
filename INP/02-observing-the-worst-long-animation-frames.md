# Observing the worst long animation frames

Sites may wish to collect data on the longest animation frame (or frames), to reduce the volume of data that needs to be beaconed. 

So no matter how many long animation frames a page experiences, only data for the worst one, five, or however many long animation frames absolutely necessary is beaconed back.

```js
const MAX_LOAFS_TO_CONSIDER = 10;
let longestBlockingLoAFs = [];

const observer = new PerformanceObserver((list) => {
  longestBlockingLoAFs = longestBlockingLoAFs
    .concat(list.getEntries())
    .sort((a, b) => b.blockingDuration - a.blockingDuration)
    .slice(0, MAX_LOAFS_TO_CONSIDER);
});
observer.observe({ type: "long-animation-frame", buffered: true });

console.table(longestBlockingLoAFs);
```