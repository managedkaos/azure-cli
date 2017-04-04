# azure-cli
Getting started with the Azure CLI

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

## Samples
https://github.com/Azure/azure-cli-samples/blob/master/compute/compute.md

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

let's create a group to hold the resources needed to create a web application running on wordpress.

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

the location:
Azure is generally available in 34 regions around the world, with plans announced for 4 additional regions. 


## Create a VM
Note VM naming conventions:
```
Linux host name cannot exceed 64 characters in length or contain the following characters: ` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\ | ; : ' \" , < > / ?
```

Get a list of VM images
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

```
$ az vm create -n wordpressvm -g wordpress_resource_group --image UbuntuLTS --data-disk-sizes-gb 10
{
  "fqdns": "",
  "id": "/subscriptions/2b97ffe3-a4ad-4f78-8d71-3494bf3c731d/resourceGroups/wordpress_resource_group/providers/Microsoft.Compute/virtualMachines/wordpressvm",
  "location": "westus2",
  "macAddress": "00-0D-3A-F7-D1-F8",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "xx.xx.xx.xx",
  "resourceGroup": "wordpress_resource_group"
}

```

One of the cool things about creating Azure VMs from the command line is that access is set up with your current SSH keys.  If you have keys in ~/.ssh/id_rsa, VMs will be created with access using that key.  You can also use the --generate-keys switch to have the command create keys for you.  Note that a user is also created for the account running the az command.  That account will have admin priveleiges and will be able to run elevated commands with `sudo` as needed.

simply log in using the IP address returned by the az command:

`ssh 52.183.82.247`

Once you're logged in, update the VM as needed:

```
sudo apt-get update
sudo apt-get upgrade -y
```

# Create a Database



