---
title: 發生錯誤後重試工作
description: 檢查是否有錯誤，然後再試一次。
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116531"
---
# <a name="detecting-and-handling-batch-service-errors"></a>偵測和處理 Batch 服務錯誤

使用 REST 服務 API 時，請務必記得檢查是否有錯誤。 執行批次作業時，錯誤通常不會發生。

## <a name="common-errors"></a>常見錯誤 

- 網路失敗-這些是從未達到批次的要求，或批次回應未及時到達用戶端。
- 內部伺服器錯誤-這些是標準5xx 狀態碼 HTTP 回應。
- 節流可能會導致429或503狀態碼 HTTP 回應的錯誤，並包含重試之後的標頭。
- 4xx 錯誤，其中包括 AlreadyExists 和 InvalidOperation 這類錯誤。 這表示資源不是處於狀態轉換的正確狀態。

如需各種錯誤碼類型和特定錯誤碼的詳細資訊，請參閱[批次狀態和錯誤碼](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="when-to-retry"></a>重試的時機

Batch Api 會在發生失敗時通知您。 它們全都可以重試，而且它們全都包含該用途的全域重試處理常式。 最佳做法是使用此內建機制。

失敗之後，您應該等候一段時間（重試之間數秒），然後再重試。 如果您太過頻繁或太快重試，重試處理常式將會節流。

### <a name="for-more-information"></a>取得詳細資訊  

[Batch api 和工具](batch-apis-tools.md)會連結至 API 參考資訊。 例如，.NET API 有一個[RetryPolicyProvider 類別]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet)，其中應指定必要的重試原則。 

如需每個 API 及其預設重試原則的詳細資訊，請參閱[批次狀態和錯誤碼](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes)。
