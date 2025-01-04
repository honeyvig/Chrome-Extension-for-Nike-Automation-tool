# Chrome-Extension-for-Nike-Automation-tool
develop a Chrome extension that can input text into an iFrame for Nike automation. This tool is intended to assist users by automating their interactions on the platform, making the process faster and more efficient. The ideal candidate should have experience with Chrome extensions, JavaScript, and working with iFrames. 
---------------
To develop a Chrome extension that automates text input into an iFrame on a website like Nike, the extension should be able to:

    Detect the iFrame on the webpage.
    Inject the text into the fields within the iFrame.
    Allow users to trigger the input action automatically or via a button click.

Below is the implementation for the Chrome extension:
1. Manifest File (manifest.json)

This configuration file defines the necessary permissions, content scripts, and background functionality for the Chrome extension.

{
  "manifest_version": 3,
  "name": "Nike Automation Extension",
  "version": "1.0",
  "description": "Automates text input into Nike iFrames for faster interactions.",
  "permissions": [
    "activeTab",
    "storage"
  ],
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["https://www.nike.com/*"],
      "js": ["content.js"]
    }
  ],
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  }
}

2. Popup HTML (popup.html)

The popup allows users to input text that will be filled into the iFrame fields.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nike Automation</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 10px;
      width: 300px;
    }
    input, button {
      width: 100%;
      padding: 8px;
      margin: 10px 0;
    }
    button {
      background-color: #4CAF50;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #45a049;
    }
  </style>
</head>
<body>
  <h3>Input Data</h3>
  <form id="inputForm">
    <label for="name">Name:</label>
    <input type="text" id="name" required />
    <label for="email">Email:</label>
    <input type="email" id="email" required />
    <button type="submit">Submit to Nike iFrame</button>
  </form>

  <script src="popup.js"></script>
</body>
</html>

3. Popup JavaScript (popup.js)

This script handles the form submission, captures user inputs, and sends the data to the content script that will handle injecting it into the iFrame.

document.getElementById("inputForm").addEventListener("submit", function (event) {
  event.preventDefault();

  const name = document.getElementById("name").value;
  const email = document.getElementById("email").value;

  // Send data to content script
  chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
    chrome.tabs.sendMessage(tabs[0].id, { action: 'fillForm', name: name, email: email });
  });
});

4. Content Script (content.js)

This script interacts with the iFrame on the Nike page, detecting it and filling in the provided text values into the form fields within the iFrame.

// Listen for messages from the popup.js
chrome.runtime.onMessage.addListener(function (message, sender, sendResponse) {
  if (message.action === 'fillForm') {
    const name = message.name;
    const email = message.email;

    // Try to find the iFrame
    const iframe = document.querySelector("iframe");
    if (iframe) {
      // Access the iFrame's content window and document
      const iframeDocument = iframe.contentWindow.document;

      // Fill in the fields if they exist in the iFrame
      const nameField = iframeDocument.querySelector("input[name='name'], input[id='name']");
      const emailField = iframeDocument.querySelector("input[name='email'], input[id='email']");

      if (nameField && emailField) {
        nameField.value = name;
        emailField.value = email;
      } else {
        alert("Unable to find the name or email field in the iFrame.");
      }
    } else {
      alert("iFrame not found.");
    }
  }
});

5. Background Script (background.js)

This is a placeholder for any background functionality. In this case, the background script doesn’t need to do much, but you can extend it to handle any background processes or data persistence in the future.

// background.js

chrome.runtime.onInstalled.addListener(() => {
  console.log("Nike Automation Extension installed!");
});

6. Icons

Add the following icons to your extension’s icons/ directory:

    icons/icon16.png: 16x16 icon.
    icons/icon48.png: 48x48 icon.
    icons/icon128.png: 128x128 icon.

You can create these icons using a graphic design tool, or you can download free icons from sources like Flaticon.
7. Testing the Extension

To test the extension:

    Open Chrome and go to chrome://extensions/.
    Enable "Developer mode".
    Click "Load unpacked" and select the folder containing your extension's files.
    Once the extension is loaded, open a Nike website (or any page with an iFrame you want to test).
    Click on the extension icon in the Chrome toolbar to open the popup.
    Fill in the name and email, and then click "Submit to Nike iFrame."
    The data will be automatically populated into the iFrame's fields.

8. How It Works

    The extension injects content into any Nike page that contains an iFrame.
    Users input their data into the popup, and this data is sent to the content script.
    The content script then looks for the iFrame on the page and attempts to fill the specified form fields (e.g., name and email) with the provided data.

Conclusion

This Chrome extension allows users to automatically fill in form fields within an iFrame on Nike’s website (or any similar website). It uses:

    A popup to collect input from users.
    A content script to interact with the page and iFrame.
    Basic permissions and manifest structure for handling the automation.

Feel free to extend this extension to include more fields or additional automation steps depending on the specific requirements of the Nike site or any other platform you are automating.
