---
title: Azure 媒體服務高可用性編碼
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
ms.openlocfilehash: f5b02376111a3deba33cd5688330018bd7c370d8
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899213"
---
# <a name="media-services-high-availability-encoding"></a>媒體服務高可用性編碼 

Azure 媒體服務編碼服務是區域批次處理平臺，目前不是針對單一區域內的高可用性所設計。 如果基礎元件或相依服務（例如儲存體、SQL）發生區域資料中心中斷或失敗，則編碼服務目前不會提供服務的立即容錯移轉。 本文說明如何部署媒體服務以維護具有容錯移轉的高可用性架構，並確保應用程式的最佳可用性。

遵循本文中所述的指導方針和最佳作法，您會降低編碼失敗的風險、延遲，以及在單一區域發生中斷時，將復原時間降到最低。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>如何建立跨區域編碼系統

* [建立](create-account-cli-how-to.md)兩個（或更多） Azure 媒體服務帳戶。

    這兩個帳戶必須位於不同的區域。 如需詳細資訊，請參閱[部署 Azure 媒體服務服務的區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 將您的媒體上傳到您計畫提交作業所在的相同區域。 如需如何開始編碼的詳細資訊，請參閱[從 HTTPS URL 建立作業輸入](job-input-from-http-how-to.md)或[從本機檔案建立作業輸入](job-input-from-local-file-how-to.md)。

    如果您接著需要將[作業](transforms-jobs-concept.md)重新提交至另一個區域，您可以使用 JobInputHttp 或使用[copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) ，將資料從來源資產容器複製到替代區域中的資產容器。
* 透過 Azure 事件方格，訂閱每個帳戶中的 JobStateChange 訊息。 如需詳細資訊，請參閱

    * [音訊分析範例，示範](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)如何使用 Azure 事件方格監視作業，包括新增回復，以防 Azure 事件方格訊息因某些原因而延遲。
    * [適用于媒體服務事件的 Azure 事件方格架構](media-services-event-schemas.md)
    * 透過[Azure 入口網站或 CLI 註冊事件](reacting-to-media-services-events.md)（您也可以使用 EVENTGRID Management SDK 來執行）
    * [EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （其以原生方式支援媒體服務事件）。

    您也可以透過 Azure Functions 使用事件方格事件。
*    當您建立[作業](transforms-jobs-concept.md)時：
    
    * 從目前使用的帳戶清單中隨機選取帳戶（此清單通常會包含這兩個帳戶，但如果偵測到問題，可能只會包含一個帳戶）。 如果清單是空的，請引發警示，讓操作員可以進行調查。
    * 一般指引是每個[JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)都需要一個[媒體保留單元](media-reserved-units-cli-how-to.md)（除非您使用[VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) ，建議每個 JobOutput 媒體保留單位為3個）。
    * 取得所選帳戶的媒體保留單元（Mru）計數。 如果目前的**媒體保留單元**計數尚未達到最大值，請加入作業所需的 mru 數目，並更新服務。 如果您的作業提交速率很高，而且您經常查詢 Mru 來尋找最大值，請使用分散式快取，以取得合理的時間長度。
    * 保留傳遞作業數目的計數。

* 當您的 JobStateChange 處理常式收到工作已達到排程狀態的通知時，請記錄它進入排程狀態的時間，以及所使用的區域/帳戶。
* 當您的 JobStateChange 處理常式收到工作已達到處理狀態的通知時，請將作業的記錄標記為「處理中」。
* 當您的 JobStateChange 處理常式收到作業已達到已完成/錯誤/已取消狀態的通知時，請將作業的記錄標記為最終，並遞減傳遞作業計數。 取得所選帳戶的媒體保留單元數，並比較目前的 MRU 數位與您的傳遞作業計數。 如果您的傳遞計數小於 MRU 計數，請將其遞減並更新服務。
* 有個別的程式，可定期查看作業的記錄
    
    * 如果您的工作處於 [已排程] 狀態，但在指定區域的合理時間內尚未前進到處理狀態，請從您目前使用的帳戶清單中移除該區域。  根據您的業務需求，您可以決定立即取消這些作業，並將它們重新提交至另一個區域。 或者，您可以提供更多時間來移至下一個狀態。
    * 視帳戶上所設定的媒體保留單元數目和提交速率而定，也可能會有佇列狀態的作業，表示系統尚未挑選處理。  如果排入佇列狀態中的工作清單在區域中成長超過可接受的限制，則可以取消這些作業，並將其提交至另一個區域。  不過，這可能是沒有在帳戶上設定足夠的媒體保留單位來進行目前負載的徵兆。  如有需要，您可以透過 Azure 支援要求更高的媒體保留單元配額。
    * 如果已從帳戶清單中移除某個區域，請在將其新增回清單之前，監視它的復原。  區域健康情況可以透過區域中的現有作業（如果未取消並重新提交）來監視，方法是在一段時間後將帳戶新增回清單，並由操作員監視可能會影響之中斷的 Azure 通訊Azure 媒體服務。
    
如果您發現 MRU 計數已經過多而降低，請將遞減邏輯移到定期工作。 讓前置作業提交邏輯比較傳遞計數與目前的 MRU 計數，以查看是否需要更新 Mru。

## <a name="next-steps"></a>後續步驟

* [建立隨選影片跨區域串流](media-services-high-availability-streaming.md)
* 查看程式[代碼範例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
