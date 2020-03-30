---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334864"
---
>[!NOTE]
>對於未修復的資源，請打開一個支援票證，要求增加配額。 不要創建其他 Azure 媒體服務帳戶以嘗試獲取更高的限制。

| 資源 | 限制 | 
| --- | --- | 
| 單個訂閱中的 Azure 媒體服務帳戶 | 25 (固定) |
| 每個媒體服務帳戶的媒體預留單位 |25 (S1)<br/>10 （S2， S3）<sup>1</sup> | 
| 每個媒體服務帳戶的工作 | 50,000<sup>2</sup> |
| 每個作業的鏈結工作 | 30 (固定) |
| 每個媒體服務帳戶的資產 | 1,000,000|
| 每個工作的資產 | 50 |
| 每個作業的資產 | 100 |
| 一次與資產相關聯的唯一定位器 | 5<sup>4</sup> |
| 每個媒體服務帳戶的即時頻道 |5|
| 每個通道中已停止狀態的程式  |50|
| 每個通道中執行中的程式  |3|
| 每個媒體服務帳戶停止或運行的流式處理終結點|2|
| 每個串流端點的資料流單位  |10 |
| 儲存體帳戶 | 1,000<sup>5</sup> (fixed) |
| 原則 | 1,000,000<sup>6</sup> |
| 檔案大小| 在某些情況下，在媒體服務中支援的最大檔案大小有限制。<sup>7</sup> |

<sup>1</sup>如果將類型（例如，從 S2 更改為 S1），則將重置最大預留單位限制。

<sup>2</sup>此數位包括排隊、已完成、處於活動狀態和取消的作業。 它不包括已刪除的作業。 您可以使用**IJob.Delete**或刪除**HTTP**請求刪除舊作業。

自 2017 年 4 月 1 日起，您的帳戶中任何超過 90 天的作業記錄及其關聯的任務記錄將自動刪除。 即使記錄總數低於最大配額，也會發生自動刪除。 要存檔作業和任務資訊，請使用["管理資產"中描述的代碼與媒體服務 .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md)。

<sup>3</sup>當您請求列出作業實體時，每個請求最多返回 1，000 個作業。 要跟蹤所有提交的作業，請使用頂部或跳過[OData 系統查詢選項](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中描述的查詢。

<sup>4</sup>定位器不是用於管理每個使用者的存取控制。 要向單個使用者授予不同的存取權限，請使用數位版權管理 （DRM） 解決方案。 有關詳細資訊，請參閱使用[Azure 媒體服務保護內容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>存儲帳戶必須來自同一 Azure 訂閱。

<sup>6</sup>對於不同的媒體服務策略，有 1，000，000 個策略的限制。 例如定位器策略或內容金鑰授權策略。 

>[!NOTE]
> 如果始終使用相同的日期和存取權限，請使用相同的策略 ID。 有關資訊和示例，請參閱[使用媒體服務 .NET SDK 管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>在 Azure Blob 存儲中，單個 Blob 支援的最大大小當前最多為 5 TB。 其他限制適用于基於服務使用的 VM 大小的媒體服務。 大小限制適用于您上傳的檔，以及由於媒體服務處理（編碼或分析）而生成的檔。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表顯示了媒體保留單元 S1、S2 和 S3 的限制。 如果原始檔案大於表中定義的限制，則編碼作業將失敗。 如果對持續時間長的 4K 解析度源進行編碼，則需要使用 S3 介質保留單元來實現所需的性能。 如果您的 4K 內容大於 S3 媒體保留單元上的 260 GB 限制，則打開支援票證。

|介質保留單位類型    |最大輸入大小 （GB）|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
