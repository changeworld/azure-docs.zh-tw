---
title: 瞭解 Azure Stack Edge Pro 裝置上的主動式記錄收集
description: 描述如何在 Azure Stack Edge Pro 裝置上進行主動式記錄收集。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466175"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge 裝置上的主動式記錄收集

您可以根據系統健康情況指標，在 Azure Stack Edge 裝置上啟用主動式收集記錄，以協助有效地疑難排解任何裝置問題。 本文說明何謂主動式記錄收集、如何啟用它，以及啟用主動式記錄收集時如何處理資料。
   
本文中的資訊適用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。

## <a name="about-proactive-log-collection"></a>關於主動式記錄檔收集

Microsoft 客戶支援和工程小組會使用您 Azure Stack Edge 裝置中的系統記錄，有效率地找出並修正作業期間可能會發生的問題。 主動式記錄收集是警示 Microsoft 問題/事件 (查看客戶的 Azure Stack Edge 設備偵測到所追蹤之事件) 主動式記錄收集指標區段的方法。 有關此問題的支援記錄會自動上傳至 Microsoft 所管理和控制的 Azure 儲存體帳戶。 Microsoft 支援服務與 Microsoft 工程師審查這些支援記錄，以判斷解決客戶問題的最佳做法。    

> [!NOTE]
> 這些記錄僅用於進行調試，並在發生問題時提供客戶支援。 


## <a name="enabling-proactive-log-collection"></a>啟用主動收集記錄

您可以在嘗試透過本機 UI 啟用裝置時啟用主動式記錄檔收集。 

1. 在裝置的本機 Web UI 中，移至 **開始使用** 頁面。
2. 在 **啟用** 圖格上，選取 [啟動]。 

    ![本機 web UI 的 [雲端詳細資料] 第1頁](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. 在 [ **啟動** ] 窗格中：
    1. 輸入您取得的 **啟用金鑰** ，以 [取得 Azure Stack Edge Pro R 的啟用金鑰](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)。

    1. 您可以啟用主動式記錄收集，讓 Microsoft 根據裝置的健康狀態來收集記錄。 以這種方式收集的記錄會上傳至 Azure 儲存體帳戶。
    
    1. 選取 [套用]。

    ![本機 Web UI 的「雲端詳細資料」頁面 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>主動式記錄檔收集指示器

啟用主動式記錄檔收集之後，會在裝置上偵測到下列其中一個事件時自動上傳記錄：  


|警示/錯誤/條件  |描述  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 一致的服務狀況不良。         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge 代理程式未執行。         |
|UpdateInstallFailedEvent | 無法安裝更新。        |

 
Microsoft 會繼續將新的事件新增至上述清單。 新的事件不需要額外的同意。 請參閱此頁面，以瞭解最新的主動式記錄收集指示器。    
 

## <a name="other-log-collection-methods"></a>其他記錄檔收集方法

除了主動收集記錄（收集與特定問題有關的特定記錄）之外，還有其他記錄檔集合可以更深入瞭解系統健康情況和行為。 這些其他記錄收集通常可以在支援要求期間執行，或由 Microsoft 根據裝置提供的遙測資料來觸發。  

## <a name="handling-data"></a>處理資料

如果客戶啟用了主動式記錄收集，則會同意 Microsoft 從 Azure Stack Edge 裝置收集記錄，如本文所述。 客戶也會在 Microsoft 所管理和控制的 Azure 儲存體帳戶中，確認並同意這些記錄檔的上傳和保留。

Microsoft 只會使用資料來針對系統健康情況和問題進行疑難排解。 資料不會用於行銷、廣告或任何其他商業目的，而不需要客戶同意。 

Microsoft 收集的資料會依照我們的標準隱私權實務來處理。 如果客戶決定撤銷其對主動式記錄檔收集的同意，則在撤銷之前以同意收集的任何資料都不會受到影響。

## <a name="next-steps"></a>後續步驟

瞭解如何 [收集支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)。