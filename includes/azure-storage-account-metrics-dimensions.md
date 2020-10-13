---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711192"
---
| 維度名稱 | 描述 |
| ------------------- | ----------------- |
| **GeoType** | 來自主要或次要叢集的交易。 可用的值包括**主要**和**次要**。 在從次要租用戶讀取物件時，此維度會套用到讀取權限異地備援儲存體 (RA-GRS)。 |
| **ResponseType** | 交易回應類型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：描述項目之外的其他所有伺服器端錯誤 </li> <li>**ServerBusyError**：傳回 HTTP 503 狀態碼的已驗證要求。 </li> <li>**ServerTimeoutError**：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 逾時是因為伺服器錯誤而發生。 </li> <li>**AuthorizationError**：由於未經授權存取資料或授權失敗，從而發生失敗的已驗證要求。 </li> <li>**NetworkError**：由於網路錯誤而失敗的已驗證要求。 當用戶端在逾時到期前就過早關閉連線時，最常會發生這個情況。 </li><li>**ClientAccountBandwidthThrottlingError**：要求會針對超過[儲存體帳戶可擴縮性限制](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)，在頻寬上受到節流。</li><li>**ClientAccountRequestThrottlingError**：要求會針對超過[儲存體帳戶可擴縮性限制](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)，在要求速率上進行節流。<li>**ClientThrottlingError**：其他用戶端節流錯誤。 已排除 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li> <li>**ClientTimeoutError**：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 如果用戶端的網路逾時或要求逾時設定為比儲存體服務預期的值還低，則此值是符合預期的逾時。 否則，它會回報為 ServerTimeoutError。 </li> <li>**ClientOtherError**：描述項目之外的其他所有用戶端錯誤。 </li> <li>**成功**：成功的要求</li> <li> **SuccessWithThrottling**：當 SMB 用戶端在第一次嘗試中受到節流，但是在重試之後成功時，成功的要求。</li> |
| **ApiName** | 作業的名稱。 
| **驗證** | 交易中所使用的驗證類型。 可用的值包括： <br/> <li>**AccountKey**：交易會使用儲存體帳戶金鑰進行驗證。</li> <li>**SAS**：交易會使用共用存取簽章進行驗證。</li> <li>**OAuth**：交易會使用 OAuth 存取權杖進行驗證。</li> <li>**Anonymous**：以匿名方式要求交易。 不包括預檢要求。</li> <li>**AnonymousPreflight**：交易是預檢要求。</li> |