# Lookup the IP Address for Azure API Management Instance

## How does it work?
In certain automated deployment (DevOps) scenarios, for example when you want to whitelist traffic from APIM only, you have the deployed resources (for example Logic Apps) automatically configured with the IP Address of the APIM Instance. This avoids having to manually reconfigure the IP address into all the required resources after deployment.

Luckily, the Azure Resource Manager provides a function that can be used from ARM templates to lookup (or "reference") details from other resources during deployment.

The **`reference()`** function allows you to retrieve the resource in the form of a JSON object and allows you to pick the specific properties that you are interested in:
```
reference(resourceId(variables('apimResourceGroup'),'Microsoft.ApiManagement/service/',variables('apimServiceName')),'2019-12-01','Full').properties.publicIPAddresses[0]
```

A cutdown response JSON object would look similar to the following (many other details omitted for clarity):
```JSON
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

> You can use Rest API (or Azure Resource Explorer) to retrieve and inspect the JSON response object to see which properties are available
> `GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>`

An example of what this would look like in practice (for example: setting access control on a Logic App to whitelist the IP address) would be as follows:
```JSON
"accessControl": {
  "triggers": {
    "allowedCallerIpAddresses": [
      {
        "addressRange": "[concat(reference(resourceId(variables('apimResourceGroup'),'Microsoft.ApiManagement/service/',variables('apimServiceName')),'2019-12-01','Full').properties.publicIPAddresses[0],'/30')]"
      }
    ]
  },
  "actions": {
    "allowedCallerIpAddresses": []
  }
}
```
> In the example above the Logic App expects a CIDR block or IP Address range which is why the "/30" subnet mask has been concatenated to the retrieved IP address.