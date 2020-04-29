---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72511454"
---
Azure 磁碟加密可以透過[Azure CLI](/cli/azure)和[Azure PowerShell](/powershell/azure/new-azureps-module-az)來啟用及管理。 若要這麼做，您必須在本機安裝這些工具，並聯機到您的 Azure 訂用帳戶。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以遵循[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)中的步驟，將它安裝在本機。

若要使用[Azure CLI 登入您的 Azure 帳戶](/cli/azure/authenticate-azure-cli)，請使用[az login](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```azurecli
az login
```

如果您想要選取用於登入的租用戶，請使用：
    
```azurecli
az login --tenant <tenant>
```

如果您有多個訂用帳戶並想要指定特定訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list) 取得訂用帳戶清單並以 [az account set](/cli/azure/account#az-account-set) 進行指定。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

如需詳細資訊，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az 模組](/powershell/azure/new-azureps-module-az)提供了一組 Cmdlet，可使用[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)模型來管理您的 Azure 資源。 您可以在瀏覽器中使用[Azure Cloud Shell](/azure/cloud-shell/overview)，也可以使用[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)中的指示，將它安裝在本機電腦上。 

如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

若要使用[Azure PowerShell 登入您的 Azure 帳戶](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，請使用[disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet。

```powershell
Connect-AzAccount
```

如果您有多個訂用帳戶，而且想要指定一個訂用帳戶，請使用[get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)指令程式列出這些訂用帳戶，後面接著[set-azcoNtext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) Cmdlet：

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

執行[set-azcoNtext](/powershell/module/Az.Accounts/Get-AzContext)指令程式將會確認已選取正確的訂用帳戶。

若要確認已安裝 Azure 磁碟加密 Cmdlet，請使用[Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) Cmdlet：
     
```powershell
Get-command *diskencryption*
```
如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。 