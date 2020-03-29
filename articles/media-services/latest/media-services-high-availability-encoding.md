---
title: Azure 媒體服務高可用性編碼
description: 瞭解如何在區域資料中心中斷或發生故障時容錯移轉到輔助媒體服務帳戶。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934189"
---
# <a name="media-services-high-availability-encoding"></a>媒體服務高可用性編碼 

Azure 媒體服務編碼服務是一個區域批次處理平臺，當前不是為單個區域內的高可用性而設計的。 如果基礎元件或從屬服務（如存儲、SQL）發生區域資料中心中斷或故障，編碼服務當前不會提供服務的即時容錯移轉。 本文介紹如何部署媒體服務，以維護具有容錯移轉的高可用性體系結構，並確保應用程式的最佳可用性。

通過遵循本文中描述的指南和最佳實踐，您將降低編碼失敗、延遲的風險，並在單個區域中發生中斷時將恢復時間降至最低。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何構建跨區域編碼系統

* [創建](create-account-cli-how-to.md)兩個（或更多）Azure 媒體服務帳戶。

    這兩個帳戶需要在不同的區域。 有關詳細資訊，請參閱部署[Azure 媒體服務的區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 將媒體上載到計畫提交作業的同一區域。 有關如何開始編碼的詳細資訊，請參閱[從 HTTPS URL 創建作業輸入](job-input-from-http-how-to.md)或[從本地檔創建作業輸入](job-input-from-local-file-how-to.md)。

    如果隨後需要將[作業](transforms-jobs-concept.md)重新提交到其他區域，則可以使用 JobInputHttp 或使用[Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)將資料從源資產容器複製到備用區域中的資產容器。
* 通過 Azure 事件網格訂閱每個帳戶中的作業狀態更改消息。 如需詳細資訊，請參閱

    * [音訊分析示例](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)演示如何使用 Azure 事件網格監視作業，包括添加回退，以防 Azure 事件網格消息因某種原因延遲。
    * [Azure 媒體服務事件的 Azure 事件格線結構描述](media-services-event-schemas.md)
    * [通過 Azure 門戶或 CLI 註冊事件](reacting-to-media-services-events.md)（您也可以使用事件網格管理 SDK 進行註冊）
    * [Microsoft.Azure.EventGrid SDK（](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)支援本地媒體服務事件）。

    還可以通過 Azure 函數使用事件網格事件。
* 創建[作業](transforms-jobs-concept.md)時：

    * 從當前使用的帳戶清單中隨機播放一個帳戶（此清單通常包含兩個帳戶，但如果檢測到問題，則可能只包含一個帳戶）。 如果清單為空，請引發警報，以便操作員可以進行調查。
    * 一般指導是，每個[作業輸出](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一個[介質保留單元](media-reserved-units-cli-how-to.md)（除非您使用[視頻Analyzer預設](analyzing-video-audio-files-concept.md)，其中建議每個作業輸出保留 3 個介質單元）。
    * 獲取所選帳戶的媒體保留單位 （MRUs） 計數。 如果當前**媒體預留單位**計數尚未達到最大值，請添加作業所需的 MRUs 編號並更新服務。 如果作業提交率較高，並且經常查詢 MRUS 以查找最大值，請使用具有合理超時的值的分散式緩存。
    * 保留機上作業數的計數。

* 當您的 JobStateChange 處理常式收到作業已達到計畫狀態的通知時，記錄其進入計畫狀態的時間以及所使用的區域/帳戶。
* 當 JobStateChange 處理常式收到作業已達到處理狀態的通知時，將作業的記錄標記為處理。
* 當您的 JobStateChange 處理常式收到作業已達到"已完成/錯誤/已取消"狀態的通知時，將作業的記錄標記為最終，並遞減機上作業計數。 獲取所選帳戶的媒體保留單位數，並將當前 MRU 編號與機上作業計數進行比較。 如果您的機上計數小於 MRU 計數，則將其取消並更新服務。
* 有一個單獨的流程，定期查看作業記錄
    
    * 如果給定區域的計畫狀態中作業未在合理時間內推進到處理狀態，請從當前使用的帳戶清單中刪除該區域。  根據您的業務需求，您可以決定立即取消這些作業，並將其重新提交到其他區域。 或者，您可以給他們更多的時間移動到下一個狀態。
    * 根據帳戶上配置的媒體保留單位的數量和提交率，也可能有處於系統尚未拾取處理的排隊狀態的作業。  如果排隊狀態中的作業清單超出區域中可接受的限制，則可以取消這些作業並提交到其他區域。  但是，這可能是未在帳戶上為當前負載配置足夠的媒體保留單元的症狀。  如有必要，可以通過 Azure 支援請求更高的媒體預留單位配額。
    * 如果從帳戶清單中刪除了區域，則監視其以進行恢復，然後再將其添加回清單中。  可以通過區域的現有作業（如果未取消並重新提交）監視區域運行狀況，在一段時間後將帳戶重新添加到清單中，以及通過操作員監視 Azure 通信中可能影響的中斷Azure 媒體服務。
    
如果您發現 MRU 計數在上下顛簸很多，則將遞減邏輯移動到定期任務。 讓作業前提交邏輯將機上計數與當前 MRU 計數進行比較，以查看是否需要更新 MRU。

## <a name="next-steps"></a>後續步驟

* [構建視頻點播跨區域流](media-services-high-availability-streaming.md)
* 查看[代碼示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
