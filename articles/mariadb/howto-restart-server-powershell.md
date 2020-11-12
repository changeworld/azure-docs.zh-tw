---
title: 重新開機伺服器-Azure PowerShell-適用於 MariaDB 的 Azure 資料庫
description: 本文說明如何使用 PowerShell 來重新開機適用於 MariaDB 的 Azure 資料庫伺服器。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538932"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>使用 PowerShell 重新開機適用於 MariaDB 的 Azure 資料庫伺服器

本主題說明如何重新啟動適用於 MariaDB 的 Azure 資料庫伺服器。 基於維護原因，您可能需要重新開機伺服器，這會導致作業期間短暫中斷。

如果服務忙碌中，則會封鎖伺服器重新開機。 例如，該服務可能正在處理先前要求的作業，例如調整虛擬核心。

完成重新開機所需的時間量取決於適用于 mariadb 復原程式。 若要減少重新開機的時間，建議您在重新開機之前，將伺服器上發生的活動量降至最低。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](/powershell/azure/install-az-ps)安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)于瀏覽器中
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 雖然 Az.MariaDb PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> 在正式推出 Az.MariaDb PowerShell 模組後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式提供。

如果您選擇在本機使用 PowerShell，請使用 [disconnect-azaccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連接到您的 Azure 帳戶。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>重新啟動伺服器

使用下列命令重新開機伺服器：

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 PowerShell 建立適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)