# Measure custom metrics with performance `mark` and `measure`

When you want to track when something happened, and measure how much time it took.

```js
perfrmance.mark('someCode:start');
// The code to be tracked will be here
performance.mark('someCode:end');
performance.measure('someCode', 'someCode:start', 'someCode:end');
```

Replace `someCode` with a name that makes sense for your use case.