---
title: 從 Azure Marketplace 部署 VM
description: 說明如何從 Azure Marketplace 預先設定的虛擬機器部署虛擬機器。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 2fb04e2e953e2bcbe479f7685b8042fdc7ea1767
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146999"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>從 Azure Marketplace 部署虛擬機器

> [!IMPORTANT]
> 從2020年4月13日開始，我們會開始將 Azure 虛擬機器供應專案的管理移至合作夥伴中心。 在遷移之後，您將在合作夥伴中心建立和管理您的供應專案。 請遵循[AZURE VM 映射認證](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification)中的指示來管理您遷移的供應專案。

本文說明如何使用提供的 Azure PowerShell 指令碼，從 Azure Marketplace 部署預先設定的虛擬機器 (VM)。  此指令碼也會公開 VM 上的 WinRM HTTP 和 HTTPS 端點。  此指令碼會要求您已經將憑證上傳至 Azure Key Vault 中，如[建立 Azure Key Vault 的憑證](./cpp-create-key-vault-cert.md)中所述。 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>VM 部署範本

快速入門 Azure VM 部署範本，可以線上檔案 [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json) 的形式取得。  該範本包含下列參數：

|  **參數**        |   **說明**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | 儲存體帳戶的名稱                       |
| dnsNameForPublicIP    | 公用 IP 的 DNS 名稱。 必須是小寫字母。    |
| adminUserName            | 系統管理員的使用者名稱                          |
| adminPassword            | 系統管理員的密碼                          |
| imagePublisher        | 映像發行者                                   |
| imageOffer            | 映像供應項目                                       |
| imageSKU                | 映像 SKU                                         |
| vmSize                | VM 的大小                                    |
| vmName                | VM 的名稱                                    |
| vaultName                | 金鑰保存庫的名稱                             |
| vaultResourceGroup    | 金鑰保存庫的資源群組                   |
| certificateUrl        | 用於憑證的 URL，包括 KeyVault 中的版本，例如 `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>部署指令碼

編輯下列 Azure PowerShell 指令碼並加以執行，以部署指定的 Azure Marketplace VM。

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>後續步驟

一旦您部署預先設定的 VM 之後，您就可以設定並存取解決方案及所含的服務，或將其用於進一步開發之用。 
