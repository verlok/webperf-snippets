# Log to console long animation frame (LoAF) and the longtest running script

Sometimes you just want to log to console long animation frames as they happen, and the longest running script inside that long animation frame. 

The following code works from Chrome 122 up, but if you are on Chrome 121 or lower you need to use the [polyfilled version of this cript](./06-long-animation-frame-loaf-and-longest-running-script-console-log--chrome-121.md).


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