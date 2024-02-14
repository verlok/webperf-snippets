# Surfacing long animation frames data in devtools

You can surface long animation frames in DevTools using the performance.measure() API, which are then displayed in the DevTools user timings track in **performance traces** to show where to focus your efforts for performance improvements:

```js
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    performance.measure("LoAF", {
      start: entry.startTime,
      end: entry.startTime + entry.duration,
    });
  }
});

observer.observe({ type: "long-animation-frame", buffered: true });
```