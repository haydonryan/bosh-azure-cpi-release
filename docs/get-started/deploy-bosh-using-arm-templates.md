# Deploy BOSH on Azure using ARM templates

<a name="bosh_setup"></a>
# 1 Setup a Development Environment on Azure 

## 1.1 Prepare Azure Resources

Here we’ll create the following Azure resources that’s required for deploying BOSH and Cloud Foundry:

* An Azure Storage Account
* Two reserved IPs
* A Virtual Network
* A Virtual Machine with a public IP

The [**bosh-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/bosh-setup) ARM template can help you to deploy all the above resources on Azure. Just click the button below with the following parameters:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fbosh-setup%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

| Name | Description | Default Value |
|:---------------:|:----- |:----- |
| newStorageAccountName | **Unique DNS Name** for the Storage Account where the Virtual Machine disks will be placed. **It must be between 3 and 24 characters in length and use numbers and lower-case letters only.** | |
| virtualNetworkName | name of the virtual network | boshvnet-crp |
| subnetNameForBosh  | name of the subnet for Bosh | Bosh |
| subnetNameForCloudFoundry | name of the subnet for CloudFoundy | CloudFoundry |
| vmName | Name of Virtual Machine | |
| vmSize | Size of the Virtual Machine | Standard_D1 |
| adminUsername | Username for the Virtual Machines. **Never use root as the adminUsername**. | |
| adminPassword | Password for the Virtual Machine | |
| enableDNSOnDevbox | A default DNS will be setup in the dev-box if it is true. **If the dev-box reboots, its public IP address may change. You need to manually update it in /etc/bind/cf.com.wan.** | true |
| tenantID | ID of the tenant | TENANT-ID |
| clientID | ID of the client | CLIENT-ID |
| clientSecret | secret of the client | CLIENT-SECRET |

>**NOTE:**
  * Currently BOSH can be only deployed from a Virtual Machine in the same VNET on Azure.
  * The default type of Azue storage account is "Standard_GRS" (Geo-redundant storage). For a list of available Azure storage accounts, their capacities and prices, check [**HERE**](http://azure.microsoft.com/en-us/pricing/details/storage/). Please note Standard_ZRS account cannot be changed to another account type later, and the other account types cannot be changed to Standard_ZRS. The same goes for Premium_LRS accounts.

## 1.2 Login your dev-box

After the deployment succeeded, you can find the resource group with the name you specified on Azure Portal. The VM in the resource group is your dev-box. You can find the public IP address of the dev-box.

Login your dev-box with the ARM template parameter `adminUsername` and `adminPassword`.

```
ssh <adminUsername>@<public-IP-address>
```

After you login, you can check `~/install.log` to determine the status of the deployment. When the deployment succeeds, you will find **Finish** at the end of the log file and no **ERROR** message in it.

# 2 Deploy BOSH

## 2.1 Configure

The ARM template pre-creates the deployment manifest file `bosh.yml` in your home directory.

If you leave **TENANT-ID**, **CLIENT-ID**, **CLIENT-SECRET** at default values in [section 1](#bosh_setup), you need to update these three properties in `~/bosh.yml`.

## 2.2 Deploy

Run the following commands in your home directory to deploy bosh:

```
./deploy_bosh.sh
```

>**NOTE:**
  * Never use root to perform these steps.
  * More verbose logs are written to `~/run.log`.
  * If you hit any issue, please see [**troubleshooting**](./troubleshooting.md), [**known issues**](./known-issues.md) and [**migration**](./migration.md). If it does not work, you can file an issue [**HERE**](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues).
