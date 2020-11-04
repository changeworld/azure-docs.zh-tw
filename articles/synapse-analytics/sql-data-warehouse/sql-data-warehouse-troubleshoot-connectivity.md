---
title: 連線能力疑難排解
description: 針對專用 SQL 集區中的連線能力進行疑難排解。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 82b9f988ef4a7f4a53cd0b451da28642b53bcb65
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308372"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool"></a>針對專用 SQL 集區中的連線能力問題進行疑難排解

本文列出連接到您專用的 SQL 集區資料庫時，常見的疑難排解技巧。

## <a name="check-service-availability"></a>檢查服務可用性

檢查以了解服務是否可供使用。 在 Azure 入口網站中，移至您嘗試連接的專用 SQL 集區。 在左側 TOC 窗格中，按一下 [診斷並解決問題]。

![選取資源健康狀態](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

您專用的 SQL 集區的狀態將會顯示在此處。 如果服務未顯示為 **可用** ，請查看更進一步的步驟。

![可用的服務](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果您的資源健康狀態顯示您專用的 SQL 集區實例已暫停或正在調整，請依照指引繼續您的實例。

![螢幕擷取畫面顯示已暫停或調整的 SQL 資料倉儲實例。](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
您可以在這裡找到資源健康狀態的其他相關資訊。

## <a name="check-for-paused-or-scaling-operation"></a>檢查已暫停或正在調整的作業

請檢查入口網站，以查看您專用的 SQL 集區實例是否已暫停或調整。

![螢幕擷取畫面顯示如何檢查資料倉儲是否已暫停。](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果您看到服務已暫停或正在調整，請檢查服務是否不在維護排程期間。 在專用 SQL 集區 *總覽* 的入口網站上，您會看到所選擇的維護排程。

![維護排程概觀](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否則，請洽詢您的 IT 管理員，確認此維護不是已排定事件。 若要繼續專用的 SQL 集區實例，請遵循下列 [步驟](pause-and-resume-compute-portal.md)。

## <a name="check-your-firewall-settings"></a>檢查防火牆設定

專用的 SQL 集區資料庫會透過埠1433進行通訊。如果您嘗試從公司網路內進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 在此情況下，除非 IT 部門開啟埠1433，否則您無法連線到您的 [邏輯伺服器](../../azure-sql/database/logical-servers.md) 。 您可以在[這裡](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)找到防火牆組態的其他資訊。

## <a name="check-your-vnetservice-endpoint-settings"></a>檢查 VNet/服務端點設定

如果您收到錯誤 40914 和 40615，請參閱[此處的錯誤描述和解決方法](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>檢查最新的驅動程式

### <a name="software"></a>軟體

請檢查以確定您使用的是最新的工具來連接到您的專用 SQL 集區：

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驅動程式

請檢查以確定您使用的是最新版的驅動程式。  使用較舊的驅動程式版本可能會導致非預期的行為，因為較舊的驅動程式可能不支援新的功能。

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>檢查連接字串

進行檢查以確認連接字串是否已正確設定。  以下是一些範例。  您可以[在這裡找到關於連接字串](sql-data-warehouse-connection-strings.md)的其他資訊。

ADO.NET 連接字串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 連接字串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 連接字串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 連接字串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>連線斷續的問題

進行檢查以了解伺服器上是否因為有大量已排入佇列的要求而讓負載太高。 您可能需要擴大專屬的 SQL 集區，以取得額外的資源。

## <a name="common-error-messages"></a>常見的錯誤訊息

錯誤 40914 和 40615，請參閱[此處的錯誤描述和解決方法](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍然有連線問題嗎？

建立[支援票證](sql-data-warehouse-get-started-create-support-ticket.md)，讓工程小組可以提供支援。
