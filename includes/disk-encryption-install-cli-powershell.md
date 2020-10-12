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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72511454"
---
Azure 磁碟加密可以透過 [Azure CLI](/cli/azure) 和 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 來加以啟用及管理。 若要這麼做，您必須在本機安裝工具，並連線到您的 Azure 訂用帳戶。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) 是命令列工具，可用於管理 Azure 資源。 CLI 的設計是要讓您能夠彈性地查詢資料、以非封鎖處理序的形式支援長時間執行作業，並輕鬆地撰寫指令碼。 您可以遵循[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 中的步驟，將其安裝到本機。

若要[使用 Azure CLI 登入您的 Azure 帳戶](/cli/azure/authenticate-azure-cli)，請使用 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令。

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
[Azure PowerShell az 模組](/powershell/azure/new-azureps-module-az)提供了一組 Cmdlet，其會使用 [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 您可以在瀏覽器中搭配 [Azure Cloud Shell](/azure/cloud-shell/overview) 來使用此模組，也可以使用[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)中的指示將其安裝到本機電腦上。 

如果您已將它安裝在本機上，請確定您是使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell 版本](https://github.com/Azure/azure-powershell/releases)。

若要[使用 Azure PowerShell 登入您的 Azure 帳戶](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，請使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) Cmdlet。

```powershell
Connect-AzAccount
```

如果您有多個訂用帳戶，而且想要指定其中一個，請使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) Cmdlet 來列出這些訂用帳戶，後面接著 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) Cmdlet：

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

執行 [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) Cmdlet 能確認所選取的訂用帳戶是否正確。

若要確認是否已安裝 Azure 磁碟加密 Cmdlet，請使用 [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) Cmdlet：
     
```powershell
Get-command *diskencryption*
```
如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。 