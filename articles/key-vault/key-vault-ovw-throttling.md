---
title: Azure Key Vault 節流指導方針
description: Key Vault 節流措施會限制並行呼叫數目，以防止過度使用資源。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197363"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 節流指導方針

節流是您起始來限制 Azure 服務並行呼叫數目，以避免過度使用資源的程序。 Azure Key Vault (AKV) 被設計來處理大量的要求。 如果產生大量的要求，針對用戶端的要求進行節流有助於維護 AKV 服務的最佳效能和可靠性。

節流限制視情節而異。 例如，如果您正在執行大量的寫入，則節流的可能性要高於僅執行讀取作業的可能性。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何處理其限制？

金鑰保存庫中的服務限制可防止資源濫用，並確保所有金鑰保存庫用戶端的服務品質。 當超過服務閾值時，Key Vault 會限制來自該用戶端的任何進一步請求一段時間，返回 HTTP 狀態碼 429（請求太多），並且請求失敗。 返回 429 計數到金鑰保存庫跟蹤的油門限制的失敗請求。 

金鑰保存庫最初設計用於在部署時存儲和檢索機密。  世界已經發展，金鑰保存庫在運行時用於存儲和檢索機密，並且應用和服務通常希望像資料庫一樣使用金鑰保存庫。  當前限制不支援高吞吐率。

金鑰保存庫最初是使用[Azure 金鑰保存庫服務限制](key-vault-service-limits.md)中指定的限制創建的。  要通過放位率最大化金鑰保存庫，以下是一些最大化輸送量的建議指南/最佳實踐：
1. 確保有限制到位。  客戶必須遵守 429 的指數回退策略，並確保您根據以下指南執行重試。
1. 將金鑰保存庫流量劃分為多個保存庫和不同區域。   為每個安全/可用性域使用單獨的保存庫。   如果您有五個應用（每個應用在兩個區域中），則我們建議每個保存庫包含 10 個保存庫，每個保存庫包含應用和區域特有的機密。  所有事務類型的訂閱範圍限制是單個金鑰保存庫限制的五倍。 例如，每次訂閱的 HSM 其他事務限制為每個訂閱在 10 秒內進行 5，000 個事務。 請考慮在服務或應用中緩存機密，以便將 RPS 直接減少到金鑰保存庫和/或處理基於突發的流量。  您還可以將流量劃分到不同區域之間，以儘量減少延遲並使用不同的訂閱/保存庫。  不要向單個 Azure 區域中的金鑰保存庫服務發送超過訂閱限制。
1. 緩存從 Azure 金鑰保存庫在記憶體中檢索到的秘密，並盡可能從記憶體中重複使用。  僅當緩存的副本停止工作時（例如，因為它在源處旋轉）才從 Azure 金鑰保存庫重新讀取。 
1. 金鑰保存庫專為您自己的服務機密而設計。   如果要存儲客戶的秘密（尤其是高通量金鑰存儲方案），請考慮將金鑰放在具有加密的資料庫或存儲帳戶中，並僅將主金鑰存儲在 Azure 金鑰保存庫中。
1. 無需訪問金鑰保存庫即可執行加密、包裝和驗證公開金鑰操作，這不僅降低了限制風險，還提高了可靠性（只要正確緩存公開金鑰材料）。
1. 如果使用金鑰保存庫存儲服務的憑據，請檢查該服務是否支援 AAD 身份驗證以直接進行身份驗證。 這減少了金鑰保存庫的負載，提高了可靠性並簡化了代碼，因為金鑰保存庫現在可以使用 AAD 權杖。  許多服務已轉向使用 AAD Auth。 請參閱支援 Azure[資源的託管標識的服務中的](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)當前清單。
1. 請考慮在較長時間內將負載/部署驚人的時間，以保持在當前 RPS 限制之下。
1. 如果應用包含多個需要讀取同一金鑰的節點，則請考慮使用扇出模式，其中一個實體從金鑰保存庫讀取金鑰，扇出所有節點。   僅在記憶體中緩存檢索到的秘密。
如果您發現上述內容仍不能滿足您的需要，請填寫下表並聯系我們，以確定可以添加哪些額外容量（下面僅供說明）。

| 保存庫名稱 | 保險庫區域 | 物件類型（機密、金鑰或證書） | 操作* | 索引鍵類型 | 鍵長度或曲線 | HSM 金鑰？| 需要穩定狀態 RPS | 需要峰值 RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Key | 簽署 | EC | P-256 | 否 | 200 | 1000 |

\*有關可能值的完整清單，請參閱[Azure 金鑰保存庫操作](/rest/api/keyvault/key-operations)。

如果批准增加容量，請注意以下情況：：
1. 資料一致性模型更改。 允許列出具有額外輸送量容量的保存庫後，金鑰保存庫服務資料一致性保證更改（滿足較高卷 RPS 所需的條件，因為基礎 Azure 存儲服務無法跟上）。  簡言之：
  1. **不允許列出**：金鑰保存庫服務將反映寫入操作的結果（例如。 在後續調用中立即設置秘密集，創建金鑰（例如， 秘密獲取，鑰匙簽名）。
  1. **允許清單**：金鑰保存庫服務將反映寫入操作的結果（例如。 在後續調用（例如，創建鍵）60 秒內設置秘密設置，創建金鑰 秘密獲取，鑰匙簽名）。
1. 用戶端代碼必須遵守 429 次重試的退退策略。 調用金鑰保存庫服務的用戶端代碼在收到 429 回應代碼時，不得立即重試金鑰保存庫請求。  此處發佈的 Azure 金鑰保存庫限制指南建議在接收 429 Http 回應代碼時應用指數回退。

如果您有需要更高節流限制的有效商務案例，請與我們連絡。

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何為您的應用程式進行節流處理，以回應服務限制

以下是在限制服務時應實現的**最佳做法**：
- 減少每個要求的作業數目。
- 減少要求的頻率。
- 避免立即重試。 
    - 所有要求都會讓您的使用量限制累加。

當您實作應用程式的錯誤處理時，請使用 HTTP 錯誤碼 429 來偵測用戶端節流的需求。 如果要求再次失敗並傳回 HTTP 429 錯誤碼，您仍然遇到 Azure 服務限制。 繼續使用建議的用戶端節流方法，重試要求直到成功為止。

實作指數輪詢的程式碼如下所示。 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


在用戶端 C# 應用程式中使用此代碼非常簡單。 

### <a name="recommended-client-side-throttling-method"></a>建議使用的用戶端節流方法

針對 HTTP 錯誤碼 429，使用指數型輪詢方法開始為您的用戶端進行節流處理：

1. 等候 1 秒，重試要求
2. 如果等候 2 秒仍然進行節流處理，重試要求
3. 如果等候 4 秒仍然進行節流處理，重試要求
4. 如果等候 8 秒仍然進行節流處理，重試要求
5. 如果等候 16 秒仍然進行節流處理，重試要求

此時，您應該不會收到 HTTP 429 回應碼。

## <a name="see-also"></a>另請參閱

如需在 Microsoft Cloud 上進行節流處理的詳細資訊，請參閱[節流模式](https://docs.microsoft.com/azure/architecture/patterns/throttling) \(英文\)。

