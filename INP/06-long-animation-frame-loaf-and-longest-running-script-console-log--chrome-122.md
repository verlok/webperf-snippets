# Long animation frame (LoAF) and the longtest running script

If you want to log to console, or send to analytics, the longest animation frame and the longest running script within that long animation frame.

The following code works on Chrome versions from 122 up. If you willing to support older versions of Chrome, you should just use the [polyfilled version of this script](./06-long-animation-frame-loaf-and-longest-running-script-console-log--chrome-121.md).


```js
const REPORTING_THRESHOLD_MS = 200;

const observer = new PerformanceObserver((list) => {
  let longestScript = null; // Track the script with the longest duration
  let longestEntry = null; // Track the entry containing the longest script
  for (const entry of list.getEntries()) {
    if (
      entry.duration > REPORTING_THRESHOLD_MS &&
      entry.firstUIEventTimestamp > 0
    ) {
      const longestDuration = entry.scripts.reduce(
        (maxDuration, script) => Math.max(maxDuration, script.duration),
        0
      );
      const currentLongestScript = entry.scripts.find(
        (script) => script.duration === longestDuration
      );
      if (!longestScript || longestDuration > longestScript.duration) {
        // Update longestScript and longestEntry
        longestScript = currentLongestScript;
        longestEntry = entry;
      }
    }
  }
  if (longestScript && longestEntry) {
    // Example here logs to console, but could also report back to analytics
    const newPropertyNames = !!longestScript.invokerType;
    if (!newPropertyNames) {
      console.warning("Old LoAF property names in the API, exiting");
      return;
    }
    console.log({
      loaf_entry: {
        start: longestEntry.startTime,
        duration: longestEntry.duration,
      },
      longestScript: ({
        invoker,
        invokerType,
        duration,
        sourceURL,
        sourceCharPosition,
        sourceFunctionName,
        pauseDuration,
        forcedStyleAndLayoutDuration,
      } = longestScript),
    });
    // console.log(longestScript); //need anything else?
  }
});

observer.observe({ type: "long-animation-frame", buffered: true });
```
