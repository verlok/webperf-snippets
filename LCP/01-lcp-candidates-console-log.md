```js
try {
  const observer = new PerformanceObserver((entryList) => {
    const lcpEntries = entryList.getEntries();
    if (lcpEntries.length > 0) {
      console.log('Largest Contentful Paint (LCP) Candidates:');
      lcpEntries.forEach((entry, index) => {
        console.log(`Candidate ${index + 1}:`);
        console.log(`  - Time: ${Math.round(entry.startTime)}ms`);
        console.log(`  - Size: ${entry.size}px²`);
        console.log('  - Element:', entry.element);
      });
      // The last entry in the list is the final LCP element
      const finalLCP = lcpEntries[lcpEntries.length - 1];
      console.log('%cFinal LCP Element:', 'font-weight: bold; color: green;', finalLCP.element);
    } else {
      console.log('No LCP candidates were observed. The page might be too simple or the observer ran too late.');
    }
  });

  // Start observing LCP entries. The 'buffered: true' flag ensures
  // you get entries that occurred even before this script ran.
  observer.observe({ type: 'largest-contentful-paint', buffered: true });

  // Disconnect the observer after a short delay to avoid continuously
  // logging and to finalize the list.
  setTimeout(() => {
    observer.disconnect();
    console.log('Observer disconnected.');
  }, 2000);

} catch (e) {
  console.error('This browser does not support the PerformanceObserver API with "largest-contentful-paint".', e);
}
```