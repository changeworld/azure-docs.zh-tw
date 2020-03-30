---
title: 如何安全地運行 OPC 保存庫證書管理服務 - Azure |微軟文檔
description: 介紹如何在 Azure 中安全地運行 OPC Vault 證書管理服務，並查看其他要考慮的安全準則。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271703"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全地運行 OPC 保存庫證書管理服務

本文介紹如何在 Azure 中安全地運行 OPC Vault 證書管理服務，並查看其他要考慮的安全準則。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>受信任和授權的角色

OPC Vault 微服務允許不同角色訪問服務的各個部分。

> [!IMPORTANT]
> 在部署期間，腳本僅添加作為所有角色的使用者運行部署腳本的使用者。 對於生產部署，應查看此角色指派，並遵循以下準則進行適當重新配置。 此任務需要在 Azure 活動目錄 （Azure AD） 企業應用程式門戶中手動分配角色和服務。

### <a name="certificate-management-service-roles"></a>證書管理服務角色

OPC Vault 微服務定義了以下角色：

- **讀取器**：預設情況下，租戶中的任何經過身份驗證的使用者都具有讀取存取許可權。 
  - 讀取對應用程式和證書請求的訪問。 可以列出和查詢應用程式和證書請求。 此外，設備發現資訊和公共證書可通過讀取存取訪問。
- **編寫**器 ：編寫器角色指派給使用者以添加某些任務的寫入權限。 
  - 對應用程式和證書請求的讀/寫訪問。 可以註冊、更新和取消註冊應用程式。 可以創建證書請求並獲得批准的私密金鑰和證書。 也可以刪除私密金鑰。
- **核准者**：核准者角色指派給使用者以批准或拒絕證書請求。 該角色不包括任何其他角色。
  - 除了訪問 OPC Vault 微服務 API 的審批器角色外，使用者還必須在 Azure 金鑰保存庫中具有金鑰簽名許可權才能對證書進行簽名。
  - 應將編寫器和核准者角色指派給不同的使用者。
  - 審批人的主要作用是批准證書請求的生成和拒絕。
- **管理員**：管理員角色分配給使用者以管理證書組。 該角色不支援核准者角色，但包括"編寫者"角色。
  - 管理員可以通過頒發新的憑證撤銷清單 （CRL） 來管理證書組、更改配置和撤銷應用程式證書。
  - 理想情況下，作者、審批人和管理員角色分配給不同的使用者。 為了獲得其他安全性，具有審批方或管理員角色的使用者還需要金鑰保存庫中的金鑰簽名許可權，才能頒發證書或續訂頒發者 CA 憑證。
  - 除了微服務管理角色外，該角色還包括，但不限於：
    - 負責管理 CA 安全實踐的實施。
    - 證書的生成、吊銷和掛起的管理。 
    - 加密金鑰生命週期管理（例如，續訂頒發者 CA 金鑰）。
    - 安裝、配置和維護運行 CA 的服務。
    - 服務的日常運行。 
    - CA 和資料庫備份和恢復。

### <a name="other-role-assignments"></a>其他角色指派

在運行服務時，還要考慮以下角色：

- 證書採購合同的業主與外部根憑證授權單位（例如，當擁有者從外部 CA 購買證書或操作從屬外部 CA 的 CA 時）。
- 憑證授權單位的開發和驗證。
- 審查審計記錄。
- 説明支援 CA 或管理物理和雲設施，但不受直接信任執行 CA 操作的人員，由*授權*角色擔任。 還必須記錄授權角色中的任務集。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季度審核受信任和授權角色的成員資格

至少每季度審核一次受信任和授權角色的成員資格。 確保將每個角色中的人員（用於手動流程）或服務標識（用於自動化流程）集保持在最低限度。

### <a name="role-separation-between-certificate-requester-and-approver"></a>證書要求者和核准者之間的角色分離

憑證發行過程必須強制證書要求者和證書核准者角色（人員或自動系統）之間的角色分離。 憑證發行必須由證書核准者角色授權，該角色驗證憑證要求者是否有權獲取證書。 擔任證書審批人職務的人員必須是正式授權的人員。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特權角色的分配

您應該將特權角色的分配（如授權管理員和核准者組的成員身份）限制為一組有限的授權人員。 任何特權角色更改都必須在 24 小時內撤銷存取權限。 最後，每季度查看特權角色指派，並刪除任何不需要或過期的分配。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特權角色應使用雙重身份驗證

使用多重要素驗證（也稱為雙重身份驗證）對核准者和管理員對服務的互動式登入。

## <a name="certificate-service-operation-guidelines"></a>憑證服務操作指南

### <a name="operational-contacts"></a>業務連絡人

憑證服務必須存檔最新的安全回應計畫，其中包含詳細的操作事件回應連絡人。

### <a name="security-updates"></a>安全性更新

所有系統都必須使用最新的安全更新進行持續監視和更新。

> [!IMPORTANT]
> OPC Vault 服務的 GitHub 存儲庫會不斷更新安全修補程式。 監視這些更新，並定期將其應用於服務。

### <a name="security-monitoring"></a>安全性監控

訂閱或實施適當的安全監視。 例如，訂閱中央監視解決方案（如 Azure 安全中心或 Office 365 監視解決方案），並對其進行適當配置，以確保安全事件傳輸到監視解決方案。

> [!IMPORTANT]
> 預設情況下，OPC Vault 服務隨[Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/devops)一起部署作為監視解決方案。 強烈建議添加 Azure[安全中心](https://azure.microsoft.com/services/security-center/)等安全解決方案。

### <a name="assess-the-security-of-open-source-software-components"></a>評估開源軟體元件的安全性

產品或服務中使用的所有開源元件都必須沒有中等或更大的安全性漏洞。

> [!IMPORTANT]
> 在連續集成生成期間，OPC Vault 服務的 GitHub 存儲庫會掃描所有元件中的漏洞。 在 GitHub 上監視這些更新，並定期將它們應用於服務。

### <a name="maintain-an-inventory"></a>維護庫存

維護所有生產主機（包括持久虛擬機器）、設備、所有內部 IP 位址範圍、VIP 和公有 DNS 功能變數名稱的資產清單。 每當添加或刪除系統、設備 IP 位址、VIP 或公共 DNS 域時，都必須在 30 天內更新清單。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>預設 Azure OPC Vault 微服務生產部署的清單 

在 Azure 中：
- **應用服務方案**：服務主機的應用服務方案。 預設 S1。
- 用於微服務**的應用服務**：OPC Vault 服務主機。
- 示例**應用程式的應用服務**：OPC Vault 應用程式範例主機。
- **金鑰保存庫標準**：用於存儲 Web 服務的秘密和 Azure Cosmos DB 金鑰。
- **金鑰保存庫高級**：承載頒發者 CA 金鑰、簽名服務以及保存庫配置和應用程式私密金鑰的存儲。
- **Azure 宇宙資料庫**：用於應用程式和證書請求的資料庫。 
- **應用程式見解**：（可選）用於 Web 服務和應用程式的監視解決方案。
- **Azure AD 應用程式註冊**：應用程式範例、服務和邊緣模組的註冊。

對於雲服務，應記錄用於部署服務的所有主機名稱、資源組、資源名稱、訂閱 ID 和租戶 ID。 

在 Azure IoT 邊緣或本地 IoT 邊緣伺服器中：
- **OPC Vault IoT 邊緣模組**：支援工廠網路 OPC UA 全域發現伺服器。 

對於 IoT 邊緣設備，應記錄主機名稱和 IP 位址。 

### <a name="document-the-certification-authorities-cas"></a>記錄認證機構 （C）

CA 層次結構文檔必須包含所有操作的 CA。 這包括所有相關的從屬 C、父 C 和根 C，即使它們不是由服務管理也是如此。 您可以提供一組詳盡無遺的所有未過期 CA 憑證，而不是正式文檔。

> [!NOTE]
> OPC Vault 應用程式範例支援下載服務中使用的和生成的所有證書以進行文檔記錄。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>記錄所有認證機構 （C） 頒發的證書

提供過去 12 個月內簽發的所有證書的詳盡集。

> [!NOTE]
> OPC Vault 應用程式範例支援下載服務中使用的和生成的所有證書以進行文檔記錄。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>記錄安全刪除加密金鑰的標準操作過程

在 CA 的存留期內，金鑰刪除可能很少發生。 這就是為什麼沒有使用者分配了金鑰保存庫證書刪除權，以及為什麼沒有公開要刪除頒發者 CA 憑證的 API 的原因。 安全刪除憑證授權單位加密金鑰的手動標準操作過程只能通過直接存取 Azure 門戶中的金鑰保存庫來獲取。 您還可以刪除金鑰保存庫中的證書組。 為確保立即刪除，請禁用[金鑰保存庫虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能。

## <a name="certificates"></a>憑證

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>證書必須符合最低證書設定檔

OPC Vault 服務是向訂閱者頒發最終實體證書的線上 CA。 OPC Vault 微服務在預設實現中遵循這些準則。

- 所有證書必須包含以下 X.509 欄位，如下所述：
  - 版本欄位的內容必須為 v3。 
  - 序號欄位的內容必須包括從 FIPS（聯邦資訊處理標準）140 經批准的亂數產生器獲取的至少 8 個位元組熵。<br>
    > [!IMPORTANT]
    > 預設情況下，OPC Vault 序號為 20 位元組，是從作業系統加密亂數產生器獲取的。 亂數產生器是 FIPS 140 在 Windows 設備上批准，但不是在 Linux 上。 在選擇使用 Linux VM 或 Linux Docker 容器的服務部署時，請考慮這一點，其中底層技術 OpenSSL 未批准 FIPS 140。
  - 頒發者唯一ID 和主題唯一 ID 欄位不得存在。
  - 最終實體證書必須根據 IETF RFC 5280 使用基本約束擴展進行標識。
  - 對於頒發 CA 憑證，必須將 pathLen 限制欄位設置為 0。 
  - 擴展金鑰使用副檔名必須存在，並且必須包含擴展金鑰使用物件識別碼 （OID） 的最小集。 不得指定任何擴展金鑰使用 OID （2.5.29.37.0）。 
  - CRL 發佈點 （CDP） 擴展必須存在於頒發者 CA 憑證中。<br>
    > [!IMPORTANT]
    > CDP 擴展存在於 OPC 保存庫 CA 憑證中。 儘管如此，OPC UA 設備使用自訂方法來分發 CRL。
  - 授權資訊訪問擴展必須存在於訂閱者證書中。<br>
    > [!IMPORTANT]
    > 頒發機構資訊訪問擴展位於 OPC Vault 訂閱者證書中。 但是，OPC UA 設備使用自訂方法來分發頒發者 CA 資訊。
- 必須使用經批准的非對稱演算法、金鑰長度、雜湊函數和填充模式。
  - RSA 和 SHA-2 是唯一受支援的演算法。
  - RSA 可用於加密、金鑰交換和簽名。
  - RSA 加密必須僅使用 OAEP、RSA-KEM 或 RSA-PSS 填充模式。
  - 需要大於或等於 2048 位的鑰匙長度。
  - 使用 SHA-2 系列雜湊演算法（SHA256、SHA384 和 SHA512）。
  - RSA 根 CA 金鑰的典型存留期大於或等於 20 年，必須為 4096 位或更大。
  - RSA 頒發者 CA 金鑰必須至少為 2048 位。 如果 CA 憑證到期日期在 2030 年之後，CA 金鑰必須為 4096 位或更大。
- 證書存留期
  - 根 CA 憑證：根 CA 的最大證書有效期不得超過 25 年。
  - 子 CA 或連線頒發者 CA 憑證：連線且僅頒發訂閱者證書的 CA 的最大證書有效期不得超過 6 年。 對於這些 C，相關私有簽名金鑰的使用時間不得超過 3 年才能頒發新證書。<br>
    > [!IMPORTANT]
    > 頒發者證書，因為它是在預設的 OPC Vault 微服務中生成的，沒有外部根 CA，被視為具有相應要求和存留期的連線子 CA。 預設存留期設置為 5 年，金鑰長度大於或等於 2048。
  - 所有非對稱金鑰必須具有最長 5 年的存留期和建議的 1 年存留期。<br>
    > [!IMPORTANT]
    > 預設情況下，使用 OPC Vault 頒發的應用程式證書的存留期為 2 年，應每年更換。 
  - 每當續訂證書時，都會使用新金鑰續訂證書。
- 應用程式實例證書中特定于 OPC UA 的擴展
  - 主題 AltName 擴展包括應用程式 Uri 和主機名稱。 這些位址可能包括 FQDN、IPv4 和 IPv6 位址。
  - 金鑰使用包括數位簽章、非再加密、金鑰加密和資料加密。
  - 擴展的金鑰使用包括伺服器 Auth 和用戶端 Auth。
  - 權威金鑰識別碼在簽名的證書中指定。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 金鑰和證書必須滿足最低要求

- **私密金鑰**：RSA 金鑰必須至少為 2048 位。 如果 CA 憑證到期日期在 2030 年之後，CA 金鑰必須為 4096 位或更大。
- **存留期**：連線且僅頒發訂閱者證書的 C 的最大證書有效期不得超過 6 年。 對於這些 C，相關私有簽名金鑰的使用時間不得超過 3 年才能頒發新證書。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬體安全模組保護 CA 金鑰

OpcVault 使用 Azure 金鑰保存庫高級版，金鑰受 FIPS 140-2 級硬體安全模組 （HSM） 的保護。 

金鑰保存庫使用的加密模組（無論是 HSM 還是軟體）均經過 FIPS 驗證。 作為受 HSM 保護創建或導入的金鑰在 HSM 中處理，驗證為 FIPS 140-2 級 2。 作為軟體保護創建或導入的金鑰在加密模組中處理，這些模組驗證為 FIPS 140-2 級 1。

## <a name="operational-practices"></a>業務實踐

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>記錄和維護憑證註冊的標準操作 PKI 實踐

記錄和維護標準操作過程 （SPS），用於 CA 如何頒發證書，包括： 
- 如何識別和驗證訂閱者。 
- 如何處理和驗證憑證請求（如果適用，還包括如何處理證書續訂和重新金鑰請求）。 
- 如何向訂閱者分發頒發的證書。 

OPC Vault 微服務 SOP 在[OPC 保存庫體系結構](overview-opc-vault-architecture.md)[和管理 OPC 保存庫憑證服務](howto-opc-vault-manage.md)中描述。 這些做法遵循"OPC 統一體系結構規範第 12 部分：發現和全球服務"。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>記錄和維護憑證撤銷的標準操作 PKI 做法

憑證撤銷過程在[OPC 保存庫體系結構](overview-opc-vault-architecture.md)和管理[OPC 保存庫憑證服務](howto-opc-vault-manage.md)中進行了描述。
    
### <a name="document-ca-key-generation-ceremony"></a>文檔 CA 金鑰生成儀式 

由於 Azure 金鑰保存庫中的安全存儲，OPC Vault 微服務中的頒發者 CA 金鑰生成得到了簡化。 有關詳細資訊，請參閱管理[OPC 保存庫憑證服務](howto-opc-vault-manage.md)。

但是，當您使用外部根憑證授權單位時，CA 金鑰生成儀式必須遵守以下要求。

CA 金鑰生成儀式必須針對至少包含以下專案的文檔腳本執行： 
- 角色和參與者職責的定義。
- 批准 CA 金鑰生成儀式的舉行。
- 儀式所需的加密硬體和啟動材料。
- 硬體準備（包括資產/配置資訊更新和簽核）。
- 作業系統安裝。
- 在 CA 金鑰生成儀式期間執行的特定步驟，例如： 
  - CA 應用程式安裝和配置。
  - CA 金鑰生成。
  - CA 金鑰備份。
  - CA 憑證簽名。
  - 在服務的受保護 HSM 中導入已簽名的金鑰。
  - CA 系統關閉。
  - 準備儲存材料。


## <a name="next-steps"></a>後續步驟

現在您已經學會了如何安全地管理 OPC Vault，您可以：

> [!div class="nextstepaction"]
> [使用 OPC 保存庫保護 OPC UA 設備](howto-opc-vault-secure.md)
