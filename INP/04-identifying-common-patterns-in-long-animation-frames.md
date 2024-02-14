# Identifying common patterns in long animation frames

An alternative strategy would be to look at common scripts appearing the most in long animation frame entries.

Data could be reported back at a script and/or character position level to identify repeat offenders.

This may work particularly well for customizable platforms where themes or plugins causing performance issues could be more easily identified across a number of sites.

The execution time of common scripts—or third-party origins—in long animation frames could be summed up and reported back to identify common contributors to long animation frames across a site or a collection of sites.


```js
const observer = new PerformanceObserver((list) => {
  const allScripts = list.getEntries().flatMap((entry) => entry.scripts);
  const scriptSource = [
    ...new Set(allScripts.map((script) => script.sourceLocation)),
  ];
  const scriptsBySource = scriptSource.map((sourceLocation) => [
    sourceLocation,
    allScripts.filter((script) => script.sourceLocation === sourceLocation),
  ]);
  const processedScripts = scriptsBySource.map(([sourceLocation, scripts]) => ({
    sourceLocation,
    count: scripts.length,
    totalDuration: scripts.reduce(
      (subtotal, script) => subtotal + script.duration,
      0
    ),
  }));
  processedScripts.sort((a, b) => b.totalDuration - a.totalDuration);
  // Example here logs to console, but could also report back to analytics
  console.table(processedScripts);
});

observer.observe({ type: "long-animation-frame", buffered: true });
```