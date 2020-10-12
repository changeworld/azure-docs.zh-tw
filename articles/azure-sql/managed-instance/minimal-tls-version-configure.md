---
title: 設定基本的 TLS 版本管理的實例
description: 瞭解如何設定受控實例的基本 TLS 版本
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 2dbd4b9af3db122703a7f2b4e0140ec3305f1c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620047"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>在 Azure SQL 受控執行個體中設定基本的 TLS 版本
最基本的 [傳輸層安全性 (TLS) ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本設定，可讓客戶控制其 Azure SQL 受控執行個體所使用的 TLS 版本。

目前我們支援 TLS 1.0、1.1 和1.2。 設定最短的 TLS 版本，可確保支援後續、較新的 TLS 版本。 例如，選擇大於1.1 的 TLS 版本。 表示只接受具有 TLS 1.1 和1.2 的連線，而且會拒絕 TLS 1.0。 在測試以確認您的應用程式支援應用程式之後，建議您將最基本的 TLS 版本設定為1.2，因為它包含在舊版中找到的弱點修正，而且是 Azure SQL 受控執行個體中支援的最高 TLS 版本。

如果客戶的應用程式依賴舊版的 TLS，建議您根據應用程式的需求來設定最基本的 TLS 版本。 針對依賴應用程式使用未加密連線來連線的客戶，我們建議不要設定任何基本的 TLS 版本。 

如需詳細資訊，請參閱 [SQL Database 連接的 TLS 考慮](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

設定最小 TLS 版本之後，如果用戶端使用的 TLS 版本低於伺服器的最小 TLS 版本，其登入嘗試將會失敗，並出現下列錯誤：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>透過 PowerShell 設定基本的 TLS 版本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 下列腳本需要 [Azure PowerShell 模組](/powershell/azure/install-az-ps)。

下列 PowerShell 腳本會示範如何 `Get` 與 `Set` 實例層級的 **最基本 TLS 版本** 屬性：

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>透過 Azure CLI 設定基本的 TLS 版本

> [!IMPORTANT]
> 本節中的所有腳本都需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

下列 CLI 腳本說明如何變更 bash shell 中的 **最基本 TLS 版本** 設定：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
