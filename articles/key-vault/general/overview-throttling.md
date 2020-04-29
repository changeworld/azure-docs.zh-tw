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
ms.openlocfilehash: f32a988ec0d75ca8d8eca04e69edd7226bf283b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432082"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 節流指導方針

節流是您起始來限制 Azure 服務並行呼叫數目，以避免過度使用資源的程序。 Azure Key Vault (AKV) 被設計來處理大量的要求。 如果產生大量的要求，針對用戶端的要求進行節流有助於維護 AKV 服務的最佳效能和可靠性。

節流限制視情節而異。 例如，如果您正在執行大量的寫入，則節流的可能性要高於僅執行讀取作業的可能性。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何處理其限制？

Key Vault 中的服務限制可防止濫用資源，並確保所有 Key Vault 用戶端的服務品質。 超過服務閾值時，Key Vault 會限制來自該用戶端的任何進一步要求一段時間，傳回 HTTP 狀態碼429（太多要求），且要求會失敗。 傳回429計數的失敗要求會指向 Key Vault 追蹤的節流限制。 

Key Vault 原本是設計用來在部署期間儲存和抓取您的秘密。  世界已經進化，而 Key Vault 在執行時間用來儲存和抓取秘密，而應用程式和服務通常會想要使用 Key Vault 就像資料庫一樣。  目前的限制不支援高輸送量速率。

Key Vault 最初是以[Azure Key Vault 服務限制](service-limits.md)中指定的限制來建立。  若要透過 put 費率最大化您的 Key Vault，以下是將輸送量最大化的一些建議方針/最佳作法：
1. 請確定您已準備好節流。  用戶端必須遵循429的指數退避原則，並確定您依照下列指導來執行重試。
1. 將您的 Key Vault 流量劃分到多個保存庫和不同區域之間。   針對每個安全性/可用性網域使用個別的保存庫。   如果您有五個應用程式，每個在兩個區域中，則建議使用10個保存庫，其中每個都包含應用程式和區域獨有的秘密。  所有交易類型的全訂用帳戶限制是個別金鑰保存庫限制的五倍。 例如，每個訂用帳戶的 HSM-其他交易限制為每個訂用帳戶10秒內的5000筆交易。 請考慮在您的服務或應用程式內快取秘密，同時將 RPS 直接減少到金鑰保存庫和/或處理高載型流量。  您也可以將流量分散到不同的區域，以將延遲降到最低，並使用不同的訂用帳戶/保存庫。  請不要在單一 Azure 區域中傳送超過訂用帳戶限制給 Key Vault 服務。
1. 快取您從記憶體中 Azure Key Vault 取出的秘密，並盡可能重複使用記憶體。  只有在快取的複本停止運作（例如，因為它已在來源上旋轉）時，才從 Azure Key Vault 重新讀取。 
1. Key Vault 是針對您自己的服務秘密所設計。   如果您要儲存客戶的秘密（特別是針對高輸送量金鑰儲存案例），請考慮將金鑰放在具有加密功能的資料庫或儲存體帳戶中，然後在 Azure Key Vault 中只儲存主要金鑰。
1. 加密、包裝和驗證公用金鑰作業可以執行，而不需要 Key Vault 的存取權，這不僅會降低節流的風險，也會改善可靠性（只要您正確地快取公開金鑰內容即可）。
1. 如果您使用 Key Vault 來儲存服務的認證，請檢查該服務是否支援直接驗證 Azure AD 驗證。 這會降低 Key Vault 的負載、改善可靠性並簡化您的程式碼，因為 Key Vault 現在可以使用 Azure AD token。  許多服務已移至使用 Azure AD Auth。 [如需支援 Azure 資源受控識別的服務，](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)請參閱目前的清單。
1. 請考慮將您的負載/部署錯開一段較長的時間，以維持在目前的 RPS 限制之下。
1. 如果您的應用程式包含多個需要讀取相同秘密的節點，則請考慮使用「扇出」模式，其中一個實體會從 Key Vault 讀取秘密，並將風扇輸出到所有節點。   只在記憶體中快取抓取的秘密。
如果您發現上述程式仍然不符合您的需求，請填寫下表並與我們聯絡，以判斷可新增哪些額外的容量（以下範例僅供說明之用）。

| 保存庫名稱 | 保存庫區域 | 物件類型（秘密、金鑰或 Cert） | 作業（秒） * | 索引鍵類型 | 金鑰長度或曲線 | HSM 金鑰？| 需要穩定狀態 RPS | 需要尖峰 RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Key | 簽署 | EC | P-256 | 否 | 200 | 1000 |

\*如需可能值的完整清單，請參閱[Azure Key Vault 作業](/rest/api/keyvault/key-operations)。

如果已核准額外容量，請注意下列容量增加的結果：
1. 資料一致性模型變更。 當保存庫允許以額外的輸送量容量列出時，Key Vault 服務資料一致性保證會進行變更（因為基礎 Azure 儲存體服務無法跟上，所以必須符合較高的磁片區 RPS）。  簡言之：
  1. **不允許清單**： Key Vault 服務會反映寫入作業的結果（例如， SecretSet，CreateKey）立即在後續的呼叫中（例如 SecretGet, KeySign).
  1. **使用允許清單**： Key Vault 服務會反映寫入作業的結果（例如， SecretSet，CreateKey），在後續呼叫的60秒內（例如 SecretGet, KeySign).
1. 用戶端程式代碼必須遵循429重試的反向原則。 呼叫 Key Vault 服務的用戶端程式代碼不能在收到429回應碼時立即重試 Key Vault 要求。  此處所發佈的 Azure Key Vault 節流指導方針，建議您在收到 429 Http 回應碼時套用指數輪詢。

如果您有需要更高節流限制的有效商務案例，請與我們連絡。

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何為您的應用程式進行節流處理，以回應服務限制

以下是您在服務節流時應執行的**最佳作法**：
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


在用戶端 c # 應用程式中使用此程式碼相當簡單。 

### <a name="recommended-client-side-throttling-method"></a>建議使用的用戶端節流方法

針對 HTTP 錯誤碼 429，使用指數型輪詢方法開始為您的用戶端進行節流處理：

1. 等候 1 秒，重試要求
2. 如果等候 2 秒仍然進行節流處理，重試要求
3. 如果等候 4 秒仍然進行節流處理，重試要求
4. 如果等候 8 秒仍然進行節流處理，重試要求
5. 如果等候 16 秒仍然進行節流處理，重試要求

此時，您應該不會收到 HTTP 429 回應碼。

## <a name="see-also"></a>請參閱

如需在 Microsoft Cloud 上進行節流處理的詳細資訊，請參閱[節流模式](https://docs.microsoft.com/azure/architecture/patterns/throttling) \(英文\)。

