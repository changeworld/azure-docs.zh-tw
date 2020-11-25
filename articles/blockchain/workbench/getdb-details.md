---
title: 取得 Azure Blockchain Workbench 資料庫詳細資料
description: 瞭解如何取得 Azure Blockchain Workbench Preview 資料庫和資料庫伺服器資訊。
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015480"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>取得 Azure Blockchain Workbench 資料庫的相關資訊

本文說明如何取得 Azure Blockchain Workbench Preview 資料庫的詳細資訊。

## <a name="overview"></a>概觀

使用 Blockchain Workbench SQL DB 中的資料庫檢視可提供應用程式、工作流程與智慧合約執行的相關資訊。 開發人員可以在使用如 Microsoft Excel、Power BI、Visual Studio 及 SQL Server Management Studio 等工具時運用此資訊。

開發人員在連線到資料庫之前，必須具備：

* 資料庫防火牆中允許的外部用戶端存取權。 本文與設定資料庫防火牆有關，說明如何允許存取權。
* 資料庫伺服器名稱與資料庫名稱。

## <a name="connect-to-the-blockchain-workbench-database"></a>連線至 Blockchain Workbench 資料庫

若要連線至資料庫：

1. 使用具有 Azure Blockchain Workbench 資源 **擁有** 者許可權的帳戶登入 Azure 入口網站。
2. 在左側導覽窗格中，選取 [資源群組]。
3. 為 Blockchain Workbench 部署選擇資源群組的名稱。
4. 選取 [類型] 排序資源清單，然後選擇您的 **SQL 伺服器**。 下一個螢幕擷取畫面中已排序的清單會顯示兩個資料庫： "master"，另一個則使用 "lhgn" 作為 **資源前置** 詞。

   ![排序後的 Blockchain Workbench 資源清單](./media/getdb-details/sorted-workbench-resource-list.png)

5. 若要檢視 Blockchain Workbench 資料庫的相關詳細資訊，請選取具有您部署 Blockchain Workbench 時所提供 **資源前置詞** 的資料庫連結。

   ![資料庫詳細資料](./media/getdb-details/workbench-db-details.png)

資料庫伺服器名稱與資料庫名稱可讓您使用開發或報告工具連線至 Blockchain Workbench 資料庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的資料庫檢視](database-views.md)