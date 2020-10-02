---
title: Azure TLS 憑證變更
description: Azure TLS 憑證變更
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc79261035ef0f8671b9e43e1332ad68d1c9d39
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654137"
---
# <a name="azure-tls-certificate-changes"></a>Azure TLS 憑證變更  

Microsoft 正在更新 Azure 服務，以使用來自一組不同根憑證授權單位 (CA) 的 TLS 憑證。 由於目前的 CA 憑證不符合其中一個 CA/瀏覽器論壇基準需求，因此需要進行此項變更。

## <a name="when-will-this-change-happen"></a>此變更何時會發生？

- [Azure Active Directory](/azure/active-directory) (Azure AD) 服務于2020年7月7日開始此轉換。
- 所有新建立的 Azure TLS/SSL 端點都會包含已更新的憑證，並鏈結至新的根 CA。
- 現有的 Azure 端點會從 2020 年 8 月 13 日起分階段轉換，並於 2020 年 10 月 26 日完成。
- [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub)和 [DPS](/azure/iot-dps/) 將會保留在 Baltimore CyberTrust Root CA 上，但其中繼 CA 將會變更。 [按一下這裡以取得詳細資訊](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456)。
- [Azure 儲存體](/azure/storage) 會保留在巴爾的摩 CYBERTRUST 根 CA，但其中繼 ca 將會變更。 [按一下這裡以取得詳細資訊](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)。

> [!IMPORTANT]
> 客戶可能需要在這次變更之後更新其應用程式，以避免在嘗試連線到 Azure 服務時發生連線失敗。

## <a name="what-is-changing"></a>變更內容為何？

目前 Azure 服務所使用的大部分 TLS 憑證會鏈結到下列根 CA：

| CA 的一般名稱 | 指紋 (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure 服務所使用的 TLS 憑證會鏈結到下列其中一個根 CA：

| CA 的一般名稱 | 指紋 (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>何時可以淘汰舊的中繼指紋？

在2021年2月15日之前，將 *不* 會撤銷目前的 CA 憑證。 在該日期之後，您就可以從程式碼中移除舊的指紋。

如果此日期有所變動，您會收到新撤銷日期的通知。

## <a name="will-this-change-affect-me"></a>這種變更是否會影響我？ 

我們預期**大部分的 Azure 客戶都不會**受到影響。  不過，如果您的應用程式有明確指定可接受的 CA 清單，可能會受到影響。 這種做法稱為憑證關聯。

以下有一些方法，可偵測您的應用程式是否會受到影響：

- 在原始程式碼中，搜尋您在[此處](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到的任何 Microsoft IT TLS CA 指紋、一般名稱和其他憑證屬性。 如果相符，則您的應用程式會受到影響。 若要解決此問題，請更新原始程式碼並納入新的 CA。 最佳做法是確定可以在簡短通知時新增或編輯 Ca。 產業規定需要在七天內取代 CA 憑證，因此需要進行釘選的客戶必須能夠迅速回應。

- 如果您的應用程式與 Azure API 或其他 Azure 服務整合，而您不確定該應用程式是否使用憑證關聯，請洽詢應用程式廠商。

- 與 Azure 服務通訊的其他作業系統和語言執行階段可能需要額外的步驟，才能使用這些新的根正確建立憑證鏈結：
    - **Linux**：許多散發套件都會要求您將 ca 新增至/etc/ssl/certs。 如需特定指示，請參閱散發套件的檔。
    - **Java**︰請確定 Java 金鑰存放區包含上列的 CA。
    - **在已中斷連線的環境中執行的 Windows**：在已中斷連線的環境中執行的系統必須將新的根目錄新增至「信任的根憑證授權單位」存放區，並將中繼新增至中繼憑證授權單位單位存放區。
    - **Android**：檢查您的裝置和 Android 版本文件。
    - **其他硬體裝置，特別是 IoT**：請連絡裝置製造商。

- 如果您的環境中已將防火牆規則設定為僅允許對特定憑證撤銷清單 (CRL) 下載和/或線上憑證狀態通訊協定 (OCSP) 驗證位置的輸出呼叫。 您需要允許下列 CRL 和 OCSP URL：

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="next-steps"></a>後續步驟

如果您有其他問題，請透過 [支援](https://azure.microsoft.com/support/options/)聯絡我們。
