## `What is Router Nitro ?`

Router Nitro is a cross-chain swapping engine or protocol designed to enable cross-chain asset transfers. It serves as a mechanism for seamlessly moving digital assets or tokens between different blockchain networks

The Lastest Version employs a trustless method for managing cross-chain asset transfers. In this system, a forwarder entity delivers the requested asset to the user on the destination chain. After confirming the forwarder's successful settlement on the destination chain, they can access the user's deposited funds on the source chain

## `Build Don't Talk`

In this tutorial we'll be building an AI DApp which can execute CrossChain Transactions based on prompts. 

![download](https://github.com/router-resources/NitroAIDApp/assets/124175970/f46ad4ce-af52-4cf5-872e-f4eeb1b1bc9b)



## `Running the Application`

**Clone the Repository**
```
git clone https://github.com/router-resources/NitroAIDApp.git
```

**Install the necessary packages**

```
npm install
```

**Replace Your_OpenAI_API_KEY with your OpenAI API Key**

![Screenshot 2024-06-28 at 5 59 18 PM](https://github.com/router-resources/NitroAIDApp/assets/124175970/2433e270-4217-49fd-a390-7b3f9e20dab3)


Run on localhost

```
npm start
```
Your application will start running

## `Understanding the Code`

### `Setting up the project`

**Downloading necessary Libraries**

Run the following commands in your Coding Terminal to download the necessary libraries required in our project

```
npm install axios
```

```
npm install ethers@5.7.2
```

```
npm install openai
```

**Getting OpenAI API Key**

Visit [https://openai.com/index/openai-api/](url) and click on ***Explore API*** . Make sure to create an account on OpenAI before proceeding with the step.

It will redirect you to [https://openai.com/api/](url) . Click on ***Start Building*** .

It will redirect you to OpenAI Platform . Click on ***API Keys** option in the left Side Bar and create your new API Key

Copy the API Key somewhere as you need it afterwards

***Imports***

Import the downloaded libararies by 

```
import OpenAI from "openai";
import axios from "axios"
import { ethers } from 'ethers';
```

**Extract Variable Function**


The `extractVariables` function is designed to extract specific variables from a given sentence using the OpenAI API. It identifies and extracts the `sourceToken`, `sourceChain`, `desToken`, `desChain`, and `amount` from the sentence based on predefined sets of possible values.

#### Parameters

- `sentence` (String): The input sentence from which the variables need to be extracted.

#### Returns

- A Promise that resolves to an object containing the extracted variables in the following format:
  ```json
  {
    "sourceToken": "x",
    "sourceChain": "a",
    "desToken": "y",
    "desChain": "b",
    "amount": "z"
  }
  ```

#### Usage

```javascript
const sentence = "Transfer 100 USDT from Holsky to Fuji in exchange for USDC.";
extractVariables(sentence).then(variables => {
  console.log(variables);
});
```

#### Function Details

1. **Initialize OpenAI Instance**: 
   The function initializes an instance of the OpenAI client using the provided API key.

   ```javascript
   const openai = new OpenAI({
     apiKey: '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',
     dangerouslyAllowBrowser: true 
   });
   ```

2. **Call OpenAI API**:
   The function sends a request to the OpenAI API's `chat.completions.create` endpoint, instructing it to extract the required variables from the input sentence. It uses the `gpt-3.5-turbo` model.

   ```javascript
   const completion = await openai.chat.completions.create({
     messages: [{ role: "system", content: `Extract the sourceToken, sourceChain, desToken, desChain, and amount from the following sentence. Source Chains can be Holsky, Fuji and Amoy. Similarly, Destination Chains can be Holsky, Fuji and Amoy. Source and Destination Tokens can be AFTT, USDT, USDC. 
     "${sentence}"
     Return the results in the following format: 
     {
       "sourceToken": "x",
       "sourceChain": "a",
       "desToken": "y",
       "desChain": "b",
       "amount": "z"
     }` }],
     model: "gpt-3.5-turbo",
   });
   ```

3. **Parse and Return Result**:
   The function parses the JSON response from the OpenAI API and returns the extracted variables.

   ```javascript
   const result = completion.choices[0].message["content"];
   const variables = JSON.parse(result);
   return variables;
   ```

#### Example Output

Given the input sentence `"Transfer 100 USDT from Holsky to Fuji in exchange for USDC."`, the function might return:
```json
{
  "sourceToken": "USDT",
  "sourceChain": "Holsky",
  "desToken": "USDC",
  "desChain": "Fuji",
  "amount": "100"
}
```






