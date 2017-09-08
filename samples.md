# List of Azure samples

## [CoinValue (Azure Functions, Serverless computing)](https://github.com/lbugnion/sample-azure-coinvalue)

This sample shows two Azure Functions:

- A **Timer Triggered** function used to save bitcoin value every hour to an Azure Table
- An **HTTP Triggered** function used to get the current bitcoin value as well as the current trend (up, flat, down) calculated over the last 10 samples.

In addition you will find Xamarin and Windows applications connecting to the HTTP Triggered function and displaying the result.