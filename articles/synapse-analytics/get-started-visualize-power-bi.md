---
title: 教學課程：開始使用 Azure Synapse Analytics - 使用 Power BI 將工作區資料視覺化
description: 在此教學課程中，您將了解如何建立 Power BI 工作區、連結 Azure Synapse 工作區，以及建立 Power BI 資料集來利用 Azure Synapse 工作區中的資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208959"
---
# <a name="visualize-data-with-power-bi"></a>使用 Power BI 視覺化資料

在此教學課程中，您將了解如何建立 Power BI 工作區、連結 Azure Synapse 工作區，以及建立 Power BI 資料集來利用 Azure Synapse 工作區中的資料。 

> [!NOTE]
> 若要完成本教學課程，請 [安裝 Power BI Desktop](https://aka.ms/pbidesktopstore)。

## <a name="overview"></a>概觀

從 NYC 計程車資料中，我們在兩個資料表中建立了彙總的資料集：
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

您可以將 Power BI 工作區連結至您的 Azure Synapse 工作區。 此功能可讓您輕鬆地將資料放入 Power BI 工作區。 您可以直接在 Azure Synapse 工作區中編輯您的 Power BI 報表。 

### <a name="create-a-power-bi-workspace"></a>建立 Power BI 工作區

1. 登入 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 按一下 [ **工作區**]，然後選取 [ **建立工作區**]。 建立名為 **NYCTaxiWorkspace1** 或類似的新 Power BI 工作區，因為此名稱必須是唯一的。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>將您的 Azure Synapse 工作區連結至您新的 Power BI 工作區

1. 在 Synapse Studio 中，移至 [管理] > [連結服務]。
1. 選取  >  **[新增連接至 Power BI]**。
1. 將 **名稱** 設定為 **NYCTaxiWorkspace1**。
1. 將 **工作區名稱** 設定為您在上面建立的 Power BI 工作區，類似于 **NYCTaxiWorkspace1**。
1. 選取 [建立]。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>建立在 Azure Synapse 工作區中使用資料的 Power BI 資料集

1. 在 Synapse Studio 中，移至 [開發] > [Power BI]。
1. 移至 [NYCTaxiWorkspace1] > [Power BI 資料集]，然後選取 [新增 Power BI 資料集]。 按一下 [啟動]。
1. 選取 **SQLPOOL1** 資料來源，然後按一下 [ **繼續**]。
1. 按一下 [ **下載** ] 以下載 **NYCTaxiWorkspace1SQLPOOL1 .pbids** 檔案的 .pbids 檔案。 按一下 [繼續] 。
1. 開啟下載的 **.pbids** 檔案。 Power BI Desktop 會開啟，並自動連接到您 Azure Synapse 工作區中的 **SQLDB1** 。
1. 如果您看到名為 **SQL Server database** 的對話方塊：
    1. 選取 [Microsoft 帳戶]。
    1. 選取 [登入] 並登入您的帳戶。
    1. 選取 [連接]。
1. [導覽器] 對話方塊開啟之後，請檢查 **PassengerCountStats** 資料表，然後選取 [載入]。
1. 出現 [連線設定] 對話方塊之後，請選取 [DirectQuery] > [確定]。
1. 選取左側的 [報告] 按鈕。
1. 在 [ **視覺效果**] 下，按一下 [折線圖] 圖示，將 **折線圖** 加入至報表。
    1. 在 [**欄位**] 下，將 [ **PassengerCount** ] 資料行拖曳到 [**視覺效果**]  >  **軸**
    1. 將 [SumTripDistance] 和 [AvgTripDistance] 資料行拖曳到 [視覺效果] > [值]。
1. 在 [首頁] 索引標籤中，選取 [發佈]。
1. 選取 [儲存] 來儲存變更。
1. 選擇檔案名稱 **PassengerAnalysis.pbix**，然後選取 [儲存]。
1. 在 [ **發佈至 Power BI** ] 視窗的 [ **選取目的地**] 底下，選擇您的 **NYCTaxiWorkspace1**，然後按一下 [ **選取**]。
1. 等待發佈完成。 

### <a name="configure-authentication-for-your-dataset"></a>設定資料集的驗證

1. 開啟 [powerbi.microsoft.com](https://powerbi.microsoft.com/)，然後 **登入**。
1. 在左側的 [工作區] 下，選取 [NYCTaxiWorkspace1] 工作區。
1. 在該工作區中，找出名為 [乘客分析 (Passenger Analysis)] 的資料集，以及名為 [乘客分析 (Passenger Analysis)] 的報表。
1. 將滑鼠停留在 [乘客分析 (Passenger Analysis)] 資料集上，選取省略符號 (...) 按鈕，然後選取 [設定]。
1. 在 [ **資料來源認證**] 中，按一下 [ **編輯**]，將 **驗證方法** 設定為 [ **OAuth2**]，然後選取 [登 **入**]。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中編輯報告

1. 返回 Synapse Studio，然後選取 [關閉並重新整理]。
1. 移至 [開發] 中樞。
1. 在 [ **Power BI** 圖層的右邊，省略號 ( ...] ) 按鈕， **然後按一下 [** 重新整理] 以重新整理 [ **Power BI 報表** ] 節點。
1. 在 **Power BI** 底下，您應該會看到：
    * 在 **NYCTaxiWorkspace1**  >  **Power BI 資料集**，這是名為 **PassengerAnalysis** 的新資料集。
    * 在 [NYCTaxiWorkspace1] > [Power BI 報告] 下，新的報告稱為 [PassengerAnalysis]。
1. 選取 [PassengerAnalysis] 報告。 報表會隨即開啟，而且您可以直接在 Synapse Studio 內進行編輯。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視](get-started-monitor.md)
                                 

