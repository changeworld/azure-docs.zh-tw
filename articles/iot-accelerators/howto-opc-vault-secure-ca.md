---
title: 如何安全地執行 OPC 保存庫憑證管理服務-Azure |Microsoft Docs
description: 說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務，並回顧其他要考慮的安全性指導方針。
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271703"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>安全地執行 OPC 保存庫憑證管理服務

本文說明如何在 Azure 中安全地執行 OPC 保存庫憑證管理服務，並回顧其他要考慮的安全性指導方針。

## <a name="roles"></a>角色

### <a name="trusted-and-authorized-roles"></a>信任和授權的角色

OPC 保存庫微服務可讓不同的角色存取服務的各個部分。

> [!IMPORTANT]
> 在部署期間，腳本只會將執行部署腳本的使用者新增為所有角色的使用者。 針對生產環境部署，您應該檢查此角色指派，並遵循下列指導方針來適當地重新設定。 這項工作需要在 Azure Active Directory （Azure AD）企業應用程式入口網站中手動指派角色和服務。

### <a name="certificate-management-service-roles"></a>憑證管理服務角色

OPC 保存庫微服務會定義下列角色：

- **讀者**：根據預設，租使用者中所有已驗證的使用者都有讀取權限。 
  - 應用程式和憑證要求的讀取存取權。 可以列出及查詢應用程式和憑證要求。 此外，也可以使用讀取權限來存取裝置探索資訊和公用憑證。
- **寫入器**：將寫入者角色指派給使用者，以加入特定工作的寫入權限。 
  - 應用程式和憑證要求的讀取/寫入存取權。 可以註冊、更新及取消註冊應用程式。 可以建立憑證要求，並取得核准的私密金鑰和憑證。 也可以刪除私密金鑰。
- **核准者**：核准者角色會指派給使用者，以核准或拒絕憑證要求。 角色不會包含任何其他角色。
  - 除了用來存取 OPC 保存庫微服務 API 的核准者角色之外，使用者也必須具有 Azure Key Vault 中的金鑰簽署許可權，才能簽署憑證。
  - [寫入器] 和 [核准者] 角色應指派給不同的使用者。
  - 核准者的主要角色是核准憑證要求的產生和拒絕。
- **系統管理員**：系統管理員角色會指派給使用者，以管理憑證群組。 角色不支援核准者角色，但包含寫入器角色。
  - 系統管理員可以藉由發出新的憑證撤銷清單（CRL）來管理憑證群組、變更設定，以及撤銷應用程式憑證。
  - 在理想情況下，寫入器、核准者和系統管理員角色會指派給不同的使用者。 為了增加安全性，擁有核准者或系統管理員角色的使用者也需要 Key Vault 中的金鑰簽署許可權，以頒發證書或更新簽發者 CA 憑證。
  - 除了微服務系統管理角色之外，此角色還包含（但不限於）：
    - 管理 CA 安全性作法的執行責任。
    - 管理憑證的產生、撤銷和暫停。 
    - 密碼編譯金鑰生命週期管理（例如，更新簽發者 CA 金鑰）。
    - 安裝、設定及維護操作 CA 的服務。
    - 服務的日常作業。 
    - CA 和資料庫備份與復原。

### <a name="other-role-assignments"></a>其他角色指派

當您執行服務時，也請考慮下列角色：

- 具有外部根憑證授權單位之憑證採購合約的商務擁有者（例如，當擁有者從外部 CA 購買憑證，或操作屬於外部 CA 的 CA 時）。
- 憑證授權單位單位的開發和驗證。
- 審查記錄。
- 在*授權*角色中，可協助支援 CA 或管理實體和雲端設施，但不直接受信任來執行 ca 作業的人員。 允許執行授權角色的一組工作人員也必須記載。

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>每季審查信任和授權角色的成員資格

至少每季查看受信任和授權角色的成員資格。 請確定每個角色中的一組人員（用於手動程式）或服務身分識別（用於自動化程式）都會保持最低。

### <a name="role-separation-between-certificate-requester-and-approver"></a>憑證要求者與核准者之間的角色隔離

憑證發佈程式必須在憑證要求者和憑證核准者角色（人員或自動化系統）之間強制執行角色分離。 憑證發行必須由憑證核准者角色授權，確認憑證要求者已獲授權可取得憑證。 持有憑證核准者角色的人員必須是正式授權的人員。

### <a name="restrict-assignment-of-privileged-roles"></a>限制特殊許可權角色的指派

您應該限制特殊許可權角色的指派，例如授與系統管理員和核准者群組的成員資格給一組有限的授權人員。 任何特殊許可權角色變更都必須在24小時內撤銷存取權。 最後，每季檢查特殊許可權角色指派，並移除任何不必要或已過期的指派。

### <a name="privileged-roles-should-use-two-factor-authentication"></a>特殊許可權角色應該使用雙重要素驗證

針對核准者和系統管理員的互動式登入，使用多重要素驗證（也稱為雙因素驗證）來進行服務。

## <a name="certificate-service-operation-guidelines"></a>憑證服務操作指導方針

### <a name="operational-contacts"></a>營運連絡人

憑證服務必須在檔案上具有最新的安全性回應計畫，其中包含詳細的操作事件回應連絡人。

### <a name="security-updates"></a>安全性更新

所有系統都必須以最新的安全性更新持續受到監視和更新。

> [!IMPORTANT]
> OPC 保存庫服務的 GitHub 儲存機制會持續更新安全性修補程式。 監視這些更新，並定期將它們套用至服務。

### <a name="security-monitoring"></a>安全性監控

訂閱或執行適當的安全性監視。 例如，訂閱中央監視解決方案（例如 Azure 資訊安全中心或 Office 365 監視解決方案），並適當地進行設定，以確保安全性事件會傳送到監視解決方案。

> [!IMPORTANT]
> 根據預設，OPC 保存庫服務會以[Azure 應用程式 Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops)作為監視解決方案進行部署。 強烈建議您新增[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)之類的安全性解決方案。

### <a name="assess-the-security-of-open-source-software-components"></a>評估開放原始碼軟體元件的安全性

在產品或服務內使用的所有開放原始碼元件，都必須擁有中等或更高的安全性弱點。

> [!IMPORTANT]
> 在持續整合組建期間，OPC 保存庫服務的 GitHub 儲存機制會掃描所有元件是否有弱點。 在 GitHub 上監視這些更新，並定期將它們套用至服務。

### <a name="maintain-an-inventory"></a>維護清查

維護所有生產主機（包括持續性虛擬機器）、裝置、所有內部 IP 位址範圍、Vip 和公用 DNS 功能變數名稱的資產清查。 每當您新增或移除系統、裝置 IP 位址、VIP 或公用 DNS 網域時，都必須在30天內更新清查。

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>預設 Azure OPC 保存庫微服務生產環境部署的清查 

在 Azure 中：
- **App Service 方案**：服務主機的 App Service 方案。 預設值為 S1。
- 微服務的**App Service** ： OPC 保存庫服務主機。
- 範例應用程式的**App Service** ： OPC 保存庫範例應用程式主機。
- **Key Vault 標準**：用來儲存 web 服務的秘密和 Azure Cosmos DB 金鑰。
- **Key Vault Premium**：裝載簽發者 CA 金鑰、簽署服務，以及保存庫設定和應用程式私密金鑰的儲存。
- **Azure Cosmos DB**：應用程式和憑證要求的資料庫。 
- **Application Insights**：（選擇性）適用于 web 服務和應用程式的監視解決方案。
- **Azure AD 應用程式註冊**：範例應用程式、服務和 edge 模組的註冊。

針對雲端服務，您應該記載所有用來部署服務的主機名稱、資源群組、資源名稱、訂用帳戶識別碼和租使用者識別碼。 

在 Azure IoT Edge 或本機 IoT Edge 伺服器中：
- **OPC 保存庫 IoT Edge 模組**：支援 FACTORY 網路 OPC UA 全域探索伺服器。 

針對 IoT Edge 裝置，必須記載主機名稱和 IP 位址。 

### <a name="document-the-certification-authorities-cas"></a>記錄憑證授權單位單位（Ca）

CA 階層檔必須包含所有運作中的 Ca。 這包括所有相關的附屬 Ca、父 Ca 和根 Ca，即使它們不是由服務所管理。 除了正式的檔外，您還可以提供一組完整的非過期 CA 憑證。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中所使用和產生的所有憑證，以提供檔。

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>記錄所有憑證授權單位單位（Ca）發行的憑證

提供在過去12個月內發出的一組完整憑證。

> [!NOTE]
> OPC 保存庫範例應用程式支援下載服務中所使用和產生的所有憑證，以提供檔。

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>記錄安全刪除密碼編譯金鑰的標準作業程式

在 CA 的存留期間，金鑰刪除可能只會發生很少的情況。 這就是為什麼沒有任何使用者已指派 Key Vault 憑證刪除許可權，以及為什麼未公開任何 Api 來刪除簽發者 CA 憑證。 安全地刪除憑證授權單位單位密碼編譯金鑰的手動標準作業程式，僅能直接存取 Azure 入口網站中的 Key Vault。 您也可以在 Key Vault 中刪除憑證群組。 若要確保立即刪除，請停用[Key Vault 虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能。

## <a name="certificates"></a>憑證

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>憑證必須符合最低憑證設定檔

OPC 保存庫服務是一種線上 CA，會向訂閱者發出終端實體憑證。 OPC 保存庫微服務會遵循預設執行中的這些指導方針。

- 所有憑證都必須包含下列 x.509 欄位，如下所示：
  - [版本] 欄位的內容必須是 v3。 
  - SerialNumber 欄位的內容必須包含至少8個位元組的熵，從 FIPS （聯邦資訊處理標準）140核准的亂數產生器取得。<br>
    > [!IMPORTANT]
    > OPC 保存庫序號預設為20個位元組，而且是從作業系統密碼編譯亂數字產生器取得。 亂數字產生器是在 Windows 裝置上核准的 FIPS 140，而不是在 Linux 上。 選擇使用 Linux Vm 或 Linux docker 容器的服務部署時，請考慮這一點，基礎技術 OpenSSL 不會核准 FIPS 140。
  - IssuerUniqueID 和 subjectUniqueID 欄位不得存在。
  - 您必須根據 IETF RFC 5280，使用基本限制延伸來識別終端實體憑證。
  - 發行 CA 憑證的 pathLenConstraint 欄位必須設定為0。 
  - 擴充金鑰使用方式延伸必須存在，而且必須包含最小的擴充金鑰使用方法物件識別碼（Oid）集。 不得指定 anyExtendedKeyUsage OID （2.5.29.37.0）。 
  - CRL 發佈點（CDP）延伸模組必須存在於簽發者 CA 憑證中。<br>
    > [!IMPORTANT]
    > CDP 延伸模組存在於 OPC 保存庫 CA 憑證中。 儘管如此，OPC UA 裝置會使用自訂方法來散發 Crl。
  - 授權單位資訊存取延伸模組必須存在於訂閱者憑證中。<br>
    > [!IMPORTANT]
    > 許可權資訊存取延伸模組存在於 OPC 保存庫訂閱者憑證中。 儘管如此，OPC UA 裝置會使用自訂方法來散發簽發者 CA 資訊。
- 必須使用已核准的非對稱演算法、金鑰長度、雜湊函數和填補模式。
  - RSA 和 SHA-1 是唯一支援的演算法。
  - RSA 可以用於加密、金鑰交換和簽章。
  - RSA 加密必須僅使用 OAEP、RSA-KEM 或 RSA-PSS 填補模式。
  - 需要大於或等於2048位的金鑰長度。
  - 使用 SHA-2 系列雜湊演算法（SHA256、SHA384 和 SHA512）。
  - 一般存留期大於或等於20年的 RSA 根 CA 金鑰必須是4096位或更大。
  - RSA 簽發者 CA 金鑰必須至少為2048位。 如果 CA 憑證到期日在2030之後，CA 金鑰必須是4096位或更大。
- 憑證存留期
  - 根 CA 憑證：根 Ca 的憑證有效期間上限不得超過25年。
  - 子 CA 或線上簽發者 CA 憑證：已上線且只發出訂閱者憑證之 Ca 的憑證有效期間上限不得超過6年。 對於這些 Ca，相關的私用簽章金鑰不得超過3年的時間來發行新的憑證。<br>
    > [!IMPORTANT]
    > 簽發者憑證（因為在沒有外部根 CA 的預設 OPC 保存庫微服務中產生）會被視為線上子 CA，並具有各自的需求和存留期。 預設存留期設定為5年，索引鍵長度大於或等於2048。
  - 所有非對稱金鑰都必須有最多5年的存留期，以及建議的1年存留期。<br>
    > [!IMPORTANT]
    > 根據預設，使用 OPC 保存庫發行之應用程式憑證的存留期有2年，應每年取代。 
  - 每當更新憑證時，就會以新的金鑰更新。
- 應用程式實例憑證中的 OPC UA 特定延伸模組
  - SubjectAltName 延伸模組包含應用程式 Uri 和主機名稱。 這些可能也包括 FQDN、IPv4 和 IPv6 位址。
  - KeyUsage 包括 digitalSignature、不可否認性、keyEncipherment 和 dataEncipherment。
  - ExtendedKeyUsage 包含 serverAuth 和 clientAuth。
  - AuthorityKeyIdentifier 是在已簽署的憑證中指定。

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA 金鑰和憑證必須符合最低需求

- **私密金鑰**： RSA 金鑰必須至少有2048位。 如果 CA 憑證到期日在2030之後，CA 金鑰必須是4096位或更大。
- **存留**期：已上線且只發出訂閱者憑證之 ca 的憑證有效期間上限不得超過6年。 對於這些 Ca，相關的私用簽章金鑰不得超過3年的時間來發行新的憑證。

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>使用硬體安全模組來保護 CA 金鑰

OpcVault 會使用 Azure Key Vault Premium，而金鑰會受到 FIPS 140-2 Level 2 硬體安全模組（HSM）的保護。 

Key Vault 使用的密碼編譯模組（HSM 或軟體）是否經過 FIPS 驗證。 建立或匯入為受 HSM 保護的金鑰會在 HSM 內進行處理，並驗證為 FIPS 140-2 層級2。 建立或匯入為受軟體保護的金鑰會在驗證為 FIPS 140-2 層級1的密碼編譯模組內進行處理。

## <a name="operational-practices"></a>操作實務

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>記錄及維護憑證註冊的標準操作 PKI 實務

記載及維護 Ca 如何頒發證書的標準操作程式（Sop），包括： 
- 如何識別和驗證訂閱者。 
- 憑證要求的處理和驗證方式（如果適用的話，也包括如何處理憑證更新和重設金鑰要求）。 
- 發行的憑證如何散發給訂閱者。 

Opc 保存庫微服務的工作區會在[opc 保存庫架構](overview-opc-vault-architecture.md)和[管理 OPC 保存庫憑證服務](howto-opc-vault-manage.md)中加以說明。 做法遵循「OPC 統一架構規格第12部分：探索和全域服務」。


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>記載及維護憑證撤銷的標準操作 PKI 作法

憑證撤銷程式會在 Opc 保存[庫架構](overview-opc-vault-architecture.md)和[管理 opc 保存庫憑證服務](howto-opc-vault-manage.md)中加以說明。
    
### <a name="document-ca-key-generation-ceremony"></a>檔 CA 金鑰產生儀式 

因為 Azure Key Vault 中的安全儲存體，所以已簡化 OPC 保存庫微服務中的簽發者 CA 金鑰產生。 如需詳細資訊，請參閱[管理 OPC 保存庫憑證服務](howto-opc-vault-manage.md)。

不過，當您使用外部根憑證授權單位時，CA 金鑰產生儀式必須符合下列需求。

CA 金鑰產生儀式必須針對已記載的腳本執行，其中至少包含下列專案： 
- 角色和參與者責任的定義。
- CA 金鑰產生儀式的核准。
- 儀式所需的密碼編譯硬體和啟用材質。
- 硬體準備（包括資產/設定資訊更新和登出）。
- 作業系統安裝。
- CA 金鑰產生儀式期間所執行的特定步驟，例如： 
  - CA 應用程式安裝和設定。
  - CA 金鑰產生。
  - CA 金鑰備份。
  - CA 憑證簽署。
  - 在服務的受保護 HSM 中匯入帶正負號的金鑰。
  - CA 系統關閉。
  - 準備儲存資料。


## <a name="next-steps"></a>後續步驟

既然您已瞭解如何安全地管理 OPC 保存庫，您可以：

> [!div class="nextstepaction"]
> [使用 OPC 保存庫保護 OPC UA 裝置](howto-opc-vault-secure.md)
