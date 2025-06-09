```js
try {
  const observer = new PerformanceObserver((entryList) => {
    const lcpEntries = entryList.getEntries();
    if (lcpEntries.length > 0) {
      console.log('--- Largest Contentful Paint (LCP) Candidates ---');
      lcpEntries.forEach((entry, index) => {
        const element = entry.element;
        console.log(`%cCandidate ${index + 1}:`, 'font-weight: bold;');
        
        // 1. ALWAYS log the Rendered Size for every element
        console.log(`  - Rendered Size: ${entry.size}px² (How big it appears on screen)`);

        let isMedia = false;
        let intrinsicW = 0;
        let intrinsicH = 0;

        // 2. Check if it's an IMAGE
        if (element && element instanceof HTMLImageElement) {
          intrinsicW = element.naturalWidth;
          intrinsicH = element.naturalHeight;
          isMedia = true;
        } 
        // 3. Check if it's a VIDEO
        else if (element && element instanceof HTMLVideoElement) {
          intrinsicW = element.videoWidth;
          intrinsicH = element.videoHeight;
          isMedia = true;
        }

        // 4. If it was an Image or Video, ALSO log the Intrinsic Size
        if (isMedia) {
          console.log(`  - Intrinsic Size: ${intrinsicW}x${intrinsicH} (Original file dimensions)`);
        }
        
        console.log('  - Element:', element);
      });

      const finalLCP = lcpEntries[lcpEntries.length - 1];
      console.log('--- Final LCP Element ---');
      console.log('%cElement:', 'font-weight: bold; color: green;', finalLCP.element);
      
    } else {
      console.log('No LCP candidates were observed.');
    }
  });

  observer.observe({ type: 'largest-contentful-paint', buffered: true });

  setTimeout(() => {
    observer.disconnect();
    console.log('--- Observer disconnected ---');
  }, 2000);

} catch (e) {
  console.error('This browser does not support the required PerformanceObserver API.', e);
}
```