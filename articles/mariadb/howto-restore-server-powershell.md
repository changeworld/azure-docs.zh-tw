---
title: 備份和還原-Azure PowerShell-適用於 MariaDB 的 Azure 資料庫
description: 瞭解如何使用 Azure PowerShell 在適用於 MariaDB 的 Azure 資料庫中備份和還原伺服器。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3870ae192955df11107f0ebb5c618b90a0cc799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87833244"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>如何使用 PowerShell 備份和還原適用於 MariaDB 的 Azure 資料庫伺服器

適用於 MariaDB 的 Azure 資料庫伺服器會定期備份，以啟用還原功能。 透過此功能，您可以將伺服器和其所有資料庫還原至更早的時間點 (在新的伺服器上)。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明指南，您需要：

- [Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)安裝在本機或[Azure Cloud Shell](https://shell.azure.com/)于瀏覽器中
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> 雖然 Az.MariaDb PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.MariaDb -AllowPrerelease`。
> 在正式推出 Az.MariaDb PowerShell 模組後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式提供。

如果您選擇在本機使用 PowerShell，請使用 [disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) Cmdlet 連接到您的 Azure 帳戶。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>設定備份組態

在伺服器建立時，您可以選擇在伺服器上設定本機多餘或地理位置多餘的備份。

> [!NOTE]
> 在建立伺服器之後，就無法變更其所擁有的冗余類型、地理位置多餘的和本機冗余。

透過命令建立伺服器時 `New-AzMariaDbServer` ， **GeoRedundantBackup** 參數會決定您的備份冗余選項。 若 **已啟用**，則會採用地理多餘備份。 或者，如果 **停用**，則會採用本機多餘的備份。

備份保留期限是由 **BackupRetentionDay** 參數所設定。

如需在伺服器建立期間設定這些值的詳細資訊，請參閱 [使用 PowerShell 建立適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-powershell.md)。

您可以依下列方式變更伺服器的備份保留期：

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

上述範例會將 mydemoserver 的備份保留期變更為 10 天。

備份保留期限會控制可取回時間點還原的距離，因為它是以可用的備份為基礎。 下一節會進一步說明時間點還原。

## <a name="server-point-in-time-restore"></a>時間點還原

您可以將伺服器還原至先前的時間點。 還原的資料會複製到新的伺服器，而現有的伺服器則不會變更。 例如，如果資料表意外卸除，您可以還原到卸除之前的時間。 然後，您可從伺服器的還原複本擷取遺失的資料表和資料。

若要還原伺服器，請使用 `Restore-AzMariaDbServer` PowerShell Cmdlet。

### <a name="run-the-restore-command"></a>執行 restore 命令

若要還原伺服器，請從 PowerShell 執行下列範例。

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Cmdlet 的 **PointInTimeRestore** 參數集 `Restore-AzMariaDbServer` 需要下列參數：

| 設定 | 建議的值 | 描述  |
| --- | --- | --- |
| resourceGroupName |  myresourcegroup |  來源伺服器所在的資源群組。  |
| 名稱 | mydemoserver-restored | 還原命令所建立之新伺服器的名稱。 |
| RestorePointInTime | 2020-03-13T13：59：00Z | 請選取要還原的時間點。 這個日期和時間必須在來源伺服器的備份保留期限內。 請使用 ISO8601 日期和時間格式。 例如，您可以使用自己的當地時區，例如 **2020-03-13T05：59： 00-08： 00**。 您也可以使用 UTC 祖魯文格式，例如 **2018-03-13T13：59： 00Z**。 |
| UsePointInTimeRestore | `<SwitchParameter>` | 使用時間點模式來還原。 |

當您將伺服器還原到之前的時間點時，會建立新的伺服器。 指定時間點的原始伺服器及其資料庫會複製到新的伺服器。

已還原伺服器的位置與定價層值與原始伺服器相同。

完成還原程序後，找出新的伺服器，確認資料如預期般還原。 新伺服器的伺服器管理員登入名稱和密碼，與還原啟動時有效的現有伺服器相同。 您可以從新伺服器的 [概觀]**** 頁面變更密碼。

在還原期間建立的新伺服器不會有原始伺服器中的 VNet 服務端點。 您必須為新伺服器分別設定這些規則。 系統會還原原始伺服器的防火牆規則。

## <a name="geo-restore"></a>異地還原

如果您將伺服器設定為地理位置多餘的備份，就可以從現有伺服器的備份建立新的伺服器。 您可以在任何可使用「適用於 MariaDB 的 Azure 資料庫」的區域中建立這個新伺服器。

若要使用異地多餘備份建立伺服器，請使用 `Restore-AzMariaDbServer` 命令搭配 **UseGeoRestore** 參數。

> [!NOTE]
> 第一次建立伺服器時，可能無法立即用來進行異地還原。 必要的中繼資料可能需要幾小時才會填入。

若要異地還原伺服器，請從 PowerShell 執行下列範例：

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

此範例會在美國東部區域中建立名為 **mydemoserver-georestored** 的新伺服器，該伺服器屬於 **myresourcegroup**。 這是一般用途、具有 8 個 vCore 的第 5 代伺服器。 此伺服器是從 **mydemoserver**的異地冗余備份建立，也是在資源群組 **myresourcegroup**中建立。

若要在與現有伺服器不同的資源群組中建立新的伺服器，請使用 **ResourceGroupName** 參數來指定新的資源組名，如下列範例所示：

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Cmdlet 的 **GeoRestore** 參數集 `Restore-AzMariaDbServer` 需要下列參數：

| 設定 | 建議的值 | 描述  |
| --- | --- | --- |
|resourceGroupName | myresourcegroup | 新伺服器所屬的資源組名。|
|名稱 | mydemoserver-georestored | 新伺服器的名稱。 |
|Location | eastus | 新伺服器的位置。 |
|UseGeoRestore | `<SwitchParameter>` | 使用地理模式來還原。 |

使用異地還原建立新的伺服器時，除非指定了 **Sku** 參數，否則它會繼承與來源伺服器相同的儲存體大小和定價層。

完成還原程序後，找出新的伺服器，確認資料如預期般還原。 新伺服器的伺服器管理員登入名稱和密碼，與還原啟動時有效的現有伺服器相同。 您可以從新伺服器的 [概觀]**** 頁面變更密碼。

在還原期間建立的新伺服器不會有原始伺服器中的 VNet 服務端點。 您必須為這個新的伺服器個別設定這些規則。 系統會還原原始伺服器的防火牆規則。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用 PowerShell 產生適用於 MariaDB 的 Azure 資料庫連接字串](howto-connection-string-powershell.md)
