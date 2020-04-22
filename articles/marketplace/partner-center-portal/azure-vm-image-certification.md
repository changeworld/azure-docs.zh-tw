---
title: Azure 虛擬機器認證 - Azure 應用商店
description: 瞭解如何在商業市場中測試和提交虛擬機產品/服務。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731133"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Azure 虛擬機器 (VM) 映像認證

> [!NOTE]
> 我們將 Azure VM 產品的管理從雲端合作夥伴門戶遷移到合作夥伴中心。 在遷移產品/服務之前,請繼續按照雲合作夥伴門戶中[為 Azure 密鑰保管庫創建證書](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert)的說明進行管理產品/服務。

本文介紹如何在商業市場中測試和提交虛擬機 (VM) 映射,以確保它滿足最新的 Azure 應用商店發佈要求。

在提交 VM 產品/服務之前完成以下步驟:

1. 創建和部署證書。
2. 使用通用映射部署 Azure VM。
3. 運行驗證。

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>為 Azure 金鑰保存與部署憑證

本節介紹如何創建和部署將 Windows 遠端管理 (WinRM) 連接設置為 Azure 託管虛擬機器所需的自簽名證書。

### <a name="create-certificates-for-azure-key-vault"></a>建立 Azure Key Vault 的憑證

此程序由三個步驟組成：

1. 建立安全性憑證。
2. 創建 Azure 金鑰保存庫以儲存憑證。
3. 將證書儲存到金鑰保管庫。

您可以針對這項工作使用新的或現有的 Azure 資源群組。

#### <a name="create-the-security-certificate"></a>建立安全憑證

編輯並運行以下 Azure PowerShell 文稿,以在本地資料夾中創建證書檔 (.pfx)。 取代下表中顯示的參數的值。

| **參數** | **說明** |
| --- | --- |
| $certroopath | 將 .pfx 檔保存到的本地資料夾。 |
| $location | Azure 標準地理位置之一。 |
| $vmName | 計畫 Azure 虛擬機器的名稱。 |
| $certname | 證書的名稱;必須匹配計畫 VM 的完全限定功能變數名稱。 |
| $certpassword | 證書的密碼必須與計劃 VM 使用的密碼匹配。 |
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
> 在這些步驟期間保持相同的 Azure PowerShell 控制台會話打開並運行,以保留各種參數的值。

> [!WARNING]
> 如果保存此腳本,則僅將其保存在安全位置,因為它包含安全資訊(密碼)。

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>建立 Azure 金鑰保存的憑證

將下面的樣本內容複製到本地電腦上的檔案。 在下面的範例文稿中, 此資源`C:\certLocation\keyvault.json`為 。

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

編輯並運行以下 Azure PowerShell 文稿以建立 Azure 密鑰保管庫和相關資源群組。 取代下表中顯示的參數值

| **參數** | **說明** |
| --- | --- |
| $postfix | 附加到部署識別碼的隨機數位字串。 |
| $rgName | 要建立的 Azure 資源群組 (RG) 名稱。 |
| $location | Azure 標準地理位置之一。 |
| $kvTemplateJson | 包含金鑰保管庫的資源管理器範本的檔路徑 (keyvault.json)。 |
| $kvname | 新密鑰保管庫的名稱。|
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

#### <a name="store-the-certificates-to-the-key-vault"></a>儲存憑證儲存到金鑰保存庫

使用此文稿將 .pfx 檔案中的憑證儲存到新的密鑰保管庫:

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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>使用通用映像部署 Azure VM

本節介紹如何部署通用 VHD 映射以創建新的 Azure VM 資源。 對於此過程,我們將使用提供的 Azure 資源管理器範本和 Azure PowerShell 腳本。

### <a name="prepare-an-azure-resource-manager-template"></a>準備 Azure 資源管理員樣本

將以下 Azure 資源管理器範本進行 VHD 部署複製到名為 VHDtoImage.json 的本地檔。 下一個腳本將請求本地電腦上的位置使用此 JSON。

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

編輯此檔案以提供這些參數的值:

| **參數** | **說明** |
| --- | --- |
| resourceGroupName | 現有的 Azure 資源群組名稱。 通常,使用與密鑰保管庫相同的 RG。 |
| TemplateFile | 檔 VHDtoImage.json 的完整路徑名稱。 |
| userStorageAccountName | 儲存體帳戶的名稱。 |
| sNameForPublicIP | 公共 IP 的 DNS 名稱;必須小寫。 |
| subscriptionId | Azure 訂閱標識碼。 |
| Location | 資源組的標準 Azure 地理位置。 |
| vmName | 虛擬機器的名稱。 |
| vaultName | 密鑰保管庫的名稱。 |
| vaultResourceGroup | 金鑰保存庫的資源群組。 |
| certificateUrl | 憑證的 Web 位址 (URL),包括儲存在金鑰保存的庫中,`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`例如: 。 |
| vhdUrl | 虛擬硬碟的 Web 位址。 |
| vmSize | 虛擬機器實體的大小。 |
| publicIPAddressName | 公共 IP 位址的名稱。 |
| virtualNetworkName | 虛擬網路的名稱。 |
| nicName | 虛擬網路的網路介面卡的名稱。 |
| adminUserName | 管理員帳戶的使用者名。 |
| adminPassword | 管理員密碼。 |
|   |   |

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

複製和編輯以下文稿以提供和`$storageaccount``$vhdUrl`變數的值。 執行它，從您現有的通用 VHD 建立 Azure VM 資源。

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

有兩種方法可以在已部署的映像上執行驗證:

- 使用認證測試工具進行 Azure 認證
- 使用自檢 API

### <a name="download-and-run-the-certification-test-tool"></a>下載並執行認證測試工具

Azure 認證測試工具在本地 Windows 計算機上運行,但測試基於 Azure 的 Windows 或 Linux VM。 它證明使用者 VM 映射可以與 Microsoft Azure 一起使用,並且滿足有關準備 VHD 的指南和要求。 該工具的輸出是相容性報告,您將上傳到合作夥伴中心門戶以請求 VM 認證。

1. 下載並安裝最新版本的 [Azure 認證的認證測試工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 打開認證工具,然後選擇 **「開始新測試**」。。
3. 從 [測試資訊] **** 畫面，輸入測試回合的**測試名稱**。
4. 選擇 VM**的平臺**,Windows 伺服器或 Linux。 所選的平台會影響其餘的選項。
5. 如果 VM 正在使用此資料庫服務,請選擇 **「Azure SQL 資料庫測試」** 複選框。

### <a name="connect-the-certification-tool-to-a-vm-image"></a>將認證工具連線至 VM 映像

該工具使用[Azure PowerShell](https://docs.microsoft.com/powershell/)連接到基於 Windows 的 VM,並透過[SSH.Net](https://www.ssh.com/ssh/protocol/)連接到 Linux VM。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>將認證工具連線至 Linux VM 映像

1. 選擇**SSH 身份驗證**模式:密碼身份驗證或密鑰檔身份驗證。
2. 如果使用基於密碼的身份驗證,請輸入 VM **DNS 名稱**、**使用者名稱**和**密碼**的值。 您還可以更改預設**的 SSH 連接埠**號。

    ![Azure 認證測試工具,Linux VM 映像的密碼身份驗證](media/avm-cert2.png)

3. 如果使用以金鑰檔案為基礎的驗證，請輸入**虛擬機器 DNS 名稱**、**使用者名稱**和**私密金鑰**位置等值。 您還可以包含**密碼**或更改預設**SSH 連接埠**號。

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**將認證工具連接到 Windows 型 VM 映像**

1. 輸入完全限定的**VM DNS 名稱**(例如,MyVMName.Cloudapp.net)。
2. 輸入 [使用者名稱]**** 和 [密碼]**** 的值。

    ![Azure 認證測試工具,基於 Windows 的 VM 映像的密碼身分驗證](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>執行認證測試

在認證工具中為 VM 映像指定參數值後,選擇 **「測試連接**」以創建與 VM 的有效連接。 驗證連線後，選取 [下一步] **** 啟動測試。 測試完成後,測試結果將顯示在表中。 "狀態"列顯示每個測試的(通過/失敗/警告)。 如果任何一項測試失敗，您的映像即_未_通過認證。 在這種情況下,請查看要求和失敗消息,進行建議的更改,然後再次運行測試。

自動測試完成後,在 **「常規****評估和****內核自訂**」螢幕的兩個選項卡上提供有關 VM 映射的其他資訊,然後選擇 **「下一步**」 。

最後一個螢幕允許您提供更多資訊,例如 Linux VM 映射的 SSH 訪問資訊,以及查找異常時任何失敗評估的說明。

最後,選擇 **「生成報告」** 以下載已執行測試用例的測試結果和日誌檔以及對調查表的回答。 將結果儲存在與您的 VHD 相同的容器中。

## <a name="next-step"></a>後續步驟

- [為每個 VHD 產生統一的資源識別碼 (URI)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
