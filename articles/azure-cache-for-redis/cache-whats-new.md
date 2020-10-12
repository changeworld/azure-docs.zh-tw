---
title: Azure Cache for Redis 的新功能
description: Azure Cache for Redis 的最新更新
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492409"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis 的新功能

## <a name="azure-tls-certificate-change"></a>Azure TLS 憑證變更

Microsoft 正在更新 Azure 服務，以從一組不同的憑證授權單位單位（ (CAs) ）使用 TLS 伺服器憑證。 這項變更會從2020年8月13日到2020年10月26日的階段推出， (預估的) 。 Azure 正在進行這項變更，因為 [目前的 ca 憑證不符合其中一個 ca/瀏覽器論壇基準需求](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)。 此問題已于2020年7月1日回報，適用于全球 (PKI) 提供者的多個熱門公開金鑰基礎結構。 現今 Azure 服務所使用的大部分 TLS 憑證都是來自 *巴爾的摩 CyberTrust 根* PKI。 Azure Cache for Redis 服務將繼續連結至巴爾的摩 CyberTrust 根。 不過，其 TLS 伺服器憑證會由新的中繼憑證授權單位單位發行 (ICAs) 自2020年10月12日開始。

> [!NOTE]
> 這項變更僅限於公用 [Azure 區域](https://azure.microsoft.com/global-infrastructure/geographies/)中的服務。 它會排除主權 (例如中國) 或政府雲端。
>
>

### <a name="does-this-change-affect-me"></a>這項變更是否會對我造成影響？

我們預期大部分的 Azure Cache for Redis 客戶都不會受到變更影響。 如果您的應用程式明確指定可接受的憑證清單（稱為「憑證釘選」的作法），則可能會受到影響。 如果它已釘選到中繼或分葉憑證，而不是巴爾的摩 CyberTrust 根目錄，您應該 **採取立即動作** 來變更憑證設定。

下表提供正在擲出之憑證的相關資訊。 根據您的應用程式所使用的憑證，您可能需要更新它，以防止 Azure Cache for Redis 實例的連接中斷。

| CA 類型 | 目前 | 2020年10月12日推出後的滾動 ()  | 動作 |
| ----- | ----- | ----- | ----- |
| Root | 指紋： d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 到期日：星期一、5月12日、2025、4:59:00 PM<br><br> 主體名稱：<br> CN = 巴爾的摩 CyberTrust Root<br> OU = CyberTrust<br> O = 巴爾的摩<br> C = IE | 未變更 | 無 |
| 中間體 | 指紋<br> CN = Microsoft IT TLS CA 1<br> 指紋：417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> 指紋：54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> 指紋：8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> 指紋： Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 到期日：星期五，5月20日，2024 5:52:38 AM<br><br> 主體名稱：<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = 華盛頓州<br> C = US<br> | 指紋<br> CN = Microsoft RSA TLS CA 01<br> 指紋：703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 指紋： b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 到期日：2024年10月8日星期二上午12:00:00：<br><br> 主體名稱：<br> O = Microsoft Corporation<br> C = US<br> | 必要 |

### <a name="what-actions-should-i-take"></a>我應該採取哪些動作？

如果您的應用程式使用作業系統憑證存放區或在其他人之間釘選巴爾的摩根目錄，則不需要採取任何動作。 另一方面，如果您的應用程式會釘選任何中繼或分葉 TLS 憑證，建議您釘選下列根目錄：

| 憑證 | Thumbprint |
| ----- | ----- |
| [巴爾的摩根 CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert 全域根目錄 G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 中繼和分葉憑證預期會經常變更。 建議您不要依賴它們。 請改為將您的應用程式釘選到根憑證，因為它的頻率較低。
>
>

若要繼續釘選中繼憑證，請將下列內容新增至釘選的中繼憑證清單中，其中包含幾個額外的專案，可將未來的變更降至最低

| CA 的一般名稱 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS 發行 CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 發行 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 發行 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 發行 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

如果您的應用程式在程式碼中驗證憑證，您將需要修改它，以辨識新釘選憑證的屬性 (例如簽發者、指紋) 。 此額外的驗證應涵蓋所有釘選的憑證，以便更進一步瞭解。

## <a name="next-steps"></a>後續步驟

如果您有其他問題，請透過 [支援](https://azure.microsoft.com/support/options/)聯絡我們。  
