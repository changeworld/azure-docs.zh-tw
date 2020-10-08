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
ms.openlocfilehash: 7aa33bb062abf748031b27df46d42e8f13aabfc3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819971"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 節流指導方針

節流是您起始來限制 Azure 服務並行呼叫數目，以避免過度使用資源的程序。 Azure Key Vault (AKV) 被設計來處理大量的要求。 如果產生大量的要求，針對用戶端的要求進行節流有助於維護 AKV 服務的最佳效能和可靠性。

節流限制視情節而異。 例如，如果您正在執行大量的寫入，則節流的可能性要高於僅執行讀取作業的可能性。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何處理其限制？

Key Vault 中的服務限制會防止資源濫用，並確保所有 Key Vault 之用戶端的服務品質。 當超過服務閾值時，Key Vault 會將該用戶端的任何進一步要求限制在一段時間內，會傳回 HTTP 狀態碼 429 (太多要求) ，且要求會失敗。 失敗的要求會針對 Key Vault 所追蹤的節流限制傳回429計數。 

Key Vault 原本是設計用來在部署期間儲存和取得秘密。  世界已經演進過，而且在執行時間使用了 Key Vault 來儲存和取出秘密，而應用程式和服務通常想要使用 Key Vault 例如資料庫。  目前的限制不支援高輸送量速率。

Key Vault 最初是以 [Azure Key Vault 服務限制](service-limits.md)中所指定的限制來建立。  若要透過 put 費率最大化您的 Key Vault，以下是將輸送量最大化的一些建議方針/最佳作法：
1. 確定您已準備好節流。  用戶端必須遵守429的指數反向原則，並確定您是依照下列指導方針來進行重試。
1. 將您的 Key Vault 流量分割至多個保存庫和不同區域。   針對每個安全性/可用性網域使用不同的保存庫。   如果您有五個應用程式，每個都在兩個區域中，則建議每個保存庫都包含應用程式和區域特有的秘密。  所有交易類型的全訂用帳戶限制是個別金鑰保存庫限制的五倍。 例如，每個訂用帳戶的 HSM 其他交易限制為每個訂用帳戶10秒內的5000筆交易。 請考慮將秘密快取在服務或應用程式中，以將 RPS 直接減少到 key vault 及/或處理以高載為基礎的流量。  您也可以將流量分散到不同區域，以將延遲降至最低，並使用不同的訂用帳戶/保存庫。  請勿將超過訂用帳戶限制的訂用帳戶限制傳送到單一 Azure 區域中的 Key Vault 服務。
1. 快取您從記憶體 Azure Key Vault 取出的秘密，並盡可能在記憶體中重複使用。  只有當快取的複本停止運作 (例如，因為它在來源) 旋轉時，才會從 Azure Key Vault 中重新讀取。 
1. Key Vault 是針對您自己的服務秘密所設計。   如果您要儲存客戶的秘密 (特別是在) 的高輸送量金鑰儲存案例中，請考慮將金鑰放在具有加密的資料庫或儲存體帳戶中，並只將主要金鑰儲存在 Azure Key Vault 中。
1. 加密、包裝和驗證公開金鑰作業可以不受 Key Vault 的存取權來執行，這不僅會降低節流的風險，還可改善可靠性 (，只要您已正確地快取) 的公開金鑰內容即可。
1. 如果您使用 Key Vault 儲存服務的認證，請檢查該服務是否支援直接驗證 Azure AD 驗證。 這可降低 Key Vault 的負載、提升可靠性並簡化您的程式碼，因為 Key Vault 現在可以使用 Azure AD token。  許多服務都已移至使用 Azure AD Auth。 請參閱目前在 [支援 Azure 資源受控](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)識別的服務清單。
1. 請考慮將負載/部署錯開一段較長的時間，以保持在目前的 RPS 限制下。
1. 如果您的應用程式包含多個節點，而這些節點需要讀取相同的秘密 (s) ，則請考慮使用「移出」模式，其中一個實體會從 Key Vault 讀取秘密，以及將風扇輸出到所有節點。   只在記憶體中快取抓取的秘密。
如果您發現上述仍不符合您的需求，請填寫下表，並與我們聯繫，以判斷可新增哪些額外的容量 (範例中，僅供說明之用) 。

| 保存庫名稱 | 保存庫區域 |  (Secret、Key 或 Cert) 的物件類型 | Operation (s) * | 索引鍵類型 | 金鑰長度或曲線 | HSM 金鑰？| 需要穩定狀態 RPS | 需要尖峰 RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | 機碼 | 簽署 | EC | P-256 | 否 | 200 | 1000 |

\* 如需可能值的完整清單，請參閱 [Azure Key Vault 作業](/rest/api/keyvault/key-operations)。

如果已核准額外容量，請注意下列情況：容量增加的結果：
1. 資料一致性模型變更。 當保存庫允許以額外的輸送量容量列出之後，Key Vault 服務資料一致性保證會 (所需的變更，以符合較高的磁片區 RPS，因為基礎 Azure 儲存體服務無法趕上) 。  簡言之：
  1. 如果**沒有允許清單**： Key Vault 服務將會反映寫入作業的結果， (例如。 在後續的呼叫中，SecretSet、CreateKey) 立即 (例如 SecretGet，KeySign) 。
  1. **使用允許清單**： Key Vault 服務會反映寫入作業的結果， (例如。 在後續呼叫的60秒內，SecretSet、CreateKey)  (例如 SecretGet，KeySign) 。
1. 用戶端程式代碼必須接受429重試的反向原則。 呼叫 Key Vault 服務的用戶端程式代碼在收到429回應碼時，不能立即重試 Key Vault 的要求。  此處所發佈的 Azure Key Vault 節流指導方針，建議您在收到 429 Http 回應碼時，套用指數輪詢。

如果您有需要更高節流限制的有效商務案例，請與我們連絡。

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何為您的應用程式進行節流處理，以回應服務限制

以下是當您的服務節流時應執行的 **最佳作法** ：
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
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
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

