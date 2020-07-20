---
title: Azure Resource Manager 範本 - Azure SQL Database 與 SQL 受控執行個體
description: 使用 Azure Resource Manager 範本建立並設定 Azure SQL Database 與 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: ee2c6580285246d3b170602e507aed038b2507f4
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983633"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>適用於 Azure SQL Database 與 SQL 受控執行個體的 Azure Resource Manager 範本
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager 範本可讓您定義基礎結構即程式碼，並將解決方案部署至 Azure 雲端，以供 Azure SQL Database 與 SQL 受控執行個體使用。

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

下表包含適用於 Azure SQL Database 的 Azure Resource Manager 範本的連結。

|連結 |描述|
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | 此 Azure Resource Manager 範本可在 Azure SQL Database 中建立單一資料庫，並設定伺服器層級 IP 防火牆規則。 |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | 此 Azure Resource Manager 範本可建立 Azure SQL Database 的伺服器。 |
| [彈性集區](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | 此範本可讓您部署彈性集區，並對其指派資料庫。 |
| [容錯移轉群組](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | 此範本可在 Azure SQL Database 中建立兩部伺服器、單一資料庫與一個容錯移轉群組。|
| [威脅偵測](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | 此範本可讓您部署啟用「威脅偵測」的伺服器與一組資料庫，並且為每個資料庫部署警示的電子郵件地址。 「威脅偵測」包含在 SQL 進階威脅防護 (ATP) 供應項目中，可提供一層安全性來防禦伺服器與資料庫所受到的潛在威脅。|
| [將稽核資料移至 Azure Blob 儲存體](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | 此範本可讓您部署啟用稽核的伺服器，以將稽核記錄寫入至 Blob 儲存體。 Azure SQL Database 的稽核可追蹤資料庫事件，並將其寫入至可存放在 Azure 儲存體帳戶、OMS 工作區或事件中樞的稽核記錄中。|
| [將稽核資料移至 Azure 事件中樞](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | 此範本可讓您部署啟用稽核的伺服器，以將稽核記錄寫入至現有的事件中樞。 若要將稽核事件傳送到事件中樞，請設定 `Enabled` `State` 的稽核設定，並將 `IsAzureMonitorTargetEnabled` 設為 `true`。 此外，請在 `master` 資料庫上設定 `SQLSecurityAuditEvents` 記錄類別的診斷設定 (用於服務層級稽核)。 稽核可追蹤資料庫事件，並將其寫入至可存放在 Azure 儲存體帳戶、OMS 工作區或事件中樞的稽核記錄中。|
| [Azure Web 應用程式與 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | 此範本可在「基本」服務層級，於 Azure SQL Database 中建立免費的 Azure Web 應用程式與資料庫。|
| [Azure Web 應用程式、Redis 快取與 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | 此範本可在相同的資源群組中建立 Web 應用程式、Redis 快取和資料庫，並且在 Web 應用程式中針對資料庫和 Redis 快取建立兩個連接字串。|
| [使用 ADF V2 從 Blob 儲存體匯入資料](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | 此 Azure Resource Manager 範本可建立能夠將資料從 Azure Blob 儲存體複製到 SQL Database 的 Azure Data Factory V2 執行個體。|
| [HDInsight 叢集與資料庫](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | 此範本可讓您建立 HDInsight 叢集、邏輯 SQL 伺服器、資料庫與兩個資料表。 [搭配使用 Sqoop 與 HDInsight 中的 Hadoop](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) 一文中使用了此範本。 |
| [依排程執行 SQL 預存程序的 Azure 邏輯應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | 此範本可讓您建立會依排程執行 SQL 預存程序的邏輯應用程式。 此程序的任何引數都可放入範本的本文區段中。|

## <a name="azure-sql-managed-instance"></a>[Azure SQL 受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL 受控執行個體之 Azure Resource Manager 範本的連結。

|連結|描述|
|---|---|
| [新 VNet 中的 SQL 受控執行個體](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) \(英文\) | 此 Azure Resource Manager 範本可在虛擬網路中建立新設定的 Azure 虛擬網路和受控執行個體。 |
| [SQL 受控執行個體的網路環境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) \(英文\) | 此部署會建立已設定的 Azure 虛擬網路和兩個子網路，一個專門用於您的受控執行個體，另一個則可用來放置其他資源 (例如 VM、App Service 環境等)。 此範本會建立已適當設定的網路環境，供您在其中部署受控執行個體。 |
| [具有 P2S 連線的 SQL 受控執行個體](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) \(英文\) | 此部署會建立具有兩個子網路 `ManagedInstance` 和 `GatewaySubnet` 的 Azure 虛擬網路。 SQL 受控執行個體將部署在 ManagedInstance 子網路中。 虛擬網路閘道會建立在 `GatewaySubnet` 子網路中，並設定點對站 VPN 連線。 |
| [SQL 受控執行個體與虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | 此部署會建立具有兩個子網路 `ManagedInstance` 和 `Management` 的 Azure 虛擬網路。 SQL 受控執行個體將部署在 `ManagedInstance` 子網路中。 具有最新版 SQL Server Management Studio (SSMS) 的虛擬機器會部署在 `Management` 子網路中。 |

---

