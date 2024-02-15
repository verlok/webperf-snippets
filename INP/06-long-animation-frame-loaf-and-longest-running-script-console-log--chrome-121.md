# Long animation frame (LoAF) and the longtest running script

If you want to log to console, or send to analytics, the longest animation frame and the longest running script within that long animation frame.

The following code works on Chrome versions previous to 122. If you willing to support from Chrome 122 up, you should just use the [non-polyfilled version of this script](./06-long-animation-frame-loaf-and-longest-running-script-console-log--chrome-122.md).


```js
const REPORTING_THRESHOLD_MS = 150;

const sourceParts = (source) => {
  const atPos = source.indexOf("@");
  const lastColon = source.lastIndexOf(":");
  const lastColonIsHttpColon = lastColon < 6;
  const sourceURL = lastColonIsHttpColon
    ? source.substring(atPos + 1)
    : source.substring(atPos + 1, lastColon);
  const sourceFunctionName = source.substring(0, atPos);
  const sourceCharPosition = lastColonIsHttpColon
    ? 0
    : Number(source.substring(lastColon + 1));
  return {
    sourceURL,
    sourceFunctionName,
    sourceCharPosition,
  };
};

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
    const objToLog = newPropertyNames
      ? // For Chrome 122+
        {
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
        }
      : // For Chrome up to 121
        {
          loaf_entry: {
            start: longestEntry.startTime,
            duration: longestEntry.duration,
          },
          longestScript: {
            invoker: longestScript.name,
            invokerType: longestScript.type,
            duration: longestScript.duration,
            ...sourceParts(longestScript.sourceLocation),
            newPropertyNames: false,
          },
        };
    console.log(objToLog, `Used new property names: ${newPropertyNames}`);
    // console.log(longestScript); //need anything else?
  }
});

observer.observe({ type: "long-animation-frame", buffered: true });
