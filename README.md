# azure-cli
Getting started with the Azure CLI

# Install the CLI
Available on macOS, Linux, and Windows.
https://docs.microsoft.com/en-us/cli/azure/install-azure-cli

## Linux
Requires GCC and development libs to be installed to be installed or you may see the following error:

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

