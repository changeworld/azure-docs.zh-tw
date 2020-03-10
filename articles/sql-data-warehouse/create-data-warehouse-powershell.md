---
title: 快速入門：建立資料倉儲 (PowerShell)
description: 使用 Azure PowerShell 快速建立 Azure Synapse Analytics 資料倉儲邏輯伺服器與伺服器層級防火牆規則。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9df9b4b1bdb33a856d9e31d65981e8654af049d2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199983"
---
# <a name="quickstart-create--query-a-data-warehouse-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立和查詢資料倉儲

使用 Azure PowerShell 佈建 SQL 集區，以建立 Azure Synapse Analytics 資料倉儲。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

> [!NOTE]
> 建立倉儲可能會產生新的可計費服務。  如需詳細資訊，請參閱 [Azure Synapse Analytics 定價](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登入 Azure 訂用帳戶並遵循畫面上的指示進行。

```powershell
Connect-AzAccount
```

若要查看您正在使用的訂用帳戶，請執行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Get-AzSubscription
```

如果需要使用不同於預設值的訂用帳戶，請執行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>建立變數

定義此快速入門中的指令碼所使用的變數。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 `westeurope` 位置建立名為 `myResourceGroup` 的資源群組。

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>建立邏輯伺服器

使用 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) 命令建立 [Azure SQL 邏輯伺服器](../sql-database/sql-database-logical-servers.md)。 邏輯伺服器包含一組當作群組管理的資料庫。 下列範例會使用名為 `ServerAdmin` 的系統管理員使用者與密碼 `ChangeYourAdminPassword1` 在資源群組中建立隨機命名的伺服器。 視需要取代這些預先定義的值。

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>設定伺服器防火牆規則

使用 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) 命令建立 [Azure SQL 伺服器層級防火牆規則](../sql-database/sql-database-firewall-configure.md)。 伺服器層級防火牆規則可讓外部應用程式 (例如 SQL Server Management Studio 或 SQLCMD 公用程式) 穿過 SQL 資料倉儲服務防火牆連線到 SQL 資料倉儲。 在下列範例中，只會針對其他 Azure 資源開啟防火牆。 若要啟用外部連線，請將 IP 位址變更為適合您環境的地址。 若要開啟所有 IP 位址，請使用 0.0.0.0 作為起始 IP 位址，並使用 255.255.255.255 作為結束位址。

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL 端點會透過連接埠 1433 進行通訊。 如果您嘗試從公司網路連線，您網路的防火牆可能不允許透過連接埠 1433 的連出流量。 如果是這樣，除非 IT 部門開放連接埠 1433，否則您將無法連線到您的 Azure SQL 伺服器。
>


## <a name="create-a-data-warehouse"></a>建立資料倉儲
這個範例會使用先前定義的變數建立資料倉儲。  並將服務目標指定為成本較低的資料倉儲起點 DW100c。 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

必要參數如下：

* **RequestedServiceObjectiveName**：您要求的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)數量。 增加此數量會增加計算成本。 如需支援值的清單，請參閱[記憶體和並行存取限制](memory-concurrency-limits.md)。
* **DatabaseName**：您要建立之資料倉儲的名稱。
* **ServerName**：您用來建立的伺服器名稱。
* **ResourceGroupName**：您使用的資源群組。 若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
* **版本**：必須是 "DataWarehouse"，才能建立資料倉儲。

選擇性參數如下：

- **CollationName**：未指定定序時的預設值為 SQL_Latin1_General_CP1_CI_AS。 無法變更資料庫的定序。
- **MaxSizeBytes**：資料庫的預設大小上限為 240TB。 大小上限會限制資料列存放區資料。 單欄式資料則無儲存限制。

如需有關參數選項的詳細資訊，請參閱 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)。


## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門教學課程會以此快速入門為基礎。 

> [!TIP]
> 如果您打算繼續進行後續的快速入門教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除此快速入門所建立的所有資源。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>後續步驟

您現在已建立資料倉儲、建立防火牆規則、連線到您的資料倉儲，並執行了一些查詢。 若要深入了解，請繼續進行載入資料的教學課程。
> [!div class="nextstepaction"]
>[將資料載入至資料倉儲](load-data-from-azure-blob-storage-using-polybase.md)
