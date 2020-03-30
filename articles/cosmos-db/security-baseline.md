---
title: 宇宙 DB 的 Azure 安全基線
description: 宇宙 DB 的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244268"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>宇宙 DB 的 Azure 安全基線

Cosmos DB 的 Azure 安全基線包含一些建議，可説明您改進部署的安全狀態。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：通過使用 Azure 專用連結，可以通過專用終結點連接到 Azure Cosmos 帳戶。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您還可以通過在網路安全性群組 （NSG） 上配置一組嚴格的出站規則並將該 NSG 與您的子閘道聯來降低資料洩露的風險。

您還可以使用服務終結點來保護 Azure Cosmos 帳戶。 通過啟用服務終結點，可以配置 Azure Cosmos 帳戶，以便僅允許從 Azure 虛擬網路的特定子網進行訪問。 啟用 Azure Cosmos DB 服務終結點後，可以限制對 Azure Cosmos 帳戶的訪問，該帳戶具有來自虛擬網路中子網的連接。

還可以使用 IP 防火牆保護存儲在 Azure Cosmos 帳戶中的資料。 Azure Cosmos DB 支援 IP 型存取控制，以提供輸入防火牆支援。 可以使用 Azure 門戶、Azure 資源管理器範本或通過 Azure CLI 或 Azure PowerShell 在 Azure Cosmos 帳戶上設置 IP 防火牆。

Azure 專用連結概述：https://docs.microsoft.com/azure/private-link/private-link-overview

如何為 Azure Cosmos DB 配置專用終結點：https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

如何創建具有安全配置的網路安全性群組：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何在 Cosmos DB 中配置 IP 防火牆：https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：使用 Azure 安全中心並遵循網路保護建議，以説明保護與 Azure Cosmos 帳戶相關的網路資源。

當虛擬機器部署在與 Azure Cosmos 帳戶相同的虛擬網路中時，可以使用網路安全性群組 （NSG） 來降低資料洩露的風險。 啟用 NSG 流日誌並將日誌發送到 Azure 存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

瞭解 Azure 安全中心提供的網路安全：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指南**：使用跨源資源分享 （CORS） 功能使在一個域下運行的 Web 應用程式能夠訪問另一個域中的資源。 網頁瀏覽器會實作稱為相同原始原則的安全性限制，它可防止網頁呼叫不同網域中的 API。 不過，CORS 提供了安全的方式來允許來源網域呼叫其他網域中的 API。 在為 Azure Cosmos 帳戶啟用 CORS 支援後，就只會對已通過驗證的要求進行評估，以根據您指定的規則來判斷是否允許這些要求。

配置跨源資源分享：https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：對 Azure 宇宙 DB 使用高級威脅保護 （ATP）。 Azure Cosmos DB 的 ATP 提供了一層額外的安全智慧，用於檢測訪問或利用 Azure Cosmos 帳戶的異常和潛在有害嘗試。 此保護層允許您處理威脅並將其與中央安全監控系統集成。

在與 Azure Cosmos 資料庫實例關聯的虛擬網路上啟用 DDoS 保護標準，以防止 DDoS 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通信。

如何配置 Azure 宇宙 DB 高級威脅保護：https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

如何配置 DDoS 保護：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 安全中心集成威脅情報：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：啟用網路安全性群組 （NSG） 流日誌並將日誌發送到存儲帳戶進行流量審核。 您可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指南**：對 Azure 宇宙 DB 使用高級威脅保護 （ATP）。 Azure Cosmos DB 的 ATP 提供了一層額外的安全智慧，用於檢測訪問或利用 Azure Cosmos 帳戶的異常和潛在有害嘗試。 此保護層允許您處理威脅並將其與中央安全監控系統集成。 

如何配置 Cosmos DB 高級威脅保護：https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指導**：不適用;建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：對於需要訪問 Azure Cosmos 帳戶的資源，請使用虛擬網路服務標記在網路安全性群組或 Azure 防火牆上定義網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱（例如 AzureCosmosDB），可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。

有關使用服務標記的詳細資訊：https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：使用 Azure 策略定義和實施網路資源的標準安全配置。 在"Microsoft.DocumentDB"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行 Azure Cosmos 資料庫實例的網路設定。 您還可以使用 Azure Cosmos DB 的內置策略定義，例如：

- 為 Cosmos DB 帳戶部署進階威脅防護

- Cosmos DB 應該使用虛擬網路服務端點

您還可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器範本、基於角色的存取控制 （RBAC） 和策略來簡化大規模 Azure 部署。 您可以通過版本控制輕鬆地將藍圖應用於新訂閱、環境和微調控制和管理。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何創建 Azure 藍圖：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：對與 Azure Cosmos DB 部署關聯的網路資源使用標記，以便從邏輯上將它們組織到分類中。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指南**：使用 Azure 活動日誌監視網路資源配置並檢測與 Azure Cosmos 資料庫實例相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。 

如何查看和檢索 Azure 活動日誌事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指南**：Microsoft 維護用於 Azure 資源（如 Azure Cosmos DB）的時間源，用於日誌中的時間戳記。


**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指南**：通過 Azure 監視器引入日誌以聚合 Azure Cosmos DB 生成的安全資料。 在 Azure 監視器中，使用日誌分析工作區來查詢和執行分析，並使用存儲帳戶進行長期/存檔存儲。 或者，您可以將資料和板載資料啟用到 Azure Sentinel 或協力廠商安全事件和事件管理 （SIEM）。 

如何為 Azure Cosmos DB 啟用診斷日誌：https://docs.microsoft.com/azure/cosmos-db/logging

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指南**：啟用 Azure Cosmos DB 的診斷設置，並將日誌發送到日誌分析工作區或存儲帳戶。 Azure Cosmos DB 中的診斷設置用於收集資源日誌。 這些日誌是按請求捕獲的，它們也稱為"資料平面日誌"。 資料平面操作的一些示例包括刪除、插入和讀取。 您還可以啟用 Azure 活動日誌診斷設置並將其發送到相同的日誌分析工作區。

如何為 Azure 宇宙資料庫啟用診斷設置：https://docs.microsoft.com/azure/cosmos-db/logging

如何為 Azure 活動日誌啟用診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：在 Azure 監視器中，根據組織的合規性法規設置與 Azure Cosmos 資料庫實例關聯的日誌分析工作區的日誌保留期。

如何設置日誌保留參數：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指南**：您可以在日誌分析工作區中執行查詢，以搜索術語、識別趨勢、分析模式並提供基於您收集的 Azure Cosmos DB 日誌的許多其他見解。

如何在日誌分析工作區中對 Azure 宇宙 DB 執行查詢：https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**：在 Azure 安全中心中，為 Azure Cosmos DB 啟用高級威脅保護，以監視安全性記錄檔和事件中的異常活動。 在 Azure Cosmos DB 中啟用診斷設置，並將日誌發送到日誌分析工作區。

 

您還可以將日誌分析工作區編上 Azure Sentinel，因為它提供了安全編排自動回應 （SOAR） 解決方案。 這允許創建行動手冊（自動解決方案），並用於修復安全問題。 此外，您還可以使用 Azure 監視器在日誌分析工作區中創建自訂日誌警報。

Azure 宇宙資料庫的威脅保護警報清單：https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

使用 Azure 監視器創建、查看和管理日誌警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指導**：不適用;Azure Cosmos DB 不處理或生成與惡意軟體相關的日誌。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指導**：不適用;Azure 宇宙資料庫不處理或生成與 DNS 相關的日誌。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊，請參閱[安全控制：標識和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

**指導**：可以使用 Azure 門戶中的標識和訪問控制項 （IAM） 窗格配置基於角色的存取控制 （RBAC） 並維護 Azure Cosmos 資料庫資源的清單。 這些角色應用於活動目錄中的使用者、組、服務主體和託管標識。 您可以將內置角色或自訂角色用於個人和組。

Azure 宇宙 DB 為 Azure Cosmos DB 中的常見管理方案提供了內置 RBAC。 在 Azure 活動目錄 （AD） 中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、組、服務主體或託管標識，以授予或拒絕對 Azure Cosmos 資料庫資源上的資源和操作的存取權限。

您還可以使用 Azure AD PowerShell 模組執行臨時查詢來發現屬於管理組成員的帳戶。 

此外，可以使用 Azure 活動目錄和特定于帳戶的主金鑰控制 Azure Cosmos DB 中的某些操作。  使用"禁用基於關鍵中繼資料寫入訪問"帳戶設置來控制金鑰訪問。

瞭解 Azure Cosmos DB 中的基於角色的存取控制：https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

使用 Azure Cosmos DB 操作（Microsoft.DocumentDB 命名空間）構建您自己的自訂角色：https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

在 Azure 活動目錄中創建新角色：https://docs.microsoft.com/azure/role-based-access-control/custom-roles

如何使用 PowerShell 獲取 Azure 活動目錄中的目錄角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 獲取 Azure 活動目錄中的目錄角色成員：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

僅限制使用者訪問資料操作：https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

**指南**：預設密碼或空白密碼的概念與 Azure AD 或 Azure Cosmos DB 無關。 相反，Azure Cosmos DB 使用兩種類型的金鑰來驗證使用者並提供對其資料和資源的訪問;主金鑰和資源權杖。 可以隨時重新生成金鑰。

瞭解對 Azure Cosmos DB 中資料的安全訪問：https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

如何重新生成 Azure 宇宙資料庫金鑰：https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

如何使用 Azure 活動目錄以程式設計方式訪問金鑰：https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

**指導**：不適用;Azure 宇宙資料庫不支援管理員帳戶。  所有訪問都與 Azure 活動目錄和基於 Azure 角色的存取控制 （RBAC） 集成。



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

**指南**：Azure Cosmos DB 使用兩種類型的金鑰來授權使用者，不支援資料平面級別的單一登入 （SSO）。 可通過 REST API 訪問 Cosmos DB 的控制平面，並支援 SSO。 要進行身份驗證，請將請求的授權標頭設置為從 Azure 活動目錄獲取的 JSON Web 權杖。

瞭解宇宙資料庫 REST API 的 Azure 資料庫：https://docs.microsoft.com/rest/api/cosmos-db/

使用 Azure 活動目錄瞭解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure 活動目錄多重要素驗證，並遵循 Azure 安全中心標識和訪問管理建議。

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用特權訪問工作站 （PAW） 進行多因素身份驗證配置以登錄和配置 Azure 資源。

瞭解特權訪問工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：對 Azure 宇宙 DB 使用高級威脅保護 （ATP）。 Azure Cosmos DB 的 ATP 提供了一層額外的安全智慧，用於檢測訪問或利用 Azure Cosmos 帳戶的異常和潛在有害嘗試。 此保護層允許您處理威脅並將其與中央安全監控系統集成。 

此外，當環境中發生可疑或不安全活動時，可以使用 Azure 活動目錄 （AD） 特權標識管理 （PIM） 生成日誌和警報。

使用 Azure AD 風險檢測查看有關風險使用者行為的警報和報告。

如何部署特權標識管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

瞭解 Azure AD 風險檢測：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：配置條件訪問策略的位置條件並管理命名位置。 使用命名位置，您可以創建 IP 位址範圍或國家和地區的邏輯分組。 您可以將對敏感資源（如 Azure Cosmos 資料庫實例）的訪問限制為已配置的命名位置。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （AD） 作為中央身份驗證和授權系統。 Azure AD 對靜態和傳輸中的資料使用增強式加密來保護資料。 Azure AD 還會對使用者憑據進行鹽漬、雜湊和安全地存儲。

如何創建和配置 Azure 活動目錄實例：https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何使用 Azure SQL 配置和管理 Azure 活動目錄身份驗證：https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指南**： Azure 活動目錄提供日誌以説明發現陳舊的帳戶。 此外，還可以使用 Azure 標識訪問審核有效地管理組成員身份、訪問企業應用程式和角色指派。 可以定期查看使用者的存取權限，以確保只有正確的使用者才能繼續訪問。

如何使用 Azure 標識訪問審核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：可以為 Azure 活動目錄使用者帳戶創建診斷設置，將稽核記錄和登錄日誌發送到日誌分析工作區，您可以在其中配置所需的警報。

如何將 Azure 活動日誌集成到 Azure 監視器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**：對 Azure 宇宙 DB 使用高級威脅保護 （ATP）。 Azure Cosmos DB 的 ATP 提供了一層額外的安全智慧，用於檢測訪問或利用 Azure Cosmos 帳戶的異常和潛在有害嘗試。 此保護層允許您處理威脅並將其與中央安全監控系統集成。 

您還可以使用 Azure AD 標識保護和風險檢測功能來配置自動回應，以識別與使用者身份相關的可疑操作。 此外，您可以將日誌引入 Azure Sentinel 以進行進一步調查。

如何查看 Azure 活動目錄風險登錄：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和啟用身份保護風險策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指南**：當前不可用;Cosmos DB 的 Azure 資料庫尚不支援客戶密碼箱。

客戶密碼箱支援服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心監視**：當前不可用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用標記來説明跟蹤存儲或處理敏感資訊的 Azure Cosmos 資料庫實例。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的訂閱和/或管理組。 Azure Cosmos 資料庫實例由虛擬網路/子網分隔，在網路安全性群組 （NSG） 或 Azure 防火牆中正確標記並加以保護。 應隔離存儲敏感性資料的 Azure Cosmos DB 實例。 通過使用 Azure 專用連結，可以通過專用終結點連接到 Azure Cosmos DB 實例帳戶。 專用終結點是虛擬網路中子網中的一組私人 IP 位址。 然後，您可以限制對所選私人 IP 位址的訪問。 

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何為 Azure Cosmos DB 配置專用終結點：https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

如何創建具有安全配置的網路安全性群組：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指南**：您可以為 Azure Cosmos DB 部署高級威脅保護，這將檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。 它當前可以觸發以下警報：

- 從異常位置訪問

- 不尋常的資料提取

此外，當使用虛擬機器訪問 Azure Cosmos 資料庫實例時，請使用專用連結、防火牆、網路安全性群組和服務標記來降低資料洩露的可能性。 Microsoft 管理 Azure Cosmos DB 的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

如何配置 Cosmos DB 高級威脅保護：https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**：與 Azure 宇宙 DB 的所有連接都支援 HTTPS。 Azure 宇宙 DB 還支援 TLS1.2。 可以強制實施最小 TLS 版本伺服器端。 為此，請聯繫[azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)。

宇宙資料庫安全性概述：https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：Azure Cosmos DB 尚不可用自動資料識別、分類和損失預防功能。 但是，您可以使用 Azure 認知搜索集成進行分類和資料分析。 如果需要合規性，您還可以實施協力廠商解決方案。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

索引 Azure 宇宙資料庫資料與 Azurehttps://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp認知搜索： ;bc_/azure/宇宙-db/麵包屑/toc.json

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指南**： Azure Cosmos DB 為 Azure Cosmos DB 中的常見管理方案提供了基於角色的內置存取控制 （RBAC）。 在 Azure 活動目錄中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、組、服務主體或託管標識，以授予或拒絕對 Azure Cosmos 資料庫資源上的資源和操作的存取權限。 角色指派範圍僅限於控制平面訪問，包括訪問 Azure Cosmos 帳戶、資料庫、容器和產品/服務（輸送量）。

如何在 Azure 宇宙 DB 中實現 RBAC：https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指導**：不適用;本指南用於計算資源。

Microsoft 管理 Cosmos DB 的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**：預設情況下，存儲在 Cosmos DB 中的所有使用者資料在靜態時進行加密。 沒有控制項可以將其關閉。 Azure Cosmos DB 對帳戶運行的所有區域使用 AES-256 加密。

預設情況下，Microsoft 管理用於加密 Azure Cosmos 帳戶中資料的金鑰。 您可以選擇使用自己的金鑰添加第二層加密。

使用 Azure Cosmos DB 瞭解靜態加密：https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

瞭解使用 Azure Cosmos DB 進行靜態加密的金鑰管理：https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

如何為 Azure Cosmos 資料庫帳戶配置客戶管理的金鑰：https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指導**：將 Azure 監視器與 Azure 活動日誌一起用於創建對 Azure Cosmos DB 生產實例進行更改時的警報。

如何為 Azure 活動日誌事件創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何為 Azure 活動日誌事件創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指南**：請按照 Azure Cosmos 資料庫實例的 Azure 安全中心的建議進行操作。 

Microsoft 在支援 Azure Cosmos DB 實例的基礎主機上執行系統修補和漏洞管理。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

Azure 安全中心中支援的功能：https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化作業系統修補程式管理解決方案

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比較背對背漏洞掃描

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用風險評級流程確定修復已發現漏洞的優先順序

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 門戶或 Azure 資源圖來發現訂閱中的所有資源（不限於 Azure Cosmos DB，但也包括計算、其他存儲、網路、埠和協定等資源）。  確保租戶中具有適當的許可權，並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

儘管可以通過資源圖發現經典 Azure 資源，但強烈建議今後創建和使用 Azure 資源管理器資源。

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 訂閱：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解基於 Azure 角色的存取控制：https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：將標記應用於 Azure Cosmos 資料庫實例和具有中繼資料的相關資源，以邏輯方式將它們組織到分類中。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

哪些 Azure Cosmos 資料庫資源支援標記：https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：在適當情況下，使用標記、管理組和單獨的訂閱來組織和跟蹤資產，包括但不限於 Azure Cosmos 資料庫資源。 定期協調庫存，確保及時從訂閱中刪除未經授權的資源。

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已批准的 Azure 資源和軟體標題的清單

**指導**：不適用;本指南適用于計算資源和整個 Azure。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 監視未經批准的 Azure 資源

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

- 不允許的資源類型

- 允許的資源類型

此外，使用 Azure 資源圖查詢/發現訂閱中的資源。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 監控計算資源中未經批准的軟體應用程式

**指導**：不適用;此基線用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 刪除未經批准的 Azure 資源和軟體應用程式

**指導**：不適用;本指南適用于計算資源和整個 Azure。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8： 僅使用已批准的應用程式

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9： 僅使用已批准的 Azure 服務

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

- 不允許的資源類型 

- 允許的資源類型

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒絕特定資源類型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="610-implement-approved-application-list"></a>6.10： 實施已批准的申請清單

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者通過腳本與 AzureResource Manager 交互的能力

**指南**：使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止訪問"來限制使用者與 Azure 資源管理器交互的能力。 這可以防止在高安全性環境中創建和更改資源。

如何配置條件訪問以阻止對 Azure 資源管理器的訪問：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制使用者在計算資源中執行腳本的能力

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或邏輯上隔離高風險應用程式

**指導**：不適用;本指南適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊，請參閱[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全配置

**指南**：使用 Azure 策略定義和實現 Cosmos 資料庫實例的標準安全配置。 在"Microsoft.DocumentDB"命名空間中使用 Azure 策略別名創建自訂策略以審核或強制執行 Cosmos 資料庫實例的配置。 您還可以使用 Azure Cosmos DB 的內置策略定義，例如：

- 為 Cosmos DB 帳戶部署進階威脅防護

- Cosmos DB 應該使用虛擬網路服務端點

如何查看可用的 Azure 策略別名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

**指導**：使用 Azure 策略 [拒絕] 和 [部署（如果不存在））在整個 Azure 資源中強制實施安全設置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

**指導**：不適用;本指南用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

**指南**：如果對 Cosmos DB 或相關資源使用自訂 Azure 策略定義，請使用 Azure 存儲庫安全地存儲和管理代碼。

Azure 存儲庫文檔： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devopshttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指導**：不適用;本指南用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指南**：使用"Microsoft.DocumentDB"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指導**：不適用;本指南用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：使用"Microsoft.DocumentDB"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 使用 Azure 策略 [審核]、[拒絕]和 [部署（如果不存在）]自動強制配置 Azure Cosmos 資料庫實例和相關資源。 

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指導**：不適用;本指南用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 Azure Cosmos 資料庫實例，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護 Azure Cosmos DB 金鑰管理。 確保啟用金鑰保存庫虛刪除。

如何與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 Azure Cosmos 資料庫實例，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護 Azure Cosmos DB 金鑰管理。

使用託管標識在 Azure 活動目錄 （AD） 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務（包括金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。

如何配置託管標識：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的憑據暴露

**指南**：實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置，如 Azure 金鑰保存庫。

如何設置憑據掃描程式：https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊，請參閱[安全控制：惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反惡意軟體

**指導**：不適用;本指南用於計算資源。 Microsoft 反惡意軟體在支援 Azure 服務的基礎主機上啟用（例如，Azure 應用服務），但它不在客戶內容上運行。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上載到非計算 Azure 資源的預掃描檔

**指南**：在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，Azure 應用服務），但它不在客戶內容上運行。

您有責任預先掃描上載到非計算 Azure 資源（包括 Azure Cosmos DB）的任何檔。 Microsoft 無法訪問客戶資料，因此無法代表您對客戶內容進行反惡意軟體掃描。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指導**：不適用;基準測試適用于計算資源。 Microsoft 反惡意軟體在支援 Azure 服務的基礎主機上啟用，但不在客戶內容上運行。


**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指南**：Azure Cosmos DB 每四小時拍攝一次資料的快照。 所有備份會儲存在另外的儲存體服務中，而且系統會全域複寫這些備份，以便為區域性災害提供復原功能。 任何時候都只會保留最後兩個快照集。 不過，如果容器或資料庫已刪除，Azure Cosmos DB 只會將指定容器或資料庫的現有快照集保留 30 天。 請與 Azure 支援聯繫以從備份進行還原。

瞭解 Azure 宇宙 DB 自動備份：https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指南**：Azure Cosmos DB 可定期自動備份資料。 如果刪除資料庫或容器，則可以提交支援票證或致電 Azure 支援以從自動連線備份還原資料。 Azure 支援僅適用于所選計畫，如標準計畫、開發人員計畫以及高於它們的計畫。 若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。 

如果使用金鑰保存庫為 Cosmos 資料庫實例存儲憑據，請確保定期自動備份金鑰。

瞭解 Azure 宇宙 DB 自動備份：https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何還原 Azure 宇宙 DB 中的資料：https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何備份金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**：如果刪除資料庫或容器，則可以提交支援票證或致電 Azure 支援以從自動連線備份還原資料。 Azure 支援僅適用于所選計畫，如標準計畫、開發人員計畫以及高於它們的計畫。 若要還原特定的備份快照集，Azure Cosmos DB 需要該資料在該快照的備份週期持續時間內為可用狀態。

使用 PowerShell 測試存儲在 Azure 金鑰保存庫中的秘密還原。 還原-AzureKeyVaultKey Cmdlet 在指定的金鑰保存庫中創建金鑰。 此鍵是輸入檔中備份金鑰的副本，與原始金鑰具有相同的名稱。

瞭解 Azure 宇宙 DB 自動備份：

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

如何還原 Azure 宇宙 DB 中的資料：

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

如何還原 Azure 金鑰保存庫機密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**：由於存儲在 Cosmos DB 中的所有使用者資料都是靜態和傳輸中加密的，因此無需執行任何操作。 換句話說，待用加密預設便會「開啟」。 沒有關閉或開啟的控制項。 Azure Cosmos DB 對帳戶運行的所有區域使用 AES-256 加密。

在金鑰保存庫中啟用虛刪除，以保護金鑰免受意外或惡意刪除。

瞭解 Azure 宇宙 DB 中的資料加密：https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

如何在金鑰保存庫中啟用虛刪除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心監視**：是

**責任**： 共用

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：為您的組織構建事件回應指南。 確保有書面事件回應計畫，定義人員的所有角色以及事件處理/管理階段，從檢測到事件後審查。

您還可以利用 NIST 的電腦安全性事件處理指南來説明創建您自己的事件回應計畫：https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

如何在 Azure 安全中心內配置工作流自動化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有關構建您自己的安全事件回應流程的指導：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微軟安全回應中心的事件剖析：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：安全中心為每個警報分配嚴重性，以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找或用於發出警報的分析中的信心，以及導致警報的活動背後的惡意程度。

此外，清楚地標記訂閱（對於前 生產，非 prod），並創建命名系統以明確識別和分類 Azure 資源。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="103-test-security-response-procedures"></a>10.3： 測試安全回應程式

**指導**：進行練習，以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距，並根據需要修訂計畫。

請參閱 NIST 出版物：IT 計畫和功能測試、培訓和鍛煉計劃指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件連絡人詳細資訊，並配置安全事件的警報通知

**指南**：如果 Microsoft 安全回應中心 （MSRC） 發現客戶的資料已被非法或未經授權的方訪問，Microsoft 將使用安全事件聯繫資訊與您聯繫。  事後查看事件，以確保問題得到解決。

如何設置 Azure 安全中心安全連絡人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 將安全警報納入事件回應系統

**指南**：使用"連續匯出"功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心資料連線器來資料流警報哨兵。

如何配置連續匯出：https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報資料流到 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自動回應安全警報

**指南**：使用 Azure 安全中心的工作流自動化功能，通過安全警報和建議上的"邏輯應用"自動觸發回應。

如何配置工作流自動化和邏輯應用：https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊，請參閱[安全控制：滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：對 Azure 資源進行定期滲透測試，並確保在 60 天內補救所有關鍵安全發現

**指南**： 遵循 Microsoft 參與規則，確保您的滲透測試不違反 Microsoft 策略：https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和執行的詳細資訊，如下所示：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**：不適用

**責任**： 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
