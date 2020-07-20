---
title: Azure 虛擬機器認證 - Azure Marketplace
description: 了解如何在商業 Marketplace 中測試並提交虛擬機器供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: d3b89945c077b9c26bab1709bd6d1def20959e33
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110040"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Azure 虛擬機器 (VM) 映像認證

此文章描述如何在商業 Marketplace 中測試並提交虛擬機器 (VM) 映像，以確保其符合最新的 Azure Marketplace 發佈需求。

提交您的 VM 供應項目之前，請先完成下列步驟：

1. 建立並部署憑證。
2. 使用一般化映像部署 Azure VM。
3. 執行驗證。

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>建立並部署 Azure Key Vault 的憑證

此節描述如何建立並部署設定 Windows 遠端管理 (WinRM) 和 Azure 託管虛擬機器連線所需的自我簽署憑證。

### <a name="create-certificates-for-azure-key-vault"></a>建立 Azure Key Vault 的憑證

此程序由三個步驟組成：

1. 建立安全性憑證。
2. 建立 Azure Key Vault 來儲存憑證。
3. 將憑證儲存到金鑰保存庫。

您可以針對這項工作使用新的或現有的 Azure 資源群組。

#### <a name="create-the-security-certificate"></a>建立安全性憑證

編輯並執行下列 Azure PowerShell 指令碼，以便在本機資料夾中建立憑證檔案 (.pfx)。 取代下表中所示參數的值。

| **參數** | **說明** |
| --- | --- |
| $certroopath | 儲存 .pfx 檔案所在的本機資料夾。 |
| $location | 其中一個 Azure 標準地理位置。 |
| $vmName | 已規劃的 Azure 虛擬機器名稱。 |
| $certname | 憑證的名稱；必須符合已規劃 VM 的完整網域名稱。 |
| $certpassword | 憑證的密碼，必須符合用於已規劃 VM 的密碼。 |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> 在這些步驟期間保持相同的 Azure PowerShell 主控台工作階段開啟並執行中，以保留各種參數的值。

> [!WARNING]
> 如果您儲存此指令碼，僅能將其儲存在安全的位置，因為其包含安全性資訊 (密碼)。

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>建立 Azure Key Vault 來儲存憑證

將以下範本的內容複製至本機電腦上的檔案。 在以下的範例指令碼中，此資源是 `C:\certLocation\keyvault.json`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

編輯並執行下列 Azure PowerShell 指令碼以建立 Azure Key Vault 和相關聯的資源群組。 取代下表中所示參數的值

| **參數** | **說明** |
| --- | --- |
| $postfix | 附加至部署識別碼的隨機數字字串。 |
| $rgName | 要建立的 Azure 資源群組 (RG) 名稱。 |
| $location | 其中一個 Azure 標準地理位置。 |
| $kvTemplateJson | 檔案 (keyvault.json) 的路徑，其中包含金鑰保存庫的 Resource Manager 範本。 |
| $kvname | 新金鑰保存庫的名稱。|
|   |   |

```PowerShell
    # Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>將憑證儲存到金鑰保存庫

使用下列指令碼，將包含在 .pfx 檔案中的憑證儲存到新的金鑰保存庫：

```PowerShell
     $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>使用一般化映像部署 Azure VM

此節描述如何部署一般化 VHD 映像，以建立新的 Azure VM 資源。 針對此程序，我們將使用提供的 Azure Resource Manager 範本和 Azure PowerShell 指令碼。

### <a name="prepare-an-azure-resource-manager-template"></a>準備 Azure Resource Manager 範本

將適用於 VHD 部署的下列 Azure Resource Manager 範本複製到名為 VHDtoImage.json 的本機檔案。 下一個指令碼會要求本機電腦上的位置使用此 JSON。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

編輯此檔案以提供下列參數的值：

| **參數** | **說明** |
| --- | --- |
| resourceGroupName | 現有的 Azure 資源群組名稱。 通常會使用與您金鑰保存庫相同的 RG。 |
| TemplateFile | 檔案 VHDtoImage.json 的完整路徑名稱。 |
| userStorageAccountName | 儲存體帳戶的名稱。 |
| sNameForPublicIP | 公用 IP 的 DNS 名稱；必須小寫。 |
| subscriptionId | Azure 訂用帳戶識別碼。 |
| Location | 資源群組的標準 Azure 地理位置。 |
| vmName | 虛擬機器的名稱。 |
| vaultName | 金鑰保存庫的名稱。 |
| vaultResourceGroup | 金鑰保存庫的資源群組。 |
| certificateUrl | 憑證的網址 (URL)，包括儲存於金鑰保存庫的版本，例如：`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`。 |
| vhdUrl | 虛擬硬碟的網址。 |
| vmSize | 虛擬機器執行個體的大小。 |
| publicIPAddressName | 公用 IP 位址的名稱。 |
| virtualNetworkName | 虛擬網路的名稱。 |
| nicName | 適用於虛擬網路的網路介面卡名稱。 |
| adminUserName | 系統管理員帳戶的使用者名稱。 |
| adminPassword | 系統管理員密碼。 |
|   |   |

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

複製並編輯下列指令碼，以提供 `$storageaccount` 和 `$vhdUrl` 變數的值。 執行它，從您現有的通用 VHD 建立 Azure VM 資源。

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>執行驗證

對已部署的映像執行驗證有兩種方式：

- 使用適用於 Azure 認證的認證測試工具
- 使用自我測試 API

### <a name="download-and-run-the-certification-test-tool"></a>下載並執行認證測試工具

Azure 認證的認證測試工具是在本機 Windows 電腦上執行，但可測試 Azure 型 Windows 或 Linux VM。 其會驗證您的使用者 VM 映像是否可以搭配 Microsoft Azure 使用，以及您 VHD 的準備工作是否有依循指導方針並達到要求。 此工具會輸出相容性報告，您要將此報告上傳至合作夥伴中心入口網站以要求 VM 認證。

1. 下載並安裝最新版本的 [Azure 認證的認證測試工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 開啟認證工具，然後選取 [啟動新測試]。
3. 從 [測試資訊] 畫面，輸入測試回合的**測試名稱**。
4. 選取您 VM 適用的 [平台]，亦即 Windows Server 或 Linux。 所選的平台會影響其餘的選項。
5. 如果您的 VM 使用此資料庫服務，請選取 [Azure SQL Database 測試] 核取方塊。

### <a name="connect-the-certification-tool-to-a-vm-image"></a>將認證工具連線至 VM 映像

此工具會透過 [Azure PowerShell](https://docs.microsoft.com/powershell/) 連線至以 Windows 為基礎的 VM，並透過 [SSH.Net](https://www.ssh.com/ssh/protocol/) 連線至 Linux VM。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>將認證工具連線至 Linux VM 映像

1. 選取 [SSH 驗證] 模式：密碼驗證或金鑰檔案驗證。
2. 如果使用以密碼為基礎的驗證，請輸入 [VM DNS 名稱]、[使用者名稱] 和 [密碼]。 您也可以變更預設的 [SSH 連接埠] 號碼。

    ![Azure 認證的測試工具，Linux VM 映像的密碼驗證](media/avm-cert2.png)

3. 如果使用以金鑰檔案為基礎的驗證，請輸入**虛擬機器 DNS 名稱**、**使用者名稱**和**私密金鑰**位置等值。 您也可以加上 [複雜密碼] 或是變更預設的 [SSH 連接埠] 號碼。

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**將認證工具連接到 Windows 型 VM 映像**

1. 輸入完整的 [VM DNS 名稱] (例如 MyVMName.Cloudapp.net)。
2. 輸入 [使用者名稱] 和 [密碼] 的值。

    ![Azure 認證的測試工具，Windows 型 VM 映像的密碼驗證](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>執行認證測試

在認證工具中提供適用於您 VM 映像的參數值後，請選取 [測試連線]，以建立與 VM 的有效連線。 驗證連線後，選取 [下一步] 啟動測試。 測試完成時，測試結果會顯示在表格中。 [狀態] 欄會針對每個測試顯示 (通過/失敗/警告)。 如果任何一項測試失敗，您的映像即_未_通過認證。 在此情況下，請檢閱需求與失敗訊息、進行建議的變更，然後再執行測試一次。

自動化測試完成之後，請在 [問卷] 畫面的兩個索引標籤 ([一般評估] 和 [核心自訂]) 上，提供 VM 映像的其他相關資訊，然後選取 [下一步]。

最後一個畫面可讓您提供其他資訊，例如 Linux VM 映像的 SSH 存取資訊，以及尋找例外狀況時任何評量失敗的說明。

最後，選取 [產生報告]，以下載已執行測試案例的測試結果和記錄檔，以及問卷調查的答案。 將結果儲存在與您的 VHD 相同的容器中。

## <a name="next-step"></a>後續步驟

- [常見 SAS URL 問題和修正程式](common-sas-uri-issues.md)
