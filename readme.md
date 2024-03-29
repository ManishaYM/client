# Azure Function Using Command Line

## Setup on [Visual Studio Online](https://online.visualstudio.com/)

* [Reference](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-azure-function-azure-cli)
* Install [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local#v2):
```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install azure-functions-core-tools -y
```

## Development

* Clone the repo
* Modify the code

## Steps to create and deploy from scratch

* Create app project: `func init HelloWorld`
* Create HttpTrigger: `cd HelloWorld && func new --name HelloWorld --template "HttpTrigger"`
* Select `dotnet` from the options presented and continue to the next step(s)
* Login to az cli: `az login`
* Function deployment requires a resource group and a storage account:
```
namePrefix=authpoc
nameResourceGroup=${namePrefix}-resource-group
nameStorageAccount=${namePrefix}storage
location=centralus
functionName=trgos-hello-world
az group create --name $nameResourceGroup --location $location
az storage account create --name $nameStorageAccount --location $location --resource-group $nameResourceGroup
```
* Create function:
```
az functionapp create --resource-group $nameResourceGroup --consumption-plan-location $location \
--name $functionName --storage-account  $nameStorageAccount --runtime dotnet
```
* To configure [CORS](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#cors):
```
az functionapp cors remove -g $nameResourceGroup -n $functionName --allowed-origins

az functionapp cors add --name $functionName \
--resource-group $nameResourceGroup \
--allowed-origins "*"
```

## What's next

* Publish the function: `cd ./HelloWorld && func azure functionapp publish trgos-hello-world`
* [Access function logs](https://markheath.net/post/three-ways-view-error-logs-azure-functions)
* Advanced logging future [options](https://stackify.com/logging-azure-functions/)
* Connect using a browser: https://trgos-hello-world.azurewebsites.net/api/HelloWorld?name=John 