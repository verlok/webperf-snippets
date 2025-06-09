```js
const po = new PerformanceObserver((list) => {
  let entries = list.getEntries();
 
  entries = dedupe(entries, "startTime");
 
  entries.forEach((item, i) => {
    console.dir(item);
    console.log(
      `${i + 1} current LCP item : ${item.element}: ${Math.max(item.startTime - getActivationStart(), 0)}`,
    );
    item.element ? (item.element.style = "border: 5px dotted lime;") : "";
  });
 
  const lastEntry = entries[entries.length - 1];
  console.log(`LCP is: ${Math.max(lastEntry.startTime - getActivationStart(), 0)}`);
});
 
po.observe({ type: "largest-contentful-paint", buffered: true });
 
function dedupe(arr, key) {
  return [...new Map(arr.map((item) => [item[key], item])).values()];
}
 
function getActivationStart() {
  const navEntry = getNavigationEntry();
  return (navEntry && navEntry.activationStart) || 0;
}
 
function getNavigationEntry() {
  const navigationEntry =
    self.performance &&
    performance.getEntriesByType &&
    performance.getEntriesByType('navigation')[0];
 
  if (
    navigationEntry &&
    navigationEntry.responseStart > 0 &&
    navigationEntry.responseStart < performance.now()
  ) {
    return navigationEntry;
  }
}
```