---
title: 使用 SQL Server Management Studio 查詢 Azure Blockchain Workbench 資料
description: 了解如何從 SQL Server Management Studio 連線至 Azure Blockchain Workbench 的 SQL Database。
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: 16e7f9a6c36ea42e1d0a4144e680baebee5a6c21
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009461"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>將 Azure Blockchain Workbench 資料搭配 SQL Server Management Studio 使用

Microsoft SQL Server Management Studio 可供快速撰寫查詢並對 Azure Blockchain Workbench 的 SQL DB 測試查詢。 本節包含逐步解說，說明如何從 SQL Server Management Studio 內連接到 Azure Blockchain Workbench 的 SQL Database。

## <a name="prerequisites"></a>Prerequisites

* 下載 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>在 Azure Blockchain Workbench 中將 SQL Server Management Studio 連線至資料

1. 開啟 SQL Server Management Studio，然後選取 [連線]。
2. 選取 [資料庫引擎]。

    ![資料庫引擎](./media/data-sql-management-studio/database-engine.png)

3. 在 [連線到伺服器] 對話方塊中，輸入伺服器名稱與您的資料庫認證。

    如果您使用的是由 Azure Blockchain Workbench 部署程序建立的認證，則使用者名稱為 **dbadmin**，且密碼為您在部署期間提供的密碼。

    ![輸入 SQL 認證](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio 會顯示 Azure Blockchain Workbench 資料庫中的資料庫、資料庫檢視與已儲存程序的清單。

      ![資料庫清單](./media/data-sql-management-studio/db-list.png)

5. 若要檢視與任何資料庫檢視相關聯的資料，您可以使用以下步驟自動產生 select 陳述式。
6. 以滑鼠右鍵按一下物件總管中的任何資料庫檢視。
7. 選取 [指令碼檢視為]。
8. 選擇 [SELECT 至]。
9. 選取 [新增查詢編輯器視窗]。
10. 您可以選取 [新增查詢] 建立新的查詢。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的資料庫檢視](database-views.md)
