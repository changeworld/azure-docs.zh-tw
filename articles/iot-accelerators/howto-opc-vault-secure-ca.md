---
title: 如何安全地執行 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務，並審查其他應考慮的安全性指導方針。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 01213cd4d04783d0d877b4565493f06fa3718f8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399630"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全地執行 OPC 保存庫憑證管理服務

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

本文說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務，並審查其他應考慮的安全性指導方針。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>受信任和已授權的角色

OPC 保存庫微服務允許不同的角色存取服務的各個部分。

> [!IMPORTANT]
> 在部署期間，腳本只會將執行部署腳本的使用者新增為所有角色的使用者。 針對生產部署，您應該檢查此角色指派，並遵循下列指導方針來適當地重新設定。 這項工作需要在 Azure Active Directory (Azure AD) 企業應用程式入口網站中手動指派角色和服務。

### <a name="certificate-management-service-roles"></a>憑證管理服務角色

OPC 保存庫微服務會定義下列角色：

- **讀者**：根據預設，租使用者中所有已驗證的使用者都具有讀取權限。 
  - 應用程式和憑證要求的讀取存取權。 可以列出和查詢應用程式和憑證要求。 此外，也可以使用讀取存取權存取裝置探索資訊和公開憑證。
- **Writer**：寫入器角色會指派給使用者，以新增特定工作的寫入權限。 
  - 應用程式和憑證要求的讀取/寫入存取權。 可以註冊、更新和取消註冊應用程式。 可以建立憑證要求，並取得核准的私密金鑰和憑證。 也可以刪除私密金鑰。
- **核准者**：核准者角色會指派給使用者，以核准或拒絕憑證要求。 角色不包含任何其他角色。
  - 除了存取 OPC 保存庫微服務 API 的核准者角色之外，使用者也必須擁有 Azure Key Vault 的金鑰簽署許可權，才能簽署憑證。
  - 寫入器和核准者角色應指派給不同的使用者。
  - 核准者的主要角色是核准產生和拒絕憑證要求。
- **系統管理員**：系統管理員角色會指派給使用者來管理憑證群組。 角色不支援核准者角色，但包含寫入器角色。
  - 系統管理員可以藉由發出新的憑證撤銷清單 (CRL) 來管理憑證群組、變更設定，以及撤銷應用程式憑證。
  - 在理想情況下，寫入器、核准者和系統管理員角色會指派給不同的使用者。 為了提高安全性，具有核准者或系統管理員角色的使用者也需要 Key Vault 中的金鑰簽署許可權，以頒發證書或更新簽發者 CA 憑證。
  - 除了微服務管理角色之外，角色還包括（但不限於）：
    - 管理 CA 安全性實務的實施責任。
    - 管理憑證的產生、撤銷和暫停。 
    - 密碼編譯金鑰生命週期管理 (例如，更新簽發者 CA 金鑰) 。
    - 安裝、設定及維護操作 CA 的服務。
    - 服務的每日作業。 
    - CA 與資料庫備份和復原。

### <a name="other-role-assignments"></a>其他角色指派

當您執行服務時，也請考慮下列角色：

- 具有外部根憑證授權單位的憑證採購合約的商務擁有者 (例如，當擁有者向外部 CA 購買憑證或操作屬於外部 CA 的 CA 時) 。
- 憑證授權單位單位的開發和驗證。
- 審核記錄的評論。
- 在 *授權* 的角色中，有助於支援 ca 或管理實體和雲端功能，但不直接受信任以執行 ca 作業的人員。 您也必須記載授權角色中的一組工作人員，才能執行此作業。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季審查信任和授權角色的成員資格

至少每季查看受信任和已授權角色的成員資格。 確定為每個角色) 的自動化程式) 或 (服務身分識別 (的人員集合，保持最小值。

### <a name="role-separation-between-certificate-requester-and-approver"></a>憑證要求者與核准者之間的角色分隔

憑證發行程式必須將憑證要求者與憑證核准者角色之間的角色分隔， (人員或自動化系統) 。 憑證發行必須由憑證核准者角色授權，以確認已授權憑證要求者取得憑證。 持有憑證核准者角色的人員必須是正式授權的人員。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特殊許可權角色的指派

您應該將特殊許可權角色的指派（例如授權系統管理員和核准者群組的成員資格）限制為一組有限的授權人員。 任何特殊許可權角色變更都必須在24小時內撤銷存取權。 最後，每季檢查特殊許可權角色指派，並移除任何不必要或已過期的指派。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特殊許可權角色應使用雙因素驗證

使用多重要素驗證 (也稱為雙因素驗證) ，可讓核准者和系統管理員的互動式登入登入服務。

## <a name="certificate-service-operation-guidelines"></a>憑證服務操作指導方針

### <a name="operational-contacts"></a>營運連絡人

憑證服務必須有最新的安全性回應計畫（包含詳細的操作事件回應連絡人）。

### <a name="security-updates"></a>安全性更新

所有系統都必須持續監視並以最新的安全性更新更新。

> [!IMPORTANT]
> OPC 保存庫服務的 GitHub 存放庫會持續更新安全性修補程式。 監視這些更新，並定期將它們套用至服務。

### <a name="security-monitoring"></a>安全性監控

訂閱或實施適當的安全性監視。 例如，訂閱中央監視解決方案 (例如 Azure 資訊安全中心或 Microsoft 365 監視解決方案) ，並適當地進行設定，以確保安全性事件會傳輸到監視解決方案。

> [!IMPORTANT]
> 根據預設，OPC 保存庫服務會以 [Azure 應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/devops) 作為監視解決方案來部署。 強烈建議您新增安全性解決方案（例如 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/) ）。

### <a name="assess-the-security-of-open-source-software-components"></a>評估開放原始碼軟體元件的安全性

產品或服務內所使用的所有開放原始碼元件都必須沒有中等或更高的安全性弱點。

> [!IMPORTANT]
> 在連續整合組建期間，OPC 保存庫服務的 GitHub 存放庫會掃描所有元件是否有弱點。 在 GitHub 上監視這些更新，並定期將它們套用至服務。

### <a name="maintain-an-inventory"></a>維護清查

維護所有生產主機的資產清查 (包括持續性虛擬機器) 、裝置、所有內部 IP 位址範圍、Vip 和公用 DNS 功能變數名稱。 當您新增或移除系統、裝置 IP 位址、VIP 或公用 DNS 網域時，您必須在30天內更新清查。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>清查預設的 Azure OPC 保存庫微服務生產部署 

在 Azure 中：
- **App Service 方案**：服務主機的 App Service 方案。 預設值 S1。
- 適用于微服務的**App Service** ： OPC 保存庫服務主機。
- 範例應用程式的**App Service** ： OPC 保存庫範例應用程式主機。
- **Key Vault 標準**：儲存 web 服務的秘密和 Azure Cosmos DB 金鑰。
- **Key Vault Premium**：裝載簽發者 CA 金鑰、簽署服務，以及保存庫設定和應用程式私密金鑰的儲存。
- **Azure Cosmos DB**：應用程式和憑證要求的資料庫。 
- **Application Insights**： (適用于 web 服務和應用程式的選擇性) 監視解決方案。
- **Azure AD 應用程式註冊**：範例應用程式、服務和 edge 模組的註冊。

針對雲端服務，應該記錄所有用來部署服務的主機名稱、資源群組、資源名稱、訂用帳戶識別碼和租使用者識別碼。 

在 Azure IoT Edge 或本機 IoT Edge 伺服器：
- **OPC 保存庫 IoT Edge 模組**：支援 FACTORY 網路 OPC UA 全域探索伺服器。 

針對 IoT Edge 裝置，應該記錄主機名稱和 IP 位址。 

### <a name="document-the-certification-authorities-cas"></a>記錄憑證授權單位單位 (Ca) 

CA 階層檔必須包含所有運作中的 Ca。 這包括所有相關的次級 Ca、父 Ca 和根 Ca，即使它們不是由服務所管理。 除了正式檔之外，您還可以提供一組完整的非過期 CA 憑證。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中使用和產生的所有憑證，以取得檔。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>記錄所有憑證授權單位單位 (Ca 所發行的憑證) 

提供過去12個月內發行的一組完整的憑證。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中使用和產生的所有憑證，以取得檔。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>記錄安全地刪除密碼編譯金鑰的標準作業程式

在 CA 的存留期內，金鑰刪除可能只會很罕見。 這就是為什麼沒有使用者 Key Vault 已指派憑證的許可權，以及為何沒有公開任何 Api 來刪除簽發者 CA 憑證的原因。 安全地刪除憑證授權單位單位密碼編譯金鑰的手動標準作業程式，只能透過直接存取 Azure 入口網站中的 Key Vault 來使用。 您也可以在 Key Vault 中刪除憑證群組。 為確保立即刪除，請停用 [Key Vault 虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 功能。

## <a name="certificates"></a>憑證

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>憑證必須符合最低憑證設定檔

OPC 保存庫服務是向訂閱者發出終端實體憑證的線上 CA。 OPC 保存庫微服務會遵循預設執行中的這些指導方針。

- 所有憑證都必須包含下列 x.509 欄位，如下所示：
  - [版本] 欄位的內容必須是 v3。 
  - SerialNumber 欄位的內容必須包含至少8個位元組的熵，從 FIPS (聯邦資訊處理標準取得（) 140 核准的亂數產生器。<br>
    > [!IMPORTANT]
    > OPC 保存庫序號預設為20個位元組，並取自作業系統密碼編譯亂數產生器。 亂數產生器是在 Windows 裝置上核准的 FIPS 140，但在 Linux 上則否。 在選擇使用 Linux Vm 或 Linux docker 容器的服務部署時，請考慮這一點，因為基礎技術 OpenSSL 未核准 FIPS 140。
  - IssuerUniqueID 和 subjectUniqueID 欄位不得存在。
  - 終端實體憑證必須使用基本限制延伸來識別，並遵守 IETF RFC 5280。
  - 發行 CA 憑證的 pathLenConstraint 欄位必須設定為0。 
  - 擴充金鑰使用方法擴充功能必須存在，且必須包含 (Oid) 的最小擴充金鑰使用方法物件識別碼集合。 不得指定 anyExtendedKeyUsage OID (2.5.29.37.0) 。 
  -  (CDP) 擴充功能的 CRL 發佈點必須存在於簽發者 CA 憑證中。<br>
    > [!IMPORTANT]
    > CDP 延伸模組存在於 OPC 保存庫 CA 憑證中。 儘管如此，OPC UA 裝置也會使用自訂方法來散發 Crl。
  - 授權資訊存取延伸模組必須存在於訂閱者憑證中。<br>
    > [!IMPORTANT]
    > OPC 保存庫訂閱者憑證中有授權資訊存取延伸模組。 儘管如此，OPC UA 裝置也會使用自訂方法來散發簽發者 CA 資訊。
- 必須使用已核准的非對稱演算法、金鑰長度、雜湊函數和填補模式。
  - RSA 和 SHA-1 是唯一支援的演算法。
  - RSA 可用於加密、金鑰交換和簽章。
  - RSA 加密必須只使用 OAEP、RSA KEM 或 RSA-PSS 填補模式。
  - 需要大於或等於2048位的金鑰長度。
  - 使用 SHA-1 的雜湊演算法系列 (SHA256、SHA384 和 SHA512) 。
  - 一般存留期大於或等於20年的 RSA 根 CA 金鑰必須是4096位或更高的版本。
  - RSA 簽發者 CA 金鑰必須至少為2048位。 如果 CA 憑證到期日在2030之後，CA 金鑰必須是4096位或更高。
- 憑證存留期
  - 根 CA 憑證：根 Ca 的憑證有效期間上限不得超過25年。
  - 子 CA 或線上簽發者 CA 憑證：在線上且只發出訂閱者憑證的 Ca 的憑證有效期間上限不得超過6年。 針對這些 CAs，相關私用簽章金鑰不能超過3年的時間來發出新的憑證。<br>
    > [!IMPORTANT]
    > 簽發者憑證是在不含外部根 CA 的預設 OPC 保存庫微服務中產生的，其處理方式就像是線上子 CA，具有個別的需求和存留期。 預設存留期會設定為5年，金鑰長度大於或等於2048。
  - 所有非對稱金鑰都必須有最多5年的存留期，以及建議的1年存留期。<br>
    > [!IMPORTANT]
    > 根據預設，使用 OPC 保存庫發出的應用程式憑證存留期具有2年的存留期，且應該每年取代。 
  - 每次更新憑證時，就會以新的金鑰更新。
- 應用程式實例憑證中的 OPC UA 特定延伸模組
  - SubjectAltName 延伸模組包含應用程式 Uri 和主機名稱。 這些可能也包含 FQDN、IPv4 和 IPv6 位址。
  - KeyUsage 包括 digitalSignature、不可否認性、keyEncipherment 和 dataEncipherment。
  - ExtendedKeyUsage 包括 serverAuth 和 clientAuth。
  - AuthorityKeyIdentifier 是在已簽署的憑證中指定。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 金鑰和憑證必須符合最低需求

- **私密金鑰**： RSA 金鑰必須至少為2048位。 如果 CA 憑證到期日在2030之後，CA 金鑰必須是4096位或更高。
- **存留**期：在線上且只發出訂閱者憑證的 ca 的憑證有效期間上限不得超過6年。 針對這些 CAs，相關私用簽章金鑰不能超過3年的時間來發出新的憑證。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬體安全模組保護 CA 金鑰

OpcVault 使用 Azure Key Vault Premium，而金鑰會受到 FIPS 140-2 Level 2 硬體安全模組 (HSM) 的保護。 

Key Vault 使用的密碼編譯模組（HSM 或軟體）皆經過 FIPS 驗證。 建立或匯入為受 HSM 保護的金鑰會在 HSM 內部進行處理，並經過驗證至 FIPS 140-2 層級2。 建立或匯入為受軟體保護的金鑰，會在驗證至 FIPS 140-2 層級1的密碼編譯模組內進行處理。

## <a name="operational-practices"></a>營運實務

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>記錄和維護憑證註冊的標準營運 PKI 實務實務

記錄和維護標準作業程式 (Sop) ，以瞭解 CAs 發出憑證的方式，包括： 
- 如何識別和驗證「訂閱者」。 
- 憑證要求的處理和驗證方式 (如果適用的話，也包括憑證更新和重設金鑰要求的處理方式) 。 
- 發行的憑證如何散發給訂閱者。 

Opc 保存庫微服務程式設計會在 [opc](overview-opc-vault-architecture.md) 保存庫架構中說明，並 [管理 opc 保存庫憑證服務](howto-opc-vault-manage.md)。 這些實務遵循「OPC 統一架構規格第12部分：探索和全域服務」。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>記錄和維護憑證撤銷的標準操作 PKI 作法

[Opc 保存庫架構](overview-opc-vault-architecture.md)中會說明憑證撤銷程式，並[管理 opc 保存庫憑證服務](howto-opc-vault-manage.md)。
    
### <a name="document-ca-key-generation-ceremony"></a>檔 CA 金鑰產生儀式 

OPC 保存庫微服務中的簽發者 CA 金鑰產生已簡化，因為 Azure Key Vault 中的安全儲存體。 如需詳細資訊，請參閱 [管理 OPC 保存庫憑證服務](howto-opc-vault-manage.md)。

不過，當您使用外部根憑證授權單位時，CA 金鑰產生人員必須遵守下列需求。

CA 金鑰產生儀式必須針對記載的腳本執行，其中至少包含下列專案： 
- 角色和參與者責任的定義。
- 取得 CA 金鑰產生儀式的核准。
- 進行儀式所需的密碼編譯硬體和啟用材料。
- 硬體準備 (包括資產/設定資訊更新和登出) 。
- 作業系統安裝。
- 在 CA 金鑰產生儀式期間執行的特定步驟，例如： 
  - CA 應用程式安裝和設定。
  - CA 金鑰產生。
  - CA 金鑰備份。
  - CA 憑證簽署。
  - 在受保護的服務 HSM 中匯入簽署的金鑰。
  - CA 系統關機。
  - 準備儲存的材質。


## <a name="next-steps"></a>後續步驟

現在您已瞭解如何安全地管理 OPC 保存庫，您可以：

> [!div class="nextstepaction"]
> [使用 OPC 保存庫保護 OPC UA 裝置](howto-opc-vault-secure.md)
