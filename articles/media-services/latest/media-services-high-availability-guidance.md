---
title: Azure 媒體服務高可用性
description: 瞭解如果發生區域資料中心中斷或失敗，如何容錯移轉至次要媒體服務帳戶。
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202282"
---
# <a name="azure-media-services-high-availability-guidance"></a>Azure 媒體服務高可用性指引

Azure 媒體服務編碼服務是區域批次處理平臺，目前不是針對單一區域內的高可用性所設計。 如果發生區域資料中心中斷或基礎元件或相依服務（例如儲存體、SQL 等）失敗，則編碼服務目前不會提供服務的立即容錯移轉。 本文說明如何部署媒體服務以維護具有容錯移轉的高可用性架構，並確保應用程式的最佳可用性。 

遵循本文中所述的指導方針和最佳作法，您會降低編碼失敗的風險、延遲，以及在單一區域發生中斷時，將復原時間降到最低。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何建立跨區域編碼系統

* [建立](create-account-cli-how-to.md)兩個（或更多） Azure 媒體服務帳戶。
* 訂閱每個帳戶中的**JobStateChange**訊息。

    * 在媒體服務 v3 中，它是透過 Azure 事件方格來完成。 如需詳細資訊，請參閱
    
       * [事件方格範例](../../event-grid/receive-events.md)、 
       * [適用于媒體服務事件的 Azure 事件方格架構](media-services-event-schemas.md)， 
       * 透過[Azure 入口網站或 CLI 註冊事件](reacting-to-media-services-events.md)（您也可以使用 EVENTGRID Management SDK 來執行）
       * [EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （其以原生方式支援媒體服務事件）。 
       
        您也可以透過 Azure Functions 使用事件方格事件。
    * 在媒體服務 v2 中，這是透過[NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md)來完成。
* 當您[建立作業](transforms-jobs-concept.md)時：

    * 從目前使用的帳戶清單中隨機選取帳戶（此清單通常會包含這兩個帳戶，但如果偵測到問題，可能只會包含一個帳戶）。 如果清單是空的，請引發警示，讓操作員可以進行調查。
    * 一般指引是每個工作或[JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一個[媒體保留單元](media-reserved-units-cli-how-to.md)（除非您在 v3 中使用[VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) ）。
    * 取得所選帳戶的[媒體保留單元](media-reserved-units-cli-how-to.md)（mru）計數。 如果目前的**媒體保留單元**計數尚未達到最大值，請加入作業所需的 mru 數目，並更新服務。 如果您的作業提交速率很高，而且您經常查詢 Mru 來尋找最大值，請使用分散式快取，以取得合理的時間長度。
    * 保留傳遞作業數目的計數。
* 當您的 JobStateChange 處理常式收到工作已達到排程狀態的通知時，請記錄它進入排程狀態的時間，以及所使用的區域/帳戶。    
* 當您的 JobStateChange 處理常式收到工作已達到處理狀態的通知時，請將作業的記錄標記為「處理中」。
* 當您的 JobStateChange 處理常式收到作業已達到已完成/錯誤/已取消狀態的通知時，請將作業的記錄標記為最終，並遞減傳遞作業計數。 取得所選帳戶的媒體保留單元數，並比較目前的 MRU 數位與您的傳遞作業計數。 如果您的傳遞計數小於 MRU 計數，請將其遞減並更新服務。
* 有個別的程式，可定期查看作業的記錄。 如果您的工作處於 [已排程] 狀態，但在指定區域的合理時間內尚未前進到處理狀態，請從您目前使用的帳戶清單中移除該區域。

    * 根據您的業務需求，您可以決定立即取消這些作業，並將它們重新提交至另一個帳戶。 或者，您可以提供更多時間來移至下一個狀態。   
    * 經過一段時間之後，請將帳戶重新加入至目前使用的清單（假設該區域已復原）。
    
如果您發現 MRU 計數已經過多而降低，請將遞減邏輯移到定期工作。 讓前置作業提交邏輯比較傳遞計數與目前的 MRU 計數，以查看是否需要更新 Mru。

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何建立隨選影片跨區域串流 

* 隨選影片跨區域串流牽涉到複製[資產](assets-concept.md)、[內容金鑰原則](content-key-policy-concept.md)（如果使用）、[串流原則](streaming-policy-concept.md)和[串流定位器](streaming-locators-concept.md)。 
* 您必須在這兩個區域中建立原則，並將其保持在最新狀態。 
* 當您建立串流定位器時，您會想要使用相同的定位器識別碼值、ContentKey 識別碼值和 ContentKey 值。  
* 如果您要將內容編碼，建議您將區域 A 中的內容編碼並加以發佈，然後將編碼的內容複寫到區域 B，並使用與區域 A 相同的值加以發行。
* 您可以在來源和金鑰傳遞服務的主機名稱上使用「流量管理員」（在媒體服務設定中，這看起來就像自訂的金鑰伺服器 URL）。

## <a name="next-steps"></a>後續步驟

* [建立帳戶](create-account-cli-how-to.md)
* 查看程式[代碼範例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
