# azure-cli
Getting started with the Azure CLI 2.0

# Install the CLI
Available on macOS, Linux, and Windows.
https://docs.microsoft.com/en-us/cli/azure/install-azure-cli

## Linux
Requires GCC and development libs to be installed or you may see the following error:

    error: command 'gcc' failed with exit status 1

To fix that on a CentOS/RHEL system, run the following:

    yum install -y gcc libffi-devel python-devel openssl-devel

## Mac 
No installation issues to report

# Using the CLI

## Getting Started
https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli

## Create a single Azure SQL database using the Azure CLI
https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-cli

## Samples
https://github.com/Azure/azure-cli-samples/blob/master/compute/compute.md
https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-cli

## Commone Azure CLI Commands on Linux and Mac (old CLI)
https://docs.microsoft.com/en-us/azure/virtual-machines/linux/cli-manage?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json


## Logging in
Log in using the `az login` command.  Running the commnad returns a code and a URL: https://aka.ms/devicelogin.  Enter the code in the form and submit.  

```
$ az login
To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code BF8JNP3Z5 to authenticate.
```

If you are not already logged in, you'll be prompted to log into your Azure account.  Once you are successfully logged in, you can go back to the terminal.  You should see output that shows you are successfully logged in:

```
$ az login
To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code BF8JNP3Z5 to authenticate.
[
  {
    "cloudName": "AzureCloud",
    "id": "2b97ffe3-a4ad-4f78-8d71-3494bf3c731d",
    "isDefault": true,
    "name": "Developer Program Benefit",
    "state": "Enabled",
    "tenantId": "35aa92d1-22bb-4938-8211-b307923fd69f",
    "user": {
      "name": "aws2@managedkaos.com",
      "type": "user"
    }
  }
]
```

After successfully logging in, you can start using various commands to manage resources in Azure.  Let's take a look at managing virtual machines, storage, and databases.

## Create a Resource Group
A resource group is a container for resources that should be logically groupped together.  Like a flight of instances, their associated databases, and storage.  It can also be considered like a project for a web application or something similar.

Get the regions
`az account list-locations | jq '.[].name' | sort`

Azure is generally available in 34 regions around the world, with plans announced for 4 additional regions. 
Use the region that best suits your location.

Let's create a group to hold the resources needed to create a web application running on wordpress.

```
$ az group create -n wordpress_resource_group -l westus2
{
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/wordpress",
  "location": "westus2",
  "managedBy": null,
  "name": "wordpress",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

To verify, run `az group list` and check the output for the group you juse created.

## Create a VM

Not all VM images and sizes are avaialable in all regions. (http://www.florisvanderploeg.com/available-vm-sizes-and-images-in-azure-per-location/)

Get a list of VM images for the region you are building in.
```
$ az vm image list | grep urnAlias
14:36 $ az vm image list | grep urnAlias
You are viewing an offline list of images, use --all to retrieve an up-to-date list
    "urnAlias": "Win2016Datacenter",
    "urnAlias": "Win2012R2Datacenter",
    "urnAlias": "Win2008R2SP1",
    "urnAlias": "Win2012Datacenter",
    "urnAlias": "UbuntuLTS",
    "urnAlias": "CentOS",
    "urnAlias": "openSUSE-Leap",
    "urnAlias": "RHEL",
    "urnAlias": "SLES",
    "urnAlias": "Debian",
    "urnAlias": "CoreOS",
```
Use --all to get an extended list of available images.  Running with --all may take several minutes to complete as the CLI tool will be making an extended query to get all available images.  Also note that the extended listing does not include the urnAlias field.  If you're trying to parse out the image names, simply grep for urn.  

For our exploration, using the default listing should suffice.


Note VM naming conventions:
```
Linux host name cannot exceed 64 characters in length or contain the following characters: ` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\ | ; : ' \" , < > / ?
```

Create a CentOS VM in our development group:
```
$ az vm create --name devserver01 --resource-group dev_group --image CentOS --data-disk-sizes-gb 10
{
  "fqdns": "",
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/dev_group/providers/Microsoft.Compute/virtualMachines/devserver01",
  "location": "westus2",
  "macAddress": "00-0D-3A-F9-12-20",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.xyz.xyz.xyz", # IP Address masked as xyz
  "resourceGroup": "dev_group"
}
```
If the image is not available in your region, you may see an error like the following:
```
At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details. {
  "error": {
    "code": "SkuNotAvailable",
    "message": "The requested tier for resource '/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/development_group/providers/Microsoft.Compute/virtualMachines/devserver01' is currently not available in location 'westus' for subscription '2b97ffe3-a4ad-4f78-8d71-3494bf3c731d'. Please try another tier or deploy to a different location."
  }
}  Correlation ID: eb474e89-12e9-4c59-a749-4cb6148c6462
```

One of the cool things about creating Azure VMs from the command line is that access is set up with your current SSH keys.  If you have keys in ~/.ssh/id_rsa, VMs will be created with access using that key.  You can also use the --generate-keys switch to have the command create keys for you.  Note that a user is also created for the account running the az command.  That account will have admin priveleiges and will be able to run elevated commands with `sudo` as needed.

simply log in using the IP address returned by the az command:

`ssh 52.183.82.247`

Once you're logged in, update the VM as needed:

```
sudo apt-get update
sudo apt-get upgrade -y
```

# Storage
https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli
https://docs.microsoft.com/en-us/cli/azure/storage
```
echo "Creating the container..."
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

echo "Done"
```

# Create a Database

## Errors
- Location is required: `az sql server create: error: argument --location/-l is required`
- Server name must be unique `The name 'devsql01.database.windows.net' already exists. Choose a different name.`

The database
```
az sql server create --name devsql-$(date +%s) --resource-group dev_group --location westus2 --admin-user devamin --admin-password SRmPjh83wH4qeLUe
  1 {
{
  "administratorLogin": "devamin",
  "administratorLoginPassword": "SRmPjh83wH4qeLUe",
  "externalAdministratorLogin": null,
  "externalAdministratorSid": null,
  "fullyQualifiedDomainName": "devsql-1491520354.database.windows.net",
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/dev_group/providers/Microsoft.Sql/servers/devsql-1491520354",
  "kind": "v12.0",
  "location": "West US 2",
  "name": "devsql-1491520354",
  "resourceGroup": "dev_group",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

The firewall
```
az sql server firewall-rule create --resource-group dev_group --server devsql-1491520354 -n AllowYourIp --start-ip-address 52.183.81.57 --end-ip-address 52.183.81.57
{
  "endIpAddress": "52.183.81.57",
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/dev_group/providers/Microsoft.Sql/servers/devsql-1491520354/firewallRules/AllowYourIp",
  "kind": "v12.0",
  "location": "West US 2",
  "name": "AllowYourIp",
  "resourceGroup": "dev_group",
  "startIpAddress": "52.183.81.57",
  "type": "Microsoft.Sql/servers/firewallRules"
}
```
The database
```
az sql db create --resource-group dev_group --server devsql-1491520354 --name dev_db --sample-name AdventureWorksLT --service-objective S0
{
  "collation": "SQL_Latin1_General_CP1_CI_AS",
  "containmentState": 2,
  "createMode": null,
  "creationDate": "2017-04-06T23:22:37.827000+00:00",
  "currentServiceObjectiveId": "f1173c43-91bd-4aaa-973c-54e79e15235b",
  "databaseId": "61befa5a-6a90-4eae-9e05-d326f1153581",
  "defaultSecondaryLocation": "West Central US",
  "earliestRestoreDate": "2017-04-06T23:33:11.080000+00:00",
  "edition": "Standard",
  "elasticPoolName": null,
  "failoverGroupId": null,
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/dev_group/providers/Microsoft.Sql/servers/devsql-1491520354/databases/dev_db",
  "kind": "v12.0,user",
  "location": "West US 2",
  "maxSizeBytes": "268435456000",
  "name": "dev_db",
  "readScale": "Disabled",
  "recommendedIndex": null,
  "requestedServiceObjectiveId": "f1173c43-91bd-4aaa-973c-54e79e15235b",
  "requestedServiceObjectiveName": "S0",
  "resourceGroup": "dev_group",
  "restorePointInTime": null,
  "sampleName": null,
  "schemas": null,
  "serviceLevelObjective": "S0",
  "serviceTierAdvisors": null,
  "sourceDatabaseId": null,
  "status": "Online",
  "tags": null,
  "transparentDataEncryption": null,
  "type": "Microsoft.Sql/servers/databases"
}
```

# Connect to the Database from the VM
Install the SQL Server command-line tools (will add to the VM Creation section)
https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-connect-and-query-sqlcmd

Connect to the server
https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-connect-and-query-sqlcmd

# Connect to the DB using Python
https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-python

# Python code for connecting to NYT API
Get an API Key
    http://developer.nytimes.com/signup

# Connect to the DB using .NET Core SDK

Install .NET Core SDK
    https://www.microsoft.com/net/core#linuxcentos

Example
    https://github.com/Microsoft/sql-server-samples/releases/tag/iot-smart-grid-v1.0

Another example
    https://blogs.msdn.microsoft.com/cdndevs/2015/03/11/python-and-data-sql-server-as-a-data-source-for-python-applications/

```
import json
import requests

url = "https://api.nytimes.com/svc/archive/v1/2017/5.json"
headers = {}
params = {"api-key":"................................"}


response = requests.request("GET", url, headers=headers, params=params)
data = response.json()
for doc in data['response']['docs']:
    for keywords in doc['keywords']:
        if 'Microsoft' in keywords['value']:
            print doc['headline']['main']
```

# Remove the Resource Group (and all associated resources!)
az group delete --name myResourceGroup

