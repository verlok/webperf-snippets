Say you're working on a website and you're getting notified from your RUM tool or a colleague that the element `DIV#disc-cp-modal__content` is the LCP element, or it has an INP issue, but the element is not immediately present on the page if you querySelect it. 

You need a script that polls for the presence of that element, or any other given element, and stops in debug mode when it appears in the DOM.

```js
/**
 * Observes the DOM for the addition of a specific element and stops in debug mode when it appears.
 * @param {string} selector - CSS selector of the element to observe for.
 * @param {number} timeout - Maximum time to observe in milliseconds (default: 10000ms).
 */
function waitForElement(selector, timeout = 10000) {
    let timer;

    // Create a MutationObserver instance to listen for DOM changes
    const observer = new MutationObserver((mutationsList, observer) => {
        // Check if the element now exists in the DOM
        const element = document.querySelector(selector);
        if (element) {
            console.log(`Element "${selector}" found. Stopping observer.`);
            debugger; // Stops the script in debug mode
            observer.disconnect(); // Stop observing once the element is found
            clearTimeout(timer); // Clear the timeout as well
        }
    });

    // Define the timeout to stop observing after the specified time
    timer = setTimeout(() => {
        observer.disconnect();
        console.warn(`Timeout reached. Element "${selector}" not found.`);
    }, timeout);

    // Start observing the entire document for child list changes and subtree modifications
    observer.observe(document, {
        childList: true,
        subtree: true
    });
}

// Usage example
waitForElement("#disc-cp-modal__content");
```