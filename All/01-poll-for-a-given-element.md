Say you're working on a website and you're getting notified from your RUM tool or a colleague that the element `DIV#disc-cp-modal__content` is the LCP element, or it has an INP issue, but the element is not immediately present on the page if you querySelect it. 

You need a script that polls for the presence of that element, or any other given element, and stops in debug mode when it appears in the DOM.

```js
/**
 * Polls for the presence of a specific element and stops in debug mode when it appears.
 * @param {string} selector - CSS selector of the element to poll for.
 * @param {number} interval - Polling interval in milliseconds (default: 500ms).
 * @param {number} timeout - Maximum time to poll in milliseconds (default: 10000ms).
 */
function waitForElement(selector, interval = 500, timeout = 10000) {
    let elapsed = 0;
    
    const poller = setInterval(() => {
        // Increment elapsed time
        elapsed += interval;

        // Check if element exists
        const element = document.querySelector(selector);
        if (element) {
            clearInterval(poller);
            console.log(`Element "${selector}" found. Stopping.`);
            debugger;  // Stops the script in debug mode
        } else if (elapsed >= timeout) {
            // Clear interval if timeout is reached
            clearInterval(poller);
            console.warn(`Timeout reached. Element "${selector}" not found.`);
        }
    }, interval);
}

// Usage example
waitForElement("#disc-cp-modal__content");
```