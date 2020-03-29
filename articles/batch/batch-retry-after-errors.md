---
title: 錯誤後重試任務
description: 檢查錯誤並重試。
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919986"
---
# <a name="detecting-and-handling-batch-service-errors"></a>檢測和處理批次處理服務錯誤

請務必記住，在使用 REST 服務 API 時，應檢查錯誤。 運行批次處理作業時發生錯誤的情況並不少見。

## <a name="common-errors"></a>常見錯誤 

- 網路故障 - 這些請求從未到達批次處理或批次處理回應未及時到達用戶端。
- 內部伺服器錯誤 - 這些是標準 5xx 狀態碼 HTTP 回應。
- 限制可能會導致錯誤，如 429 或 503 狀態碼 HTTP 回應與重試後標頭。
- 包含"已存在"和"無效操作"等錯誤的 4xx 錯誤。 這意味著資源未處於狀態轉換的正確狀態。

有關各種類型錯誤代碼和特定錯誤代碼的詳細資訊，請參閱[批次狀態和錯誤代碼](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="when-to-retry"></a>何時重試

如果發生故障，批次處理 API 將通知您。 它們都可以重試，並且它們都包括用於此目的的全域重試處理常式。 最好使用此內置機制。

失敗後，您應該等待一段時間（重試之間的幾秒鐘），然後再重試。 如果重試過於頻繁或過快，重試處理常式將限制。

### <a name="for-more-information"></a>取得詳細資訊  

[批次處理 API 和工具](batch-apis-tools.md)連結到 API 參考資訊。 例如，.NET API 具有[重試策略提供程式類]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)，其中應指定所需的重試策略。 

有關每個 API 及其預設重試策略的詳細資訊，請閱讀[批次處理狀態和錯誤代碼](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。
