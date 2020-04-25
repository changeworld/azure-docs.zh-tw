---
title: 建立 Azure Key Vault 憑證 |Azure Marketplace
description: 說明如何從部署的 Azure VHD 註冊 VM。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148246"
---
# <a name="create-certificates-for-azure-key-vault"></a>建立 Azure Key Vault 的憑證

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請遵循[AZURE VM 映射認證](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification)中的指示來管理您遷移的供應專案。

本文說明如何佈建建立 Windows 遠端管理 (WinRM) 和 Azure 託管虛擬機器 (VM) 連線所需的自我簽署憑證。 此程序由三個步驟組成：

1.    建立安全性憑證。 
2.    建立 Azure Key Vault 來儲存此憑證。 
3.    將憑證儲存到此金鑰保存庫。 

您可以針對這項工作使用新的或現有的 Azure 資源群組。  前一種方式用於以下的說明。



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>建立憑證

編輯並執行下列 Azure Powershell 指令碼，以便在本機資料夾中建立憑證檔案 (.pfx)。  您必須取代下列參數的值：

|  **參數**        |   **描述**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | 儲存 .pfx 檔案所在的本機資料夾  |
| `$location`    | 其中一個 Azure 標準的地理位置  |
| `$vmName`      | 已規劃的 Azure 虛擬機器名稱   |
| `$certname`    | 憑證的名稱；必須符合已規劃 VM 的完整網域名稱  |
| `$certpassword` | 憑證的密碼，必須符合用於已規劃 VM 的密碼  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> 在這些步驟期間保持相同的 PowerShell 主控台工作階段作用中，以便保留各種不同參數的值。

> [!WARNING]
> 如果您儲存此指令碼，僅能將其儲存在安全的位置，因為它包含安全性資訊 (密碼)。


## <a name="create-the-key-vault"></a>建立金鑰保存庫

將[金鑰保存庫部署範本](./cpp-key-vault-deploy-template.md)的內容複製至本機電腦上的檔案  （在下列範例腳本中，此資源是`C:\certLocation\keyvault.json`。） 編輯並執行下列 Azure Powershell 腳本，以建立 Azure Key Vault 實例和相關聯的資源群組。  您必須取代下列參數的值：

|  **參數**        |   **描述**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | 附加至部署識別碼的任意數字字串                     |
| `$rgName`             | 要建立的 Azure 資源群組 (RG) 名稱                                        |
|  `$location`          | 其中一個 Azure 標準的地理位置                                  |
| `$kvTemplateJson`     | 檔案 (keyvault.json) 的路徑，其中包含金鑰保存庫的 Resource Manager 範本 |
| `$kvname`             | 新金鑰保存庫的名稱                                                       |
|  |  |

```powershell
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

## <a name="store-the-certificate"></a>儲存憑證

您現在可以執行下列指令碼，將包含在 .pfx 檔案中的憑證儲存到新的金鑰保存庫。 

```powershell
    #push certificate to key vault secret

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


## <a name="next-steps"></a>後續步驟

接下來您將[從使用者 VM 映像部署 VM](./cpp-deploy-vm-user-image.md)。
