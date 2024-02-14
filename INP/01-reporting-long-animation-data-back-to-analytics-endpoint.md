# Reporting long animation frames data back to an analytics endpoint

The LoAF performance entry includes valuable information.

One strategy would be to monitor all LoAFs and beacon the ones above a certain threshold back to an analytics endpoint for later analysis:


```js
const REPORTING_THRESHOLD_MS = 150;

const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    if (entry.duration > REPORTING_THRESHOLD_MS) {
      // Example here logs to console, but could also report back to analytics
      console.log(entry);
    }
  }
});
observer.observe({ type: "long-animation-frame", buffered: true });
```