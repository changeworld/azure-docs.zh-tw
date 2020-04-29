---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334864"
---
>[!NOTE]
>對於未修正的資源，請開啟支援票證以要求增加配額。 在嘗試取得更高的限制時，請勿建立額外的 Azure 媒體服務帳戶。

| 資源 | 限制 | 
| --- | --- | 
| 在單一訂用帳戶中 Azure 媒體服務帳戶 | 25 (固定) |
| 每個媒體服務帳戶的媒體保留單元 |25 (S1)<br/>10（S2、S3）<sup>1</sup> | 
| 每個媒體服務帳戶的工作 | 50,000<sup>2</sup> |
| 每個作業的鏈結工作 | 30 (固定) |
| 每個媒體服務帳戶的資產 | 1,000,000|
| 每個工作的資產 | 50 |
| 每個作業的資產 | 100 |
| 一次與資產相關聯的唯一定位器 | 5<sup>4</sup> |
| 每個媒體服務帳戶的直播頻道 |5|
| 每個通道中已停止狀態的程式  |50|
| 每個通道中執行中的程式  |3|
| 每個媒體服務帳戶停止或執行的串流端點|2|
| 每個串流端點的資料流單位  |10 |
| 儲存體帳戶 | 1,000<sup>5</sup> (fixed) |
| 原則 | 1,000,000<sup>6</sup> |
| 檔案大小| 在某些情況下，媒體服務中支援處理的檔案大小上限有所限制。<sup>7</sup> |

<sup>1</sup>例如，如果您將類型從 S2 變更為 S1，則會重設保留單位限制的最大值。

<sup>2</sup>此數目包括已佇列、已完成、作用中和已取消的工作。 它不包含已刪除的工作。 您可以使用**IJob**或**delete** HTTP 要求來刪除舊的作業。

從2017年4月1日起，您帳戶中任何超過90天的作業記錄都會自動刪除，以及其相關聯的工作記錄。 即使記錄總數低於配額上限，也會進行自動刪除。 若要封存作業和工作資訊，請使用透過[媒體服務 .NET SDK 管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md)中所述的程式碼。

<sup>3</sup>當您提出要求來列出工作實體時，每個要求會傳回最多1000個作業。 若要追蹤所有已提交的作業，請使用 [top] 或 [略過] 查詢，如[OData 系統查詢選項](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))中所述。

<sup>4</sup>定位器並非設計來管理每個使用者的存取控制。 若要為個別使用者提供不同的存取權限，請使用數位版權管理（DRM）解決方案。 如需詳細資訊，請參閱[使用 Azure 媒體服務保護您的內容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>6</sup>針對不同的媒體服務原則，有1000000個原則的限制。 例如定位器原則或 ContentKeyAuthorizationPolicy 的範例。 

>[!NOTE]
> 如果您一律使用相同的日期和存取權限，請使用相同的原則識別碼。 如需詳細資訊和範例，請參閱[使用媒體服務 .NET SDK 來管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>單一 blob 支援的大小上限目前在 Azure Blob 儲存體中最多為 5 TB。 根據服務所使用的 VM 大小，媒體服務中會套用額外的限制。 大小限制適用于您上傳的檔案，以及因媒體服務處理（編碼或分析）而產生的檔案。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表顯示媒體保留單元 S1、S2 和 S3 的限制。 如果您的原始程式檔大於資料表中所定義的限制，則編碼工作會失敗。 如果您編碼長時間的4K 解析來源，就必須使用 S3 媒體保留單元來達到所需的效能。 如果您的4K 內容大於 S3 媒體保留單元的 260 GB 限制，請開啟支援票證。

|媒體保留單元類型    |輸入大小上限（GB）|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
