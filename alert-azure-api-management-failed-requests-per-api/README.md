# Alert on Azure API Management Failures for a specific API

[![Deploy to Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fcoditeu%2Fazure-code-snippets%2Fmaster%2Falert-azure-api-management-failed-requests-per-api%2Fazuredeploy.json)

## How does it work?
In certain scenarios you have to create alerts when failed requests are happening for a given Azure API Management API.

Luckily, Azure Application Insights tracks all requests which have all the required information.

As there are no built-in metrics on which you can alert, you can use a custom log query:
```
requests
| where  resultCode  matches regex "4[0-9]{2}|5[0-9]{2}"
| where  customDimensions["API Name"] == "<you api name>"
| project name
```
