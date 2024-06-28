y## `What is Router Nitro ?`

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

# `Understanding the Code`

## Setting up the project

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

## Getting OpenAI API Key

Visit [https://openai.com/index/openai-api/](url) and click on ***Explore API*** . Make sure to create an account on OpenAI before proceeding with the step.

It will redirect you to [https://openai.com/api/](url) . Click on ***Start Building*** .

It will redirect you to OpenAI Platform . Click on **API Keys** option in the left Side Bar and create your new API Key

Copy the API Key somewhere as you need it afterwards

## Imports

Import the downloaded libararies by 

```
import OpenAI from "openai";
import axios from "axios"
import { ethers } from 'ethers';
```

## Extract Variable Function


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

```Note: For sake for simplicity we are limiting this application for transferring AFTT Tokens between Amoy, Fuji and Holsky``` 

## Getting the Quote

The Nitro enables you to interact with the Voyager contract and initiate cross-chain token transfers. The first step in this process is to request a quote, which provides you with essential details about the proposed token transfer.

To request a quote, follow these steps:

1. Define the PATH_FINDER_API_URL: Set the PATH_FINDER_API_URL variable to the URL of the Pathfinder API for the Voyager testnet. This is where you will send your quote request.

   ```javascript
   const PATH_FINDER_API_URL = "https://api.pf.testnet.routerprotocol.com/api"
   ```

2. Create the `getQuote` Function: This function handles the quote request. It uses the `axios` library to make an HTTP GET request to the Voyager Pathfinder API.

   ```
   const getQuote = async (params) => {
		const endpoint = "v2/quote"
		const quoteUrl = `${PATH_FINDER_API_URL}/${endpoint}`
	
		console.log(quoteUrl)
	
		try {
			const res = await axios.get(quoteUrl, { params })
			return res.data;
		} catch (e) {
			console.error(`Fetching quote data from pathfinder: ${e}`)
		}    
	}
   ```

3. Call the `getQuote` Function: Use this function to request a quote by passing appropriate parameters.In this repository , this function is called using a a button.

   ```javascript
  
   const quoteParams = {
				'fromTokenAddress': source_token_address,
				'toTokenAddress': destination_token_address,
				'amount': amount,
				'fromTokenChainId': source_chain_id,
				'toTokenChainId': destination_chain_id, 
		
				'widgetId': 0,
			}
   
   const quoteData = await getQuote(quoteParams);
   console.log("Quote Data:", quoteData);
   ```
These parameters define the details of the token transfer you wish to execute. Let's break down what each parameter represents:

- `'fromTokenAddress'`: This should specify the address of the token you want to transfer from (the source token).

- `'toTokenAddress'`: Provide the address of the token you want to transfer to (the destination token).

- `'amount'`: Set the amount of the token you wish to transfer.

- `'fromTokenChainId'`: This parameter represents the chain ID of the source blockchain. In this case, it's set to "80001."

- `'toTokenChainId'`: Similarly, this parameter specifies the chain ID of the destination blockchain, which, in this example, is "43113" (Fuji).

- `'widgetId'`: This parameter is used to identify the widget responsible for the transfer. You'll typically need to obtain a unique widget ID through contact with the Voyager team, often via Telegram or other means. For now, let's keep it as 0.

With these parameters, you can now call the `getQuote` function with this `params` object to initiate a quote request for your specific token transfer. 


### Response
The `getQuote` function returns the quote data, which typically includes details about the token transfer, such as source and destination chains, token amount, fees, and other relevant information.Click the **Get Quote** button and go to console to see the quote data printed on console.

## Check and Set Allowance

In Step 2 of using Nitro, you'll verify and configure the allowance for token transfers. This process allows Router's swap or transfer contract to safely move tokens on your behalf between blockchain networks.

```javascript

import { ethers, Contract } from 'ethers'

// ERC20 Contract ABI for "Approve" and "Allowance" functions
const erc20_abi = [
    {
        "name": "approve",
        "inputs": [
            {
                "internalType": "address",
                "name": "spender",
                "type": "address"
            },
            {
                "internalType": "uint256",
                "name": "amount",
                "type": "uint256"
            }
        ],
        "outputs": [
            {
                "internalType": "bool",
                "name": "",
                "type": "bool"
            }
        ],
        "stateMutability": "nonpayable",
        "type": "function"
    },
    {
        "name": "allowance",
        "inputs": [
            {
                "internalType": "address",
                "name": "owner",
                "type": "address"
            },
            {
                "internalType": "address",
                "name": "spender",
                "type": "address"
            }
        ],
        "outputs": [
            {
                "internalType": "uint256",
                "name": "",
                "type": "uint256"
            }
        ],
        "stateMutability": "view",
        "type": "function"
    }
];

// Fetch the current allowance and update if needed

const checkAndSetAllowance = async (wallet, tokenAddress, approvalAddress, amount) => {
		
		if (tokenAddress === ethers.constants.AddressZero) {
			return
		}
	
		// Using the provided token address and the ERC20 ABI, we create an instance of the ERC20 contract.
		const erc20 = new ethers.Contract(tokenAddress, erc20_abi, wallet);
		const allowance = await erc20.allowance(await wallet.getAddress(), approvalAddress);
		if (allowance.lt(amount)) {
			const approveTx = await erc20.approve(approvalAddress, amount, {gasPrice: await wallet.provider.getGasPrice()});
			try {
				await approveTx.wait();
				console.log(`Transaction mined succesfully: ${approveTx.hash}`)
			}
			catch (error) {
				console.log(`Transaction failed with error: ${error}`)
			}
		}
		else{

			console.log("enough allowance")
			alert("enough allowance")
		}
	}
```
### 1. Define ERC20 Contract ABI

We begin by defining the ABI (Application Binary Interface) for ERC20 tokens, specifically focusing on the "approve" and "allowance" functions. This ABI is essential for interacting with ERC20 token contracts.

### 2. The `checkAndSetAllowance` Function

This function checks your current allowance and, if necessary, sets a new allowance. It first checks if the token is the native token (ETH), in which case no approval is needed.

### 3. Creating an ERC20 Contract

Using the provided token address and the ERC20 ABI, we create an instance of the ERC20 contract. This contract represents the token you want to set an allowance for.

### 4. Checking Current Allowance

We retrieve your current allowance for the token. The allowance is the maximum amount the Voyager system (or any other address) can withdraw from your wallet.

### 5. Setting the Allowance

If the current allowance is less than the desired amount, we proceed to set a new allowance. We initiate an approval transaction to the ERC20 contract, granting permission to Router's swap or transfer contract to withdraw tokens on your behalf.


## Executing the Transaction

This function is responsible for actually executing the transaction. It takes in the following parameters

- **`params`**: Parameters required for the transaction, which should include the source and destination token addresses, slippage tolerance, sender and receiver addresses, and the widget ID.
- **`quoteData`**: Quote data obtained from Step 1.


```javascript
  const getTransaction = async (params, quoteData) => {
		const endpoint = "v2/transaction"
		const txDataUrl = `${PATH_FINDER_API_URL}/${endpoint}`
	
		console.log(txDataUrl)
	
		try {
			const res = await axios.post(txDataUrl, {
				...quoteData,
				slippageTolerance: 0.5,
				senderAddress: account,
				receiverAddress: account,
			})
			return res.data;
		} catch (e) {
			console.error(`Fetching tx data from pathfinder: ${e}`)
		}    
	}

```

## Create an Input Field 

![Screenshot 2024-06-28 at 6 56 24 PM](https://github.com/router-resources/NitroAIDApp/assets/124175970/081cb1f3-aef5-4ec0-aa63-4508e77bdb5d)

Create an Input Field to enter the prompt and the extract the vaiables from the prompt .

Create the following objects.

  ```
let chainID={

    amoy:'80002',
    fuji:'43113',
    holsky:'17000'
  
  }
  
  let tokenAddress={
  
    aftt:{
      amoy:'0xBAD6e1AbE5EbEae8a123ef14AcA7024D3F8c45fb',
      fuji:'0x69dc97bb33e9030533ca2006ab4cef67f4db4125',
      holsky:'0x5c2c6ab36a6e4e160fb9c529e164b7781f7d255f'
    }
  }
```

When the variables are extracted , the variables are mapped to the fields in the above objects to get the data in terms of Token Addresses and ChainID's

## Submit Button

![Screenshot 2024-06-28 at 6 57 33 PM](https://github.com/router-resources/NitroAIDApp/assets/124175970/8ec213f9-623f-4281-bae2-2c6758a293b4)

Next , we need to create a button on click of which the following tasks take place .

### 1. Extracting the Variables**
### 2. Getting the Quote**
### 3. Checking and Setting Allowance**
### 4. Executing the Transaction**

```javascript
 <button  role="button" style={{width:'10em',height:'3em',backgroundColor:'white',borderRadius:'2em',borderColor:'black'}} onClick={async ()=>{

        //Extracting Variables
        const variables=await extractVariables(sentence)
          if (variables) {
           
            const { sourceToken, sourceChain, desToken,desChain, amount } = variables;
            let resObj={sourceToken:tokenAddress[`${sourceToken.toLowerCase()}`][`${sourceChain.toLowerCase()}`],sourceChain:chainID[`${sourceChain.toLowerCase()}`],
            desToken:tokenAddress[`${desToken.toLowerCase()}`][`${desChain.toLowerCase()}`],desChain:chainID[`${desChain.toLowerCase()}`],amount:amount}
            console.log(resObj)

            //Getting the Quote
            const params ={
              'fromTokenAddress': resObj.sourceToken,
              'toTokenAddress': resObj.desToken,
              'amount': parseFloat(amount)*Math.pow(10,18),
              'fromTokenChainId': resObj.sourceChain,
              'toTokenChainId': resObj.desChain, 
              'partnerId': "0",
              
            }
            
             const quote=await getQuote(params)
              setQuoteData(quote)
              alert(quote.allowanceTo)
              console.log(quote)

              //Checking and Setting Allowance
              if(window.ethereum) {
                console.log('detected');
            
                try {
                const accounts = await window.ethereum.request({
                  method: "eth_requestAccounts",
                });
          
                console.log(accounts[0])
                const provider = new ethers.providers.Web3Provider(window.ethereum);
                const signer = provider.getSigner();
          
                
          
                await checkAndSetAllowance(
                  signer,
                 resObj.sourceToken, 
                  quote.allowanceTo, 
                  ethers.constants.MaxUint256 
                );
                
                //Executing the Transaction
                const txResponse = await getTransaction({
                  'fromTokenAddress': resObj.sourceToken,
                  'toTokenAddress': resObj.desToken,
                  'fromTokenChainId': resObj.sourceChain,
                  'toTokenChainId': resObj.desChain, 
                  'widgetId': 0, 
                }, quote); 

                const tx = await signer.sendTransaction(txResponse.txn)
                try {
                  await tx.wait();
                  console.log(`Transaction mined successfully: ${tx.hash}`)
                  alert(`Transaction mined successfully: ${tx.hash}`)
                  
                }
                catch (error) {
                  console.log(`Transaction failed with error: ${error}`)
                }
               
                }
                catch(err) {
                console.log(err)
                }
              }
          }

        
       
      }}>Submit</button>
```









