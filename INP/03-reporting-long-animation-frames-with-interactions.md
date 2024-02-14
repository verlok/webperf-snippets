# Reporting long animation frames with interactions

The following code logs all LoAF entries greater than 150 milliseconds where an interaction occurred during the frame. 

The 150 is chosen here because it is slightly less than the 200 millisecond "good" INP threshold. 

You could choose a higher or lower value depending on your needs.

```js
const REPORTING_THRESHOLD_MS = 150;

const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    if (
      entry.duration > REPORTING_THRESHOLD_MS &&
      entry.firstUIEventTimestamp > 0
    ) {
      // Example here logs to console, but could also report back to analytics
      console.log(entry);
    }
  }
});

observer.observe({ type: "long-animation-frame", buffered: true });
```