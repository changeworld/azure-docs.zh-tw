---
title: Azure Key Vault 的新功能
description: Azure Key Vault 的近期更新
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 194b0122987d4fdc5d100112c60006588d28f96c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826922"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault 的新功能

以下是 Azure Key Vault 的新功能。 您也可以在 [Azure 更新 Key Vault 通道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)上宣佈新功能和改善項目。

## <a name="july-2020"></a>2020 年 7 月

> [!WARNING]
> 這兩項更新可能會影響到 Azure Key Vault 的實作。

### <a name="soft-delete-on-by-default"></a>預設的虛刪除

在 2020 年底之前，**所有金鑰保存庫預設都會開啟虛刪除功能** (包括新的和預先存在的金鑰保存庫)。 如需這項可能重大變更的完整詳細資料，以及尋找受影響的金鑰保存庫，並事先加以更新的步驟，請參閱[將在所有金鑰保存庫上啟用虛刪除](soft-delete-change.md)的一文。 

### <a name="azure-tls-certificate-changes"></a>Azure TLS 憑證變更  

Microsoft 正在更新 Azure 服務，以使用來自一組不同根憑證授權單位 (CA) 的 TLS 憑證。 由於目前的 CA 憑證[不符合其中一個 CA/瀏覽器論壇基準需求](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)，因此需要進行此項變更。

### <a name="when-will-this-change-happen"></a>此變更何時會發生？

- [Azure Active Directory](/azure/active-directory) (Azure AD) 服務會在 2020 年 7 月 7 日開始轉換。
- 所有新建立的 Azure TLS/SSL 端點都會包含已更新的憑證，並鏈結至新的根 CA。
- 現有的 Azure 端點會從 2020 年 8 月 13 日起分階段轉換，並於 2020 年 10 月 26 日完成。
- [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub)和 [DPS](/azure/iot-dps/) 將會保留在 Baltimore CyberTrust Root CA 上，但其中繼 CA 將會變更。 如需完整詳細資料，請參閱部落格文章 [Azure IoT TLS：即將推出變更！(...以及您關心的原因)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)。
- [Azure 儲存體](/azure/storage) 將會保留在 Baltimore CyberTrust Root CA 上，但其中繼 CA 將會變更。 如需完整詳細資料，請參閱部落格文章 [Azure 儲存體 TLS：即將推出變更！(...以及您關心的原因)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)。

> [!IMPORTANT]
> 客戶可能需要在這次變更之後更新其應用程式，以避免在嘗試連線到 Azure 服務時發生連線失敗。

### <a name="what-is-changing"></a>變更內容為何？

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

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>何時可以淘汰舊的中繼指紋？

在 2021 年 2 月 15 日前，「不會」撤銷目前的 CA 憑證。 在該日期之後，您就可以從程式碼中移除舊的指紋。

如果此日期有所變動，您會收到新撤銷日期的通知。

### <a name="will-this-change-affect-me"></a>這項變更是否會對我造成影響？ 

我們預期**大部分的 Azure 客戶都不會**受到影響。  不過，如果您的應用程式有明確指定可接受的 CA 清單，可能會受到影響。 這種做法稱為憑證關聯。

以下有一些方法，可偵測您的應用程式是否會受到影響：

- 在原始程式碼中，搜尋您在[此處](https://www.microsoft.com/pki/mscorp/cps/default.htm)找到的任何 Microsoft IT TLS CA 指紋、一般名稱和其他憑證屬性。 如果相符，則您的應用程式會受到影響。 若要解決此問題，請更新原始程式碼並納入新的 CA。 最佳做法是確保在臨時通知時可以新增或編輯 CA。 業界法規規定要在 7 天內更換 CA 憑證，因此依賴關聯的客戶必須迅速回應。

- 如果您的應用程式與 Azure API 或其他 Azure 服務整合，而您不確定該應用程式是否使用憑證關聯，請洽詢應用程式廠商。

- 與 Azure 服務通訊的其他作業系統和語言執行階段可能需要額外的步驟，才能使用這些新的根正確建立憑證鏈結：
    - **Linux**：許多散發套件都需要您將 CA 新增至 /etc/ssl/certs。 如需特定指示，請參閱散發套件文件。
    - **Java**︰請確定 Java 金鑰存放區包含上列的 CA。
    - **在中斷連線的環境中執行 Windows**：在中斷連線的環境中執行的系統，必須將新的根新增至 [信任的根憑證授權單位] 存放區，並將中繼新增至 [中繼憑證授權單位] 存放區。
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

## <a name="june-2020"></a>2020 年 6 月

適用於 Key Vault 的 Azure 監視器目前為預覽狀態。  Azure 監視器可針對您的 Key Vault 要求、效能、失敗和延遲提供統合檢視，讓您能夠全面監視金鑰保存庫。 如需詳細資訊，請參閱[適用於 Key Vault 的 Azure 監視器 (預覽)](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="may-2020"></a>2020 年 5 月

Key Vault「攜帶您自己的金鑰」 (BYOK) 現已正式發行。 請參閱 [Azure Key Vault BYOK 規格](../keys/byok-specification.md)，並了解如何[將受 HSM 保護的金鑰匯入至 Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md)。

## <a name="march-2020"></a>2020 年 3 月

私人端點現已提供預覽版本。 Azure Private Link 服務可讓您透過虛擬網路中的私人端點存取 Azure Key Vault 和 Azure 裝載的客戶/合作夥伴服務。  了解如何[整合 Key Vault 與 Azure Private Link](private-link-service.md)。

## <a name="2019"></a>2019

- 下一代 Azure Key Vault SDK 發行版本。 如需用法範例，請參閱適用於 [Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md) 和 [Node.js](../secrets/quick-create-node.md) 的 Azure Key Vault 祕密快速入門
- 管理金鑰保存庫憑證的新 Azure 原則。 請參閱[適用於 Key Vault 的 Azure 原則內建定義](../policy-samples.md)。
- Azure Key Vault 虛擬機器擴充功能現已正式發行。  請參閱[適用於 Linux 的 Key Vault 虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-linux.md)和[適用於 Windows 的 Key Vault 虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-windows.md)。
- Azure 事件方格現已提供 Azure Key Vault 事件驅動的祕密管理。 如需詳細資訊，請參閱 [Azure Key Vault 中的事件方格結構描述](../../event-grid/event-schema-key-vault.md]，並了解如何[使用 Azure 事件方格接收和回應金鑰保存庫通知](event-grid-tutorial.md)。

## <a name="2018"></a>2018

今年發行的新功能與整合項目：

- 與 Azure Functions 整合。 如需利用 [Azure Functions](../../azure-functions/index.yml) 進行金鑰保存庫作業的範例案例，請參閱[祕密自動輪替](../secrets/tutorial-rotation.md)。 
- [與 Azure Databricks 整合](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 如此一來，Azure Databricks 現在支援兩種類型的祕密範圍：Azure Key Vault 支援和 Databricks 支援。 如需詳細資訊，請參閱[建立 Azure Key Vault 支援的祕密範圍](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

今年發行的新功能：

- 受控儲存體帳戶金鑰。 儲存體帳戶金鑰功能已新增可與 Azure 儲存體更輕鬆地整合。 如需詳細資訊，請參閱概觀主題：[受控儲存體帳戶金鑰概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)。
- 虛刪除。 虛刪除功能，改善金鑰保存庫和金鑰保存庫物件的資料保護。 如需詳細資訊，請參閱概觀主題：[虛刪除概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。

## <a name="2015"></a>2015

今年發行的新功能：
- 憑證管理。 在 2016 年 9 月 26 日，已作為功能新增至正式發行版本 2015-06-01。

於 2015 年 6 月 24 日宣佈正式發行 (版本 2015-06-01)。 此版本已進行下列變更： 
- 刪除金鑰 - 已移除 "use" 欄位。
- 取得金鑰的相關資訊 - 已移除 "use" 欄位。
- 將金鑰匯入保存庫 - 已移除 "use" 欄位。
- 還原金鑰 - 已移除 "use" 欄位。     
- 針對 RSA 演算法，已將 "RSA_OAEP" 變更為 "RSA-OAEP"。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-certificates.md)。    
 
於 2015 年 4 月 20 日宣佈第二個預覽版本 (版本 2015-02-01-preview)。 如需詳細資訊，請參閱 [REST API 更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update)部落格文章。 已更新下列工作：
 
- 列出保存庫中的工作 - 已將分頁支援新增至作業。
- 列出金鑰的版本 - 已新增作業，來列出金鑰的版本。  
- 列出保存庫中的祕密 - 已新增分頁支援。
- 列出祕密的版本 - 新增作業，來列出祕密的版本。  
- 所有作業 - 已將建立/更新時間戳記新增至屬性。  
- 建立祕密 - 已將 Content-Type 新增至祕密。
- 建立金鑰 - 已將標籤新增為選用資訊。
- 建立祕密 - 已將標籤新增為選用資訊。
- 更新金鑰 - 已將標籤新增為選用資訊。
- 更新祕密 - 已將標籤新增為選用資訊。
- 已將祕密的大小上限從 10 K 變更為 25 K 位元組。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-certificates.md)。    

## <a name="2014"></a>2014
 
於 2015 年 1 月 8 日宣佈第一個預覽版本 (版本 2014-12-08-preview)。  

## <a name="next-steps"></a>後續步驟

如果您有其他問題，請透過[支援](https://azure.microsoft.com/support/options/)與我們連絡。  
