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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511454"
---
Azure 磁片加密可以通過[Azure CLI](/cli/azure)和[Azure PowerShell](/powershell/azure/new-azureps-module-az)啟用和管理。 為此，必須在本地安裝工具並連接到 Azure 訂閱。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以按照[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)中的步驟在本地安裝它。

要[使用 Azure CLI 登錄到 Azure 帳戶](/cli/azure/authenticate-azure-cli)，請使用[az 登錄](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

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
[Azure PowerShell az 模組](/powershell/azure/new-azureps-module-az)提供一組 Cmdlet，這些 Cmdlet 使用 Azure[資源管理器](/azure/azure-resource-manager/resource-group-overview)模型來管理 Azure 資源。 您可以在瀏覽器中將其與 Azure[雲外殼](/azure/cloud-shell/overview)一起使用，也可以使用[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)中的說明將其安裝到本地電腦上。 

如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

要[使用 Azure PowerShell 登錄到 Azure 帳戶](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，請使用[連接-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet。

```powershell
Connect-AzAccount
```

如果您有多個訂閱並希望指定一個訂閱，請使用[獲取-Az訂閱](/powershell/module/Az.Accounts/Get-AzSubscription)Cmdlet 列出它們，後跟[Set-AzCoNtext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) Cmdlet：

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

運行[Get-AzCoNtext](/powershell/module/Az.Accounts/Get-AzContext) Cmdlet 將驗證是否選擇了正確的訂閱。

要確認安裝了 Azure 磁片加密 Cmdlet，請使用[Get 命令](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)Cmdlet：
     
```powershell
Get-command *diskencryption*
```
有關詳細資訊，請參閱使用[Azure PowerShell 入門](/powershell/azure/get-started-azureps)。 