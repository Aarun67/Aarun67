// This is a JavaScript script for a Google Chrome extension

// First, we need to install the Grammarly API client library
// You can do this by running the following command in your terminal:
// npm install grammarly

const grammarly = require('grammarly');

// Next, we need to set up the Grammarly API client with our API key
// You can get your API key by creating a Grammarly account and
// signing up for the Grammarly API at:
// https://www.grammarly.com/developers/docs/getting-started

const client = grammarly.createClient({
  apiKey: 'YOUR_API_KEY_HERE'
});

// Now we can define a function that takes some text as input
// and returns the corrected text

function correctGrammar(text) {
  // Use the Grammarly API's "correct" method to get a list of corrections
  return client.correct(text).then((corrections) => {
    // Apply the corrections to the original text
    let correctedText = text;
    corrections.forEach((correction) => {
      correctedText = correctedText.slice(0, correction.start) + correction.text + correctedText.slice(correction.end);
    });
    // Return the corrected text
    return correctedText;
  });
}

// Finally, we can use this function in our Chrome extension
// For example, we can use it to correct the text of a selected element on the page:

chrome.runtime.onMessage.addListener((request, sender, sendResponse) => {
  if (request.action == 'correctGrammar') {
    const selectedElement = request.element;
    const originalText = selectedElement.textContent;
    correctGrammar(originalText).then((correctedText) => {
      selectedElement.textContent = correctedText;
      sendResponse({ result: 'success' });
    }).catch((error) => {
      console.error(error);
      sendResponse({ result: 'error', error: error });
    });
  }
});
