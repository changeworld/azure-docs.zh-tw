---
title: Azure 資源管理員範本
description: 使用 Azure Resource Manager 範本建立和設定 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79214012"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>適用於 Azure SQL Database 的 Azure Resource Manager 範本

Azure Resource Manager 範本可讓您定義基礎結構即程式碼，並將解決方案部署至 Azure 雲端。

## <a name="single-database--elastic-pool"></a>[單一資料庫和彈性集區](#tab/single-database)

下表包含適用於 Azure SQL Database 的 Azure Resource Manager 範本的連結。

| |  |
|---|---|
| [單一資料庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | 此 Azure Resource Manager 範本可建立具有邏輯伺服器的單一 Azure SQL Database，並設定防火牆規則。 |
| [邏輯伺服器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | 此 Azure Resource Manager 範本可建立 Azure SQL Database 的邏輯伺服器。 |
| [彈性集區](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | 此範本可讓您部署新的彈性集區，並為其指派新的相關 SQL Server 和新的 SQL Database。 |
| [容錯移轉群組](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | 此範本可建立兩個 Azure SQL 邏輯伺服器、一個 SQL 資料庫和一個容錯移轉群組。|
| [威脅偵測](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | 此範本可讓您部署啟用「威脅偵測」的 Azure SQL 邏輯伺服器和一組 Azure SQL Database，並且為每個資料庫部署警示的電子郵件地址。 「威脅偵測」包含在 SQL 進階威脅防護 (ATP) 供應項目中，可提供一層安全性來防禦 SQL 伺服器和資料庫所受到的潛在威脅。|
| [將稽核資料移至 Azure Blob 儲存體](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | 此範本可讓您部署啟用稽核的 Azure SQL Server 邏輯伺服器，以將稽核記錄寫入至 Blob 儲存體。 Azure SQL Database 的稽核可追蹤資料庫事件，並將其寫入至可存放在 Azure 儲存體帳戶、OMS 工作區或事件中樞的稽核記錄中。|
| [將稽核資料移至 Azure 事件中樞](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | 此範本可讓您部署啟用稽核的 Azure SQL Server 伺服器，以將稽核記錄寫入至現有的事件中樞。 若要將稽核事件傳送到事件中樞，請設定 `Enabled` `State` 的稽核設定，並將 `IsAzureMonitorTargetEnabled` 設為 `true`。 此外，請在 `master` 資料庫上設定 `SQLSecurityAuditEvents` 記錄類別的診斷設定 (用於服務層級稽核)。 Azure SQL Database 和 SQL 資料倉儲的稽核可追蹤資料庫事件，並將其寫入至可存放在 Azure 儲存體帳戶、OMS 工作區或事件中樞的稽核記錄中。|
| [Azure Web 應用程式與 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | 此範本可在「基本」服務層級建立免費的 Azure Web 應用程式與 SQL Database。|
| [Azure Web 應用程式、Redis 快取與 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | 此範本可在相同的資源群組中建立 Web 應用程式、Redis 快取和 SQL Database，並且在 Web 應用程式中針對 SQL Database 和 Redis 快取建立兩個連接字串。|
| [使用 ADF V2 從 Blob 儲存體匯入資料](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | 此 Azure Resource Manager 範本可建立能夠將資料從 Azure Blob 儲存體複製到 SQL Database 的 Azure Data Factory V2。|
| [HDInsight 叢集與 SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | 此範本可讓您建立 HDInsight 叢集、SQL Database 伺服器、SQL Database 和兩個資料表。 [搭配使用 Sqoop 與 HDInsight 中的 Hadoop](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) 一文中使用了此範本 |
| [依排程執行 SQL 預存程序的 Azure 邏輯應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | 此範本可讓您建立會依排程執行 SQL 預存程序的邏輯應用程式。 此程序的任何引數都可放入範本的本文區段中。|

## <a name="managed-instance"></a>[受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL Database - 受控執行個體的 Azure Resource Manager 範本的連結。

| |  |
|---|---|
| [新 VNet 中的受控執行個體](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | 此 Azure Resource Manager 範本可在 VNet 中建立新設定的 Azure VNet 和受控執行個體。 |
| [受控執行個體的網路環境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | 此部署會建立已設定的 Azure 虛擬網路和兩個子網路 - 一個專門用於您的受控執行個體，另一個則可用來放置其他資源 (例如 VM、App Service 環境等)。 此範本會建立已適當設定的網路環境，供您在其中部署受控執行個體。 |
| [具有 P2S 連線的受控執行個體](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | 此部署會建立具有兩個子網路 `ManagedInstance` 和 `GatewaySubnet` 的 Azure 虛擬網路。 受控執行個體將部署在 ManagedInstance 子網路中。 虛擬網路閘道會建立在 `GatewaySubnet` 子網路中，並設定點對站 VPN 連線。 |
| [受控執行個體與虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | 此部署會建立具有兩個子網路 `ManagedInstance` 和 `Management` 的 Azure 虛擬網路。 受控執行個體將部署在 `ManagedInstance` 子網路中。 具有最新版 SQL Server Management Studio (SSMS) 的虛擬機器會部署在 `Management` 子網路中。 |

---
