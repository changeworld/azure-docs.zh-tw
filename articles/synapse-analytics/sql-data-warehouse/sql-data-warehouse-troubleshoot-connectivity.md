---
title: 連線能力疑難排解
description: 在 Synapse SQL 池中排除連接故障。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408162"
---
# <a name="troubleshooting-connectivity-issues"></a>對連線問題進行疑難排解

本文列出了有關連接到 SQL 分析資料庫的常見故障排除技術。

## <a name="check-service-availability"></a>檢查服務可用性

檢查該服務是否可用。 在 Azure 門戶中,轉到嘗試連接的 Synapse SQL 池。 在左側 TOC 面板中,按兩下 **「診斷並解決問題**」。

![選擇資源執行狀況](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

此處將顯示 Synapse SQL 池的狀態。 如果服務未顯示為 **「可用」** 請檢查其他步驟。

![服務可用](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果「資源」執行狀況顯示 Synapse SQL 池實例已暫停或縮放,請按照該指南繼續實例。

![服務暫停](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)有關資源運行狀況的其他資訊可在此處找到。

## <a name="check-for-paused-or-scaling-operation"></a>檢查已暫停或正在調整的作業

檢查門戶以查看 Synapse SQL 池實例是否暫停或縮放。

![服務暫停](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果您看到服務已暫停或擴展,請檢查服務不在維護計畫期間。 在 Synapse SQL 池*概述*的門戶上,您將看到當選的維護計劃。

![概述維護計劃](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否則,請諮詢 IT 管理員以驗證此維護是否不是計畫事件。 要復原 SQL 分析實體,請按照[以下步驟操作](pause-and-resume-compute-portal.md)。

## <a name="check-your-firewall-settings"></a>檢查防火牆設定

SQL 分析資料庫通過埠 1433 進行通信。如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 在此情況下，除非 IT 部門開啟連接埠 1433，否則您無法連線至 Azure SQL Database 伺服器。 有關防火牆配置的其他資訊,請參閱[此處](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)。

## <a name="check-your-vnetservice-endpoint-settings"></a>檢查 VNet/服務端點設定

如果您收到錯誤 40914 與 40615,請參考[此處的錯誤描述與解決方法](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>檢查最新的驅動程式

### <a name="software"></a>軟體

檢查以確保您使用最新的工具連接到 Synapse SQL 池:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驅動程式

檢查以確保您使用的是最新的驅動程式版本。使用舊版本的驅動程式可能會導致意外行為,因為較舊的驅動程式可能不支援新功能。

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>檢查連接字串

進行檢查以確認連接字串是否已正確設定。  下面是一些示例。  您可以[在這裡找到關於連接字串](sql-data-warehouse-connection-strings.md)的其他資訊。

ADO.NET 連接字串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 連接字串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 連線字串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 連接字串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>間歇性連線問題

進行檢查以了解伺服器上是否因為有大量已排入佇列的要求而讓負載太高。 您可能需要擴展 Synapse SQL 池以尋找其他資源。

## <a name="common-error-messages"></a>常見的錯誤訊息

錯誤 40914 與 40615,請參閱[此處的錯誤描述與解決方法](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍有連接問題?

創建[支援票證](sql-data-warehouse-get-started-create-support-ticket.md),以便工程團隊可以為您提供支援。
