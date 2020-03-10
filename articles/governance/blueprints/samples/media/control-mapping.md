---
title: 媒體藍圖範例控制項
description: 媒體藍圖範例的控制項對應。 每個控制項都會對應至一或多個可協助評量的 Azure 原則。
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201735"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>媒體藍圖範例的控制項對應

下列文章詳細說明 Azure 藍圖媒體藍圖範例與媒體控制項的對應情形。 如需控制項的詳細資訊，請參閱[媒體](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

以下是與**媒體**控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\]：稽核媒體控制項**內建原則計畫。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md) \(英文\)。

## <a name="access-control"></a>存取控制

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 - 確保沒有根存取金鑰存在

- \[預覽\]：部署必要條件，以稽核信任根未包含指定憑證的 Windows VM

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 - 密碼、PIN 和權杖都必須受到保護

- \[預覽\]：部署必要條件以稽核最小密碼長度未限制為 14 個字元的 Windows VM

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - 禁止共用帳戶存取

- 除了 RootManageSharedAccessKey 外，應從服務匯流排命名空間移除所有授權規則

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 - 系統必須將存取權限制於獲得授權的使用者。

- 稽核不受限制的儲存體帳戶網路存取

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14 - 系統必須強制執行存取權限。

- \[預覽\]：部署必要條件，以稽核 [使用者權限指派] 中的 Windows VM 組態

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15 - 防止未經授權存取安全性相關資訊或功能。

- \[預覽\]：顯示 [安全性選項 - 系統設定] 中的 Windows VM 組態稽核結果

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - 職責劃分必須透過適當的角色指派來強制執行。

- [預覽\]：應在 Kubernetes 服務上使用角色型存取控制 (RBAC)

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - 確保系統不會同時連線受信任的網路和不受信任的網路。

- \[預覽\]：部署必要條件，以稽核 [安全性選項 - 網路存取] 中的 Windows VM 組態

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 和 AC-1.43 - 非員工的遠端存取必須受限於只允許存取特別核准的資訊系統

- \[預覽\]：從允許不使用密碼從帳戶遠端連線的 Linux VM 顯示稽核結果

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 - 記錄所有資訊系統元件的安全性相關事件。

- 應在 Logic Apps 中啟用診斷記錄

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 - 確保所有雲端主控台使用者都已啟用多重要素驗證 (MFA)。

- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。

## <a name="auditing--logging"></a>稽核與記錄

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 - 必須記錄成功和失敗事件。

- 應在搜尋服務中啟用診斷記錄

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16 - 網路裝置/執行個體必須記錄由任何該網路裝置/SQL Server 執行個體 (ELB、Web 應用程式防火牆等) 歸類為重大安全性事件的事件

- \[預覽\]：顯示 [安全性選項 - 帳戶] 中的 Windows VM 組態稽核結果

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 - 伺服器/執行個體必須記錄任何由該伺服器/執行個體歸類為重大安全性事件的事件

- \[預覽\]：顯示 [安全性選項 - 帳戶] 中的 Windows VM 組態稽核結果

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - 網域事件必須記錄任何由網域管理軟體歸類為重大或高安全性事件的事件

- \[預覽\]：顯示 [安全性選項 - 帳戶] 中的 Windows VM 組態稽核結果
- \[預覽\]：部署必要條件，以稽核 [安全性選項 - Microsoft 網路用戶端] 中的 Windows VM 組態

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - 網域事件必須記錄任何由網域安全性控制項歸類為重大安全性事件的事件

- \[預覽\]：顯示 [安全性選項 - 帳戶] 中的 Windows VM 組態稽核結果

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 - 網域事件必須記錄對網域記錄的任何存取權或變更

- \[預覽\]：顯示 [安全性選項 - 修復主控台] 中的 Windows VM 組態稽核結果

## <a name="cryptographic-controls"></a>密碼編譯控制項

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - 應用程式和系統必須使用目前的密碼編譯解決方案來保護資料。

- 應在 SQL 資料庫上啟用透明資料加密
- 應啟用透明資料加密，以保護待用資料，並滿足合規性需求

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 - 數位憑證必須由已核准的憑證授權單位簽署。

- \[預覽\]：顯示憑證在指定天數內到期的 Windows VM 稽核結果

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 - 必須將數位憑證唯一指派給使用者或裝置。

- \[預覽\]：部署必要條件，以稽核憑證在指定天數內到期的 Windows VM

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 - 必須儲存密碼編譯資料，以在保留記錄的時間內將記錄解密。

- 應在虛擬機器上套用磁碟加密
- Azure 資訊安全中心會依建議監視未啟用磁碟加密的 VM

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 - 秘密和私密金鑰必須以安全的方式儲存。

- 應在 SQL 資料庫上啟用透明資料加密
- 應啟用透明資料加密，以保護待用資料，並滿足合規性需求

## <a name="change--config-management"></a>變更和設定管理

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 - 只有獲得授權的使用者可以在系統上實作已核准的變更。

- 您應在機器上安裝系統更新
- Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12- 維護最新、完整、正確且隨時可用的資訊系統基準組態。

- 您應在機器上安裝系統更新
- Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - 運用自動化工具來維護資訊系統的基準組態。

- 您應在機器上安裝系統更新
- Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - 識別並停用不必要和/或不安全的功能、連接埠、通訊協定和服務。

- 網路介面應停用 IP 轉送
- \[預覽\]：應停用虛擬機器上的 IP 轉送

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - 監視對安全性組態設定的變更。

- 部署網路安全性群組的診斷設定

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - 確保只有獲得授權的軟體和更新會安裝在公司系統上。

- 您應在機器上安裝系統更新
- Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新

## <a name="identity--authentication"></a>身分識別和驗證

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 - 必須將使用者帳戶唯一指派給個人，以便存取未歸類為「公用」的資訊。 必須使用標準化邏輯格式來建構帳戶識別碼。

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。

## <a name="network-security"></a>網路安全性

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - 網路裝置管理功能的存取受限於獲得授權的使用者。

- \[預覽\]：部署必要條件，以稽核 [安全性選項 - 網路存取] 中的 Windows VM 組態

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 - 所有網路裝置都必須使用其最安全的組態進行設定。

- \[預覽\]：部署必要條件，以稽核 [安全性選項 - 網路存取] 中的 Windows VM 組態

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - 必須定期核准和稽核透過防火牆對系統進行的所有網路連線。

- \[預覽\]：顯示 [Windows 防火牆屬性] 中的 Windows VM 組態稽核結果

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 - 信任的網路與任何未受信任或公用網路之間的任何界限必須存在適當的控制項。

- \[預覽\]：部署必要條件，以稽核 [Windows 防火牆屬性] 中的 Windows VM 組態

## <a name="security-planning"></a>安全性規劃

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - 必須識別可能對公司資訊和內容的機密性、完整性或可用性造成負面影響的威脅，以及發生其發生的可能性。

- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]

### <a name="security-continuity"></a>安全性持續性

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - 長期儲存體中的資料必須可在整個保留期間內存取並受到保護，以防止媒體降級和技術變更。

- SQL 伺服器應設定有 90 天以上的稽核保留天數。
- 對稽核保留期間設為 90 天以下的 SQL 伺服器進行稽核。

## <a name="system-integrity"></a>系統完整性

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - 只有獲得授權的人員可以監視網路和使用者活動。

- 應修復 SQL 資料庫的弱點
- 監視弱點評定掃描結果及如何補救資料庫弱點的建議。

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - 網際網路對應的系統必須具有入侵偵測。

- 在 SQL 伺服器上部署威脅偵測

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - 應在全公司實作標準化集中管理的反惡意程式碼軟體。

- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 - 反惡意程式碼軟體必須至少每週掃描電腦和媒體一次。

- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組

## <a name="vulnerability-management"></a>弱點管理

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - 確保每月掃描應用程式是否有弱點。

- 應修復虛擬機器擴展集上安全性組態的弱點
- 稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - 確保已識別出弱點，將其與威脅配對，並且進行風險評估。

- 應修復虛擬機器擴展集上安全性組態的弱點
- 稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 - 確保已在雙方同意的時間表內補救已識別的弱點。

- 應修復虛擬機器擴展集上安全性組態的弱點
- 稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - 弱點管理系統的存取和使用必須受限於獲得授權的人員。

- 應修復虛擬機器擴展集上安全性組態的弱點
- 稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。

> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。 

## <a name="next-steps"></a>後續步驟

您已檢閱媒體藍圖範例的控制項對應。 接下來，請瀏覽下列文章，以深入了解概觀及部署此範例的方法：

> [!div class="next step action"]
> [媒體藍圖 - 概觀](./control-mapping.md)
> [媒體藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
