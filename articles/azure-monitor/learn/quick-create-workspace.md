---
title: 在 Azure 入口網站中建立 Log Analytics 工作區 | Microsoft Docs
description: 了解如何建立 Log Analytics 工作區，以在 Azure 入口網站中從您的雲端和內部部署環境啟用管理解決方案和資料收集。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: e6ef8fb7dfd98a509b0b89e049c70169dafbe134
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860557"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>在 Azure 入口網站中建立 Log Analytics 工作區
使用 [Log Analytics 工作區] 功能表，以使用 Azure 入口網站建立 Log Analytics 工作區。 Log Analytics 工作區是 Azure 監視器記錄資料的唯一環境。 每個工作區都有自己的資料存放庫與設定，而且資料來源和解決方案會設定為將其資料儲存在特定的工作區中。 如果您想從下列來源收集資料，就必須要有 Log Analytics 工作區：

* 訂用帳戶中的 Azure 資源
* System Center Operations Manager 監視的內部部署電腦
* 來自 Configuration Manager 的裝置集合 
* Azure 儲存體的診斷或記錄資料

針對其他來源，例如環境中的 Azure VM 和 Windows 或 Linux VM，請參閱下列主題：

*  [從 Azure 虛擬機器收集資料](../learn/quick-collect-azurevm.md) 
*  [從混合式 Linux 電腦收集資料](../learn/quick-collect-linux-computer.md)
*  [從混合式 Windows 電腦收集資料](quick-collect-windows-computer.md)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。 

## <a name="create-a-workspace"></a>建立工作區
1. 在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。

    ![Azure 入口網站](media/quick-create-workspace/azure-portal-01.png)
  
2. 按一下 [加入]，然後選取下列項目：

   * 為新的 [Log Analytics 工作區] 提供名稱，例如 DefaultLAWorkspace。 此名稱在所有 Azure 監視器訂用帳戶中必須是全域唯一的。
   * 如果選取的預設值不合適，請從下拉式清單中選取要連結的 [訂用帳戶]。
   * 針對 [資源群組]，選擇使用已設定的現有資源群組，或建立新的群組。  
   * 選取可用的**位置**。  如需詳細資訊，請參閱[可以使用 Log Analytics 的區域](https://azure.microsoft.com/regions/services/)，並從 [搜尋產品] 欄位搜尋 Azure 監視器。  
   * 如果您要在 2018 年 4 月 2 之後建立的新訂用帳戶中建立工作區，系統會自動使用「每 GB」定價方案和選項來選取將無法使用的定價層。  如果您要為在 4 月 2 日之前建立的現有訂用帳戶，或已繫結至現有 Enterprise 合約 (EA) 註冊的訂用帳戶建立工作區，請選取您偏好的定價層。  如需特定層的詳細資訊，請參閱 [Log Analytics 價格詳細資料](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![[建立 Log Analytics] 資源刀鋒視窗](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. 在 [Log Analytics 工作區] 頁面上提供必要資訊之後，按一下 [確定]。  

在驗證資訊及建立工作區時，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="troubleshooting"></a>疑難排解
當您建立在過去 14 天內刪除且處於[虛刪除狀態](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#soft-delete-behavior)的工作區時，根據您的工作區設定，此作業可能會有不同的結果：
1. 如果您提供的工作區名稱、資源群組、訂用帳戶和區域與已刪除工作區中的相同，您的工作區將會復原，包括其資料、設定和連接的代理程式。
2. 如果您使用相同的工作區名稱，但是不同的資源群組、訂用帳戶或區域，您將會收到錯誤：「此工作區名稱已在使用中。請嘗試另一個名稱。」 若要覆寫虛刪除並永久刪除您的工作區，並使用相同的名稱建立新的工作區，請遵循下列步驟先復原工作區，然後執行永久刪除：
   - [復原](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace)您的工作區
   - [永久刪除](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)您的工作區
   - 使用相同工作區名稱建立新的工作區

## <a name="next-steps"></a>後續步驟
有了可用的工作區之後，您可以設定監視遙測的集合、執行記錄搜尋以分析該資料，並且新增管理解決方案，以提供額外的資料和分析深入解析。 

* 若要從具有 Azure 診斷或 Azure 儲存體的 Azure 資源啟用資料收集，請參閱[收集 Azure 服務的記錄和計量以便使用於 Log Analytics](../platform/collect-azure-metrics-logs.md)。  
* [新增 System Center Operations Manager 作為資料來源](../platform/om-agents.md)，以從會報告 Operations Manager 管理群組的代理程式收集資料，並且將其儲存在 Log Analytics 工作區中。 
* 連線 [Configuration Manager](../platform/collect-sccm.md) 以匯入階層中集合成員的電腦。  
* 檢閱可用的[監視解決方案](../insights/solutions.md)，以及如何從您的工作區新增或移除解決方案。
