---
title: 使用 ODBC 連接到 Azure 資料資源管理器
description: 在本文中，您將瞭解如何設置與 Azure 資料資源管理器的開放資料庫連接 （ODBC） 連接。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034023"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>使用 ODBC 連接到 Azure 資料資源管理器

開放資料庫連接 （[ODBC](/sql/odbc/reference/odbc-overview)） 是一個被廣泛接受的應用程式開發介面 （API） 用於資料庫訪問. 使用 ODBC 從沒有專用連線器的應用程式連接到 Azure 資料資源管理器。

在後臺，應用程式調用 ODBC 介面中的函數，這些函數在資料庫特定的模組中實現，稱為*驅動程式*。 Azure 資料資源管理器支援 SQL Server 通訊協定的子集[（MS-TDS），](/azure/kusto/api/tds/)因此可以使用 SQL Server 的 ODBC 驅動程式。

使用以下視頻，您可以學習創建 ODBC 連接。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

或者，您可以按照下文詳述的[配置 ODBC 資料來源](#configure-the-odbc-data-source)。 

在本文中，您將瞭解如何使用 SQL Server ODBC 驅動程式，以便可以從支援 ODBC 的任何應用程式連接到 Azure 資料資源管理器。 

## <a name="prerequisites"></a>Prerequisites

您需要下列項目：

* [適用于 SQL Server 版本 17.2.0.1 或更高版本的](/sql/connect/odbc/download-odbc-driver-for-sql-server)作業系統的 Microsoft ODBC 驅動程式。

## <a name="configure-the-odbc-data-source"></a>配置 ODBC 資料來源

按照以下步驟使用 SQL Server 的 ODBC 驅動程式配置 ODBC 資料來源。

1. 在 Windows 中，搜索*ODBC 資料來源*，並打開 ODBC 資料來源桌面應用。

1. 選取 [加入]****。

    ![新增資料來源](media/connect-odbc/add-data-source.png)

1. 為 SQL 伺服器選擇**ODBC 驅動程式 17，** 然後**完成**。

    ![選擇驅動程式](media/connect-odbc/select-driver.png)

1. 輸入要連接到的連接和群集的名稱和說明，然後選擇 **"下一步**"。 群集 URL 應位於*\<群集\>名稱表單中。\<區域\>.kusto.windows.net*。

    ![選取伺服器](media/connect-odbc/select-server.png)

1. 選擇**活動目錄集成**，然後**下一個**。

    ![Active Directory 整合式](media/connect-odbc/active-directory-integrated.png)

1. 選擇具有示例資料的資料庫，然後**選擇 Next**。

    ![更改預設資料庫](media/connect-odbc/change-default-database.png)

1. 在下一個螢幕上，將所有選項保留為預設值，然後選擇 **"完成**"。

1. 選擇**測試資料來源**。

    ![測試資料來源](media/connect-odbc/test-data-source.png)

1. 驗證測試是否成功，然後選擇 **"確定**"。 如果測試未成功，請檢查您在前面的步驟中指定的值，並確保您有足夠的許可權連接到群集。

    ![測試成功](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>後續步驟

* [從 Tableau 連接到 Azure 資料資源管理器](tableau.md)