---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: a63f3e876ce3ec02f2e08b22a7712afb9b5479aa
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615856"
---
| 維度名稱 | 描述 |
| ------------------- | ----------------- |
| **GeoType** | 來自主要或次要叢集的交易。 可用的值包括 **主要** 和 **次要**。 在從次要租用戶讀取物件時，此維度會套用到讀取權限異地備援儲存體 (RA-GRS)。 |
| **ResponseType** | 交易回應類型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：描述項目之外的其他所有伺服器端錯誤 </li> <li>**ServerBusyError**：傳回 HTTP 503 狀態碼的已驗證要求。 </li> <li>**ServerTimeoutError**：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 逾時是因為伺服器錯誤而發生。 </li> <li>**AuthorizationError**：由於未經授權存取資料或授權失敗，從而發生失敗的已驗證要求。 </li> <li>**NetworkError**：由於網路錯誤而失敗的已驗證要求。 當用戶端在逾時到期前就過早關閉連線時，最常會發生這個情況。 </li><li>**ClientAccountBandwidthThrottlingError**：要求會針對超過 [儲存體帳戶可擴縮性限制](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，在頻寬上受到節流。</li><li>**ClientAccountRequestThrottlingError**：要求會針對超過 [儲存體帳戶可擴縮性限制](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，在要求速率上進行節流。<li>**ClientShareError**：其他用戶端節流錯誤。 已排除 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li><li>**ClientShareEgressThrottlingError**：僅適用于 premium 檔案共用。 其他用戶端節流錯誤。 要求失敗，因為超出共用限制的輸出頻寬節流。 ClientAccountBandwidthThrottlingError 已被排除。</li><li>**ClientShareIngressThrottlingError**：僅適用于 premium 檔案共用。 其他用戶端節流錯誤。 要求失敗，因為超過共用限制的輸入頻寬節流。 ClientAccountBandwidthThrottlingError 已被排除。</li><li>**ClientShareIopsThrottlingError**：僅適用于 premium 檔案共用。 其他用戶端節流錯誤。 要求失敗，因為 IOPS 節流。 ClientAccountRequestThrottlingError 已被排除。</li><li>**ClientTimeoutError**：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 如果用戶端的網路逾時或要求逾時設定為比儲存體服務預期的值還低，則此值是符合預期的逾時。 否則，它會回報為 ServerTimeoutError。 </li> <li>**ClientOtherError**：描述項目之外的其他所有用戶端錯誤。 </li> <li>**成功**：成功的要求</li> <li> **SuccessWithThrottling**：當 SMB 用戶端在第一次嘗試中受到節流，但是在重試之後成功時，成功的要求。</li><li> **SuccessWithShareEgressThrottling**：僅適用于 premium 檔案共用。 當 SMB 用戶端因為第一次嘗試)  (的輸出頻寬節流而受到節流處理，但在重試後成功時，便會成功要求。</li><li> **SuccessWithShareIngressThrottling**：僅適用于 premium 檔案共用。 當 SMB 用戶端因為第一次嘗試)  (的輸入頻寬節流而受到節流處理，但在重試後成功時，要求成功。</li><li> **SuccessWithShareIopsThrottling**：僅適用于 premium 檔案共用。 當 SMB 用戶端因為第一次嘗試)  (的 IOPS 節流而受到節流處理，但在重試後成功時，便會成功要求。</li> |
| **ApiName** | 作業的名稱。 如果 [在識別](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md#logged-operations) 出作業名稱之前發生失敗，名稱會顯示為「未知」。 您可以使用 [ **>responsetype** ] 維度的值，以深入瞭解失敗。
| **驗證** | 交易中所使用的驗證類型。 可用的值包括： <br/> <li>**AccountKey**：交易會使用儲存體帳戶金鑰進行驗證。</li> <li>**SAS**：交易會使用共用存取簽章進行驗證。</li> <li>**OAuth**：交易會使用 OAuth 存取權杖進行驗證。</li> <li>**Anonymous**：以匿名方式要求交易。 不包括預檢要求。</li> <li>**AnonymousPreflight**：交易是預檢要求。</li> |