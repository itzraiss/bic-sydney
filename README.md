**A demonstration using this module with [ChatGPT API](https://github.com/waylaidwanderer/node-chatgpt-api) and [PandoraAI](https://github.com/waylaidwanderer/PandoraAI) together.**
<p align="center">
  <img alt="A ChatGPT API + PandoraAI + BIC Sydney demo" src="./demos/chatgptapi_with_pandoraai.gif" width="820px" height="820px">
</p>

# node-bic-sydney
> A Node.js module that provides some 'Copilot Image Creator' functions.

## Prerequisites
- Node.js >= 16.0.0
- Sydney's optionsSets must contain 'gencontentv3' if you want Copilot to provide prompts for you.

## Usage

### Module
```bash
npm i @timefox/bic-sydney
```

### Usage
<details>
<summary><strong>Example 1. Create a client side render iframe</strong></summary>

Create images by Copilot Image Creator, and get an iframe pointing to the page contains the images created.
This method is most likely what the original Copilot browser client does.
The format of {CopilotImageCreator} options is almost same as the CopilotAiClient options of '@waylaidwanderer/chatgpt-api'.
```JS
import { CopilotImageCreator } from '@timefox/bic-sydney';
import crypto from 'crypto';

// Setup the required options.
const options = {
    // Necessary for some people in different countries, e.g. China (https://cn.bing.com)
    host: '',
    // The "_U" cookie value from Copilot.com
    userToken: '',
    // If the above doesn't work, provide all your cookies as a string instead
    cookies: '',
    // A proxy string like "http://<ip>:<port>"
    proxy: '',
    // (Optional) Set to true to enable `console.debug()` logging
    debug: false,
    // (Optional) The user agent for the network request.
    userAgent: '',
};

const prompt = 'a fox plays xbox';
const messageId = crypto.randomUUID();

// Make a creation request with given prompt. With 'gencontentv3' option on, Copilot will give you the prompt
// in a message which's contentType is 'IMAGE'.
const imageIframe = new CopilotImageCreator(options).genImageIframeCsr(prompt, messageId);
imageIframe.then((result) => {
    console.debug(result);
}).catch((error) => {
    console.debug(error);
});

```
**Note: If this iframe is returned to client browser, the user must be logged in to Copilot.com in order to generate the image successfully. The user's cookie is required for the polling requests of the generation process.**
</details>

<details>
<summary><strong>Example 2. Create a server side render iframe</strong></summary>

Create images by Copilot Image Creator, and get an iframe which uses the 'srcdoc' attribute to hold the rendered result page.
**This method does not require the user to have logged in to Copilot.com on the client side.  The entire request process is completed by the server proxy.**
**It's very useful for the clients can not visit the new Copilot's service directly.**
```JS
import { CopilotImageCreator } from '@timefox/bic-sydney';
import crypto from 'crypto';

// Setup the required options.
const options = {
    // Necessary for some people in different countries, e.g. China (https://cn.bing.com)
    host: '',
    // The "_U" cookie value from bing.com
    userToken: '',
    // If the above doesn't work, provide all your cookies as a string instead
    cookies: '',
    // A proxy string like "http://<ip>:<port>"
    proxy: '',
    // (Optional) Set to true to enable `console.debug()` logging
    debug: false,
    // (Optional) The user agent for the network request.
    userAgent: '',
};

const prompt = 'a fox plays xbox';
const messageId = crypto.randomUUID();

// Make a creation request with given prompt. With 'gencontentv3' option on, Copilot will give you the prompt
// in a message which's contentType is 'IMAGE'.
// The onProgress is a callback function. If onProgress is provided and returns true, the request will be cancelled.
imageIframe = new CopilotImageCreator(options).genImageIframeSsr(
    prompt,
    messageId,
    (progress) => {
        if (progress?.contentIframe) {
            console.debug('contentIframe:');
            console.debug(progress.contentIframe);
        }
        if (progress?.pollingStartTime) {
            console.debug(`pollingStartTime: ${progress.pollingStartTime}`);
        }
    },
);
imageIframe.then((result) => {
    console.debug(result);
}).catch((error) => {
    console.debug(error);
});

```

</details>

**For more usage, please refer to the module's documentation.**

## Documents
```bash
npm run doc
```

It will generate the module's docs for you under **./docs**.

## License
This project is licensed under the MIT License.