---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: c315c071aeb36eea0bd1af84b1344cf2fef5f703
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329649"
---
>[!NOTE]
>對於未修正的資源，請開啟支援票證以要求增加配額。 請勿為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

| 資源 | 限制 | 
| --- | --- | 
| 單一訂用帳戶中的 Azure 媒體服務帳戶 | 25 (固定) |
| 每個媒體服務帳戶的媒體保留單元 |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| 每個媒體服務帳戶的工作 | 50,000<sup>2</sup> |
| 每個作業的鏈結工作 | 30 (固定) |
| 每個媒體服務帳戶的資產 | 1,000,000|
| 每個工作的資產 | 50 |
| 每個作業的資產 | 100 |
| 一次與資產相關聯的唯一定位器 | 5<sup>4</sup> |
| 每個媒體服務帳戶的即時通道 |5|
| 每個通道中已停止狀態的程式  |50|
| 每個通道中執行中的程式  |3|
| 每個媒體服務帳戶的串流端點 (已停止或執行中)|2|
| 每個串流端點的資料流單位  |10 |
| 儲存體帳戶 | 100<sup>5</sup> (已修正) |
| 原則 | 1,000,000<sup>6</sup> |
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。<sup>7</sup> |

<sup>1</sup>如果您變更類型 (例如，從 S2 到 S1)，就會重設保留單位的上限。

<sup>2</sup>這個數字包括佇列、已完成、作用中和已取消的作業。 不包含已刪除的作業。 您可以使用 **IJob.Delete**或 **DELETE** HTTP 要求刪除舊作業。

自 2017 年 4 月 1 日起，您的帳戶中任何超過 90 天的作業記錄以及其相關工作記錄都會自動刪除。 即使記錄總數低於配額上限，仍會執行自動刪除。 若要封存作業和工作資訊，可以使用[使用媒體服務 .NET SDK 管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md)中說明的程式碼。

<sup>3</sup>提出要求來列出作業實體時，每個要求會傳回最多 1000 個作業。 若要繼續追蹤所有已送出的作業，可以使用 top 或 skip 查詢 (如 [OData 系統查詢選項](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中所述)。

<sup>4</sup>定位器並非設計來管理每個使用者的存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 解決方案。 如需詳細資訊，請參閱[使用 Azure 媒體服務保護您的內容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>6</sup>不同的媒體服務原則限制為 1,000,000 個原則。 例如，定位器原則或 ContentKeyAuthorizationPolicy 的範例。 

>[!NOTE]
> 如果您一律使用相同的天數和存取權限，請使用相同的原則識別碼。 如需詳細資訊和範例，請參閱[使用媒體服務 .NET SDK 來管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>目前，Azure Blob 儲存體中單一 Blob 支援的大小上限為 5 TB。 其他限制會根據服務所使用的 VM 大小，套用在 Azure 媒體服務中。 大小限制適用於您所上傳的檔案，以及因媒體服務處理 (編碼或分析) 而產生的檔案。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表顯示媒體保留單元 (S1、S2 和 S3) 上的限制。 如果來源檔案大於資料表中定義的限制，編碼作業就會失敗。 如果您要編碼長時間的 4k 解析來源，就必須使用 S3 媒體保留單位來達成所需的效能。 如果您的4K 內容大於 S3 媒體保留單元的 260 GB 限制，請開啟支援票證。

|媒體保留單元類型    |輸入大小上限 (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
