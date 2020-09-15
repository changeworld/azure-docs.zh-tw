---
title: 在 Microsoft Power BI 中使用 Azure Blockchain Workbench 資料
description: 了解如何在 Microsoft Power BI 中載入與檢視 Azure Blockchain Workbench SQL DB 資料。
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532370"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>將 Azure Blockchain Workbench 資料搭配 Microsoft Power BI 使用

Microsoft Power BI 能讓您使用 Power BI Desktop，輕鬆地從 SQL DB 資料庫產生強大的報表，然後將它們發行至 [https://www.powerbi.com](https://www.powerbi.com) 。

本文包含如何從 PowerBI 桌面連線至 Azure Blockchain Workbench SQL Database、建立報告，並將報告部署至 powerbi.com 的逐步解說。

## <a name="prerequisites"></a>必要條件

* 下載 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)。

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>將 Power BI 連線至 Azure Blockchain Workbench 中的資料

1.  開啟 Power BI Desktop。
2.  選取 [ **取得資料**]。

    ![取得資料](./media/data-powerbi/get-data.png)
3.  從資料來源類型中選取 [SQL Server]****。

4.  在對話方塊中提供伺服器與資料庫名稱。 指定您要匯入資料，或執行 **DirectQuery**。 選取 [確定]。

    ![選取 SQL Server](./media/data-powerbi/select-sql.png)

5.  提供存取 Azure Blockchain Workbench 的資料庫認證。 選取 [資料庫]****，然後輸入您的認證。

    如果您使用的是由 Azure Blockchain Workbench 部署程序建立的認證，則使用者名稱為 **dbadmin**，且密碼為您在部署期間提供的密碼。

    ![SQL DB 設定](./media/data-powerbi/db-settings.png)

6.  一旦連線至資料庫時，[導覽列]**** 對話方塊會顯示資料庫內可用的資料表與檢視。 檢視是針對報告所設計，所有檢視名稱的前面都會加上 **vw**。

    ![Power BI desktop 的螢幕擷取畫面，其中已選取 [導覽器] 對話方塊並選取 [vwContractAction]。](./media/data-powerbi/navigator.png)

7.  選取您要包含的檢視。 為了示範，我們包含 **vwContractAction** 它會提供在合約上已發生動作的詳細資料。

    ![選取檢視](./media/data-powerbi/select-views.png)

現在您可以像平常使用 Power BI 一般地建立與發佈報告。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的資料庫檢視](database-views.md)