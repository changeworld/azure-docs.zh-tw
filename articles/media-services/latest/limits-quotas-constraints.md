---
title: Azure 媒體服務 v3 配額和限制 | Microsoft Docs
description: 本主題介紹 Microsoft Azure 媒體服務 v3 中的配額和限制。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888422"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure 媒體服務 v3 配額和限制

本文描述 Azure 媒體服務 v3 的配額和限制。

| 資源 | 預設限制 | 
| --- | --- | 
| 每個 Azure 媒體服務帳戶的資產 | 1,000,000|
| 動態資訊清單篩選條件|100|
| 每個工作的工作輸入 | 50 (固定)|
| 每個工作的工作輸出 | 20 (固定) |
| 轉換中的轉換輸出 | 20 (固定) |
| 每個 JobInput 的檔案|10 (固定)|
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 <sup>(1)</sup> |
| 每個媒體服務帳戶的工作 | 500,000 <sup>(2)</sup> (固定)|
| 每個媒體服務帳戶的即時事件 |5|
| 單一訂用帳戶的媒體服務帳戶 | 25 (固定) |
| 每個即時事件的即時輸出 |3 <sup>（3）</sup> |
| 最大即時輸出持續時間 | 25 小時 |
| 儲存體帳戶 | 100<sup>(4)</sup> (固定) |
| 每個媒體服務帳戶的串流端點 (已停止或執行中)|2 （固定）|
| 串流原則 | 100 <sup>（5）</sup> |
| 每個媒體服務帳戶的轉換 | 100 (固定)|
| 一次與資產相關聯的唯一串流定位器 | 100<sup>（6）</sup> （固定） |
| 每個內容金鑰策略的選項 |30 | 
| 每個帳戶的媒體服務金鑰交付服務上每個 DRM 類型的每月許可證|1,000,000|

<sup>1</sup> 單一 blob 支援的大小上限目前在 Azure Blob 儲存體是最多 5 TB。 其他限制適用于基於服務使用的 VM 大小的媒體服務。 大小限制適用于您上傳的檔，以及由於媒體服務處理（編碼或分析）而生成的檔。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表顯示了媒體保留單元 S1、S2 和 S3 的限制。 如果原始檔案大於表中定義的限制，則編碼作業將失敗。 如果對持續時間長的 4K 解析度源進行編碼，則需要使用 S3 介質保留單元來實現所需的性能。 如果您的 4K 內容大於 S3 媒體保留單元上的 260 GB 限制，則打開支援票證。

|介質保留單位類型   |最大輸入大小 （GB）|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup>此數位包括排隊、已完成、處於活動狀態和取消的作業。 不包含已刪除的工作。 

您的帳戶中任何超過 90 天的工作記錄，都會自動刪除，即使記錄總數低於配額上限亦然。 

<sup>3</sup>即時輸出從創建開始，刪除時停止。

<sup>4</sup> 儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>5</sup>使用自訂[流式處理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)時，應為媒體服務帳戶設計一組有限的此類策略，並在需要相同的加密選項和協定時將其重新用於流式處理器。 不建議您對每個串流定位器建立新的串流原則。

<sup>6</sup>流式處理器不是用於管理每個使用者的存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。

## <a name="support-ticket"></a>支援票證

對於未固定的資源，您可以通過打開[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)請求提高配額。 請在要求中包含所需的配額變更、使用案例情況及所需區域的詳細資訊。 <br/>**請勿**為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

## <a name="next-steps"></a>後續步驟

[概觀](media-services-overview.md)
