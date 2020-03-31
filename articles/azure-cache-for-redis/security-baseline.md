---
title: 用於雷瑞斯 Azure 緩存的 Azure 安全基線
description: 用於雷瑞斯 Azure 緩存的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 364b87e4d64b8cc65fdf293032f4340bddec957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479133"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>用於雷瑞斯 Azure 緩存的 Azure 安全基線

Redis Azure 緩存的 Azure 安全基線包含一些建議，可説明您改進部署的安全狀況。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：在虛擬網路 （VNet） 中部署 Redis 實例的 Azure 緩存。 VNet 是雲端中的私人網路。 當 Azure Cache for Redis 執行個體是以 VNet 設定時，它將無法公開定址，而只能從 VNet 中的虛擬機器和應用程式存取。

您還可以指定具有開始和結束 IP 位址範圍的防火牆規則。 設定防火牆規則時，只有來自指定 IP 位址範圍的用戶端連線可以連接至快取。

如何為 Redis 配置高級 Azure 緩存的虛擬網路支援：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何為 Redis 防火牆規則配置 Azure 緩存：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：當虛擬機器部署在與 Redis 實例的 Azure 緩存相同的虛擬網路中時，可以使用網路安全性群組 （NSG） 來降低資料洩露的風險。 啟用 NSG 流日誌並將日誌發送到 Azure 存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：Azure 虛擬網路 （VNet） 部署為 Redis 的 Azure 緩存提供了增強的安全性和隔離性，以及子網、存取控制策略和其他功能，以進一步限制訪問。 在 VNet 中部署時，Redis 的 Azure 緩存不可公開定址，並且只能從 VNet 中的虛擬機器和應用程式訪問。

在與 Azure 緩存關聯的 Redis 實例 VNets 上啟用 DDoS 保護標準，以防止分散式阻斷服務 （DDoS） 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通信。

如何為 Redis 配置高級 Azure 緩存的虛擬網路支援：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

使用 Azure 門戶管理 Azure DDoS 保護標準：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：當虛擬機器部署在與 Redis 實例的 Azure 緩存相同的虛擬網路中時，可以使用網路安全性群組 （NSG） 來降低資料洩露的風險。 啟用 NSG 流日誌並將日誌發送到 Azure 存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指南**：在 Azure 應用服務或計算實例上運行的 Web 應用程式使用 Azure 緩存時，在 Azure 虛擬網路 （VNet） 中部署所有資源，並在 Web 應用程式閘道上使用 Azure Web 應用程式防火牆 （WAF） 進行保護。 將 WAF 配置為在"預防模式"中運行。 預防模式阻止規則檢測到的入侵和攻擊。 攻擊者會收到「403 未經授權存取」例外狀況，且連線會關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

或者，您可以從 Azure 應用商店中選擇一個產品/服務，該產品/服務支援具有有效負載檢查和/或異常檢測功能的 IDS/IPS 功能。

瞭解 Azure WAF 功能：

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：使用虛擬網路服務標記定義網路安全性群組 （NSG） 或 Azure 防火牆上的網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱（例如 ApiManagement），可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。

您還可以使用應用程式安全性群組 （ASG） 來説明簡化複雜的安全配置。 ASG 使您能夠將網路安全配置為應用程式結構的自然擴展，從而允許您對虛擬機器進行分組並根據這些組定義網路安全性原則。

虛擬網路服務標籤：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

應用程式安全性群組：

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：使用 Azure 策略為 Redis 實例定義和實施與 Azure 緩存相關的網路資源的標準安全配置。 在"Microsoft.Cache"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略，以審核或強制執行 Redis 實例的 Azure 緩存的網路設定。 您還可以使用內置策略定義，例如：

應該只允許對 Redis Cache 的安全連線

應啟用 DDoS 保護標準

您還可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器 （ARM） 範本、基於角色的存取控制 （RBAC） 和策略，來簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱和環境，並通過版本控制微調控制和管理。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何創建 Azure 藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：對與 Azure 緩存關聯的網路資源使用標記進行 Redis 部署，以便從邏輯上將它們組織到分類中。

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指南**：使用 Azure 活動日誌監視網路資源配置，並檢測與 Redis 實例的 Azure 緩存相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。

如何查看和檢索 Azure 活動日誌事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中創建警報：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指南**：Microsoft 維護用於 Azure 資源的時間源，例如日誌中用於 Redis 的 Azure 緩存。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指導**：啟用 Azure 活動日誌診斷設置，並將日誌發送到日誌分析工作區、Azure 事件中心或 Azure 存儲帳戶進行存檔。 活動日誌提供對在控制平面級別為 Redis 實例的 Azure 緩存執行的操作的見解。 使用 Azure 活動日誌資料，可以確定在 Redis 實例的 Azure 緩存的控制平面級別執行的任何寫入操作（PUT、POST、DELETE）的"什麼、誰和時間"。

如何為 Azure 活動日誌啟用診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指導**：啟用 Azure 活動日誌診斷設置，並將日誌發送到日誌分析工作區、Azure 事件中心或 Azure 存儲帳戶進行存檔。 活動日誌提供對在控制平面級別為 Redis 實例的 Azure 緩存執行的操作的見解。 使用 Azure 活動日誌資料，可以確定在 Redis 實例的 Azure 緩存的控制平面級別執行的任何寫入操作（PUT、POST、DELETE）的"什麼、誰和時間"。

雖然通過啟用診斷設置提供指標，但資料平面上的稽核記錄記錄仍不適用於 Redis 的 Azure 緩存。

如何為 Azure 活動日誌啟用診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：在 Azure 監視器中，根據組織的合規性法規為與 Azure 緩存關聯的日誌分析工作區設置日誌保留期。

請注意，資料平面上的稽核記錄記錄尚未可用於 Redis 的 Azure 緩存。

如何設置日誌保留參數：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指導**：啟用 Azure 活動日誌診斷設置並將日誌發送到日誌分析工作區。 在日誌分析中執行查詢，以搜索術語、識別趨勢、分析模式，並根據可能為 Redis 為 Azure 緩存收集的活動日誌資料提供許多其他見解。

請注意，資料平面上的稽核記錄記錄尚未可用於 Redis 的 Azure 緩存。

如何為 Azure 活動日誌啟用診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

如何在 Azure 監視器中的日誌分析工作區中收集和分析 Azure 活動日誌：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**：您可以配置為根據與 Redis 實例的 Azure 緩存相關的指標和活動日誌來接收警報。 Azure 監視器允許您配置警報以發送電子郵件通知、調用 Webhook 或調用 Azure 邏輯應用。

雖然通過啟用診斷設置提供指標，但資料平面上的稽核記錄記錄仍不適用於 Redis 的 Azure 緩存。

如何為雷定配置 Azure 緩存的警報：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指導**：不適用;Redis 的 Azure 緩存不處理或生成與惡意軟體相關的日誌。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指導**：不適用;Redis 的 Azure 緩存不處理或生成 DNS 相關日誌。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

**指導**：不適用;本指南用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊，請參閱[安全控制：標識和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

**指南**： Azure 活動目錄 （AD） 具有必須顯式分配且可查詢的內置角色。 使用 Azure AD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

如何使用 PowerShell 在 Azure AD 中獲取目錄角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 獲取 Azure AD 中的目錄角色成員：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

**指南**：通過 Azure 活動目錄 （AD） 控制對雷瑞斯 Azure 緩存的平面訪問。 Azure AD 沒有預設密碼的概念。 

通過便捷鍵控制對 Redis Azure 緩存的資料平面訪問。 這些鍵由連接到緩存的用戶端使用，並可以隨時重新生成。

不建議您將預設密碼構建到應用程式中。 相反，您可以將密碼存儲在 Azure 金鑰保存庫中，然後使用 Azure 活動目錄檢索密碼。

如何為 Redis 訪問金鑰重新生成 Azure 緩存：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

**指導**：圍繞使用專用管理帳戶創建標準操作程式。 使用 Azure 安全中心標識和訪問管理監視管理帳戶數。

此外，為了説明跟蹤專用管理帳戶，可以使用 Azure 安全中心或內置 Azure 策略的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶

- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如何使用 Azure 安全中心監視標識和訪問（預覽）：https://docs.microsoft.com/azure/security-center/security-center-identity-access

如何使用 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

**指南**：Redis 的 Azure 緩存使用訪問金鑰組使用者進行身份驗證，不支援資料平面級別的單一登入 （SSO）。 通過 REST API 可以訪問 Redis Azure 緩存的控制平面，並支援 SSO。 要進行身份驗證，請將請求的授權標頭設置為從 Azure 活動目錄獲取的 JSON Web 權杖。

瞭解 Redis REST API 的 Azure 緩存：https://docs.microsoft.com/rest/api/redis/

使用 Azure AD 瞭解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure 活動目錄 （AD） 多重要素驗證 （MFA），並遵循 Azure 安全中心標識和訪問管理建議。

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用特權訪問工作站 （PAW） 與多因素身份驗證 （MFA） 配置為登錄和配置 Azure 資源。

瞭解特權訪問工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：當環境中發生可疑或不安全活動時，請使用 Azure 活動目錄 （AD） 特權標識管理 （PIM） 生成日誌和警報。

此外，使用 Azure AD 風險檢測來查看有關風險使用者行為的警報和報告。

如何部署特權標識管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

瞭解 Azure AD 風險檢測：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：在 Azure 活動目錄 （AD） 條件訪問中配置命名位置，以僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （AD） 作為中央身份驗證和授權系統。 Azure AD 對靜態和傳輸中的資料使用增強式加密來保護資料。 Azure AD 還會對使用者憑據進行鹽漬、雜湊和安全地存儲。

Azure AD 身份驗證不能用於直接存取 Redis 資料平面的 Azure 緩存，但是，Azure AD 憑據可用於在控制項平面級別（即 Azure 門戶）進行管理，以控制 Redis 訪問金鑰的 Azure 緩存。


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指南**： Azure 活動目錄 （AD） 提供日誌，以説明您發現陳舊的帳戶。 此外，使用 Azure 標識訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。 可以定期查看使用者存取權限，以確保只有正確的使用者才能繼續訪問。 

瞭解 Azure AD 報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 標識訪問審核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：您可以訪問 Azure 活動目錄 （AD） 登錄活動、審核和風險事件日誌源，這允許您與 Azure Sentinel 或協力廠商 SIEM 集成。

您可以通過為 Azure AD 使用者帳戶創建診斷設置並將稽核記錄和登錄日誌發送到日誌分析工作區來簡化此過程。 您可以在日誌分析中配置所需的日誌警報。

如何將 Azure 活動日誌集成到 Azure 監視器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**：對於控制平面上的帳戶登錄行為偏差，請使用 Azure 活動目錄 （AD） 標識保護和風險檢測功能配置自動回應，以識別與使用者身份相關的可疑操作。 您還可以將資料引入 Azure 哨兵以進行進一步調查。

如何查看 Azure AD 風險登錄：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和啟用身份保護風險策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指導**：尚不可用;Redis 的 Azure 緩存尚不支援客戶密碼箱。

客戶密碼箱支援的服務清單：

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心監視**：當前不可用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用標記來説明跟蹤存儲或處理敏感資訊的 Azure 資源。

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的訂閱和/或管理組。 Redis 實例的 Azure 緩存應由虛擬網路/子網分隔並正確標記。 或者，使用 Redis 防火牆的 Azure 緩存來定義規則，以便只有來自指定 IP 位址範圍的用戶端連接才能連接到緩存。

如何創建其他 Azure 訂閱：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：

https://docs.microsoft.com/azure/governance/management-groups/create

如何將 Redis 的 Azure 緩存部署到 Vnet：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

如何為 Redis 防火牆規則配置 Azure 緩存：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指導**：尚不可用;資料標識、分類和損失預防功能尚未可用於 Redis 的 Azure 緩存。

Microsoft 管理 Redis Azure 緩存的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**：預設情況下，Redis 的 Azure 緩存需要 TLS 加密通信。 當前支援 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 1.1 正在全行業範圍內實現棄用，因此，如果可能，請使用 TLS 1.2。 如果用戶端庫或工具不支援 TLS，則可以通過 Azure 門戶或管理 API 啟用未加密的連接。 在無法進行加密連接的情況下，建議將緩存和用戶端應用程式放入虛擬網路。

瞭解 Redis 的 Azure 緩存在傳輸中的加密：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

瞭解 Vnet 緩存方案中使用的必要端口：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：資料識別、分類和損失預防功能尚未可用於 Redis 的 Azure 緩存。 標記包含敏感資訊的實例，並根據需要實施協力廠商解決方案，以便實現合規性目的。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指導**：使用 Azure 活動目錄 （AAD） 基於角色的存取控制 （RBAC） 來控制對 Redis 控制項平面（即 Azure 門戶）的 Azure 緩存的訪問。 

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指導**：不適用;此建議用於計算資源。

Microsoft 管理 Redis Azure 緩存的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**：Redis 的 Azure 緩存將客戶資料存儲在記憶體中，雖然受到 Microsoft 實施的許多控制項的強力保護，但預設情況下不會加密記憶體。 如果組織需要，請在存儲在 Azure 緩存中以供 Redis 之前對內容進行加密。

如果使用 Redis 的 Azure 緩存功能"Redis 資料持久性"，則資料將發送到您擁有和管理的 Azure 存儲帳戶。 您可以在創建緩存期間配置"Redis 的新 Azure 緩存"邊欄選項卡中的持久性，也可以在現有高級緩存的資源功能表上配置持久性。

Azure 存儲中的資料使用 256 位 AES 加密進行加密和透明解密，這是可用的最強的塊密碼之一，並且符合 FIPS 140-2 標準。 無法禁用 Azure 存儲加密。 您可以依賴 Microsoft 管理的金鑰來加密存儲帳戶，也可以使用自己的金鑰管理加密。

如何為 Redis 在 Azure 緩存中配置持久性：https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

瞭解 Azure 存儲帳戶的加密：https://docs.microsoft.com/azure/storage/common/storage-service-encryption

瞭解 Azure 客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指南**：將 Azure 監視器與 Azure 活動日誌一起用於創建警報，用於何時對 Redis 和其他關鍵或相關資源的 Azure 緩存的生產實例進行更改。

如何為 Azure 活動日誌事件創建警報：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指南**：按照 Azure 安全中心的建議，為 Redis 實例和相關資源保護 Azure 緩存。

Microsoft 在支援 Redis Azure 緩存的基礎系統上執行漏洞管理。

瞭解 Azure 安全中心建議：https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化作業系統修補程式管理解決方案

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比較背對背漏洞掃描

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**：Microsoft 對支援 Redis 的 Azure 緩存的基礎系統執行漏洞管理。

**Azure 安全中心監視**：不適用

**責任**： 微軟

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 資源圖查詢/發現訂閱中的所有資源（如計算、存儲、網路、埠和協定等）。  請確保租戶中的適當（讀取）許可權，並枚舉訂閱中的所有 Azure 訂閱以及資源。

儘管可以通過資源圖發現經典 Azure 資源，但強烈建議今後創建和使用 Azure 資源管理器資源。

如何使用 Azure 資源圖創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 訂閱：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：將標記應用於 Azure 資源，使中繼資料以邏輯方式將它們組織到分類中。

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：在適當情況下，使用標記、管理組和單獨的訂閱來組織和跟蹤 Redis 實例和相關資源的 Azure 緩存。 定期協調庫存，確保及時從訂閱中刪除未經授權的資源。

此外，使用 Azure 策略對客戶訂閱中可以使用以下內置策略定義創建的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已批准的 Azure 資源和軟體標題的清單

**指導**：不適用;此建議適用于計算資源和整個 Azure。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 監視未經批准的 Azure 資源

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

不允許的資源類型

允許的資源類型

此外，使用 Azure 資源圖查詢/發現訂閱中的資源。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形創建查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 監控計算資源中未經批准的軟體應用程式

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 刪除未經批准的 Azure 資源和軟體應用程式

**指導**：不適用;此建議適用于計算資源和整個 Azure。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8： 僅使用已批准的應用程式

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9： 僅使用已批准的 Azure 服務

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

不允許的資源類型

允許的資源類型

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒絕特定資源類型：

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="610-implement-approved-application-list"></a>6.10： 實施已批准的申請清單

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者通過腳本與 Azure 資源管理器交互的能力

**指南**：配置 Azure 條件訪問，通過為"Microsoft Azure 管理"應用配置"阻止訪問"來限制使用者與 Azure 資源管理器 （ARM） 交互的能力。

如何配置條件訪問以阻止對 ARM 的訪問：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制使用者在計算資源中執行腳本的能力

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或邏輯上隔離高風險應用程式

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊，請參閱[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全配置

**指南**：使用 Azure 策略為 Redis 實例定義和實現 Azure 緩存的標準安全配置。 在"Microsoft.Cache"命名空間中使用 Azure 策略別名創建自訂策略，以審核或強制執行 Redis 實例的 Azure 緩存配置。 您還可以使用與 Redis 實例的 Azure 緩存相關的內置策略定義，例如：

應該只允許對 Redis Cache 的安全連線

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

**指南**：如果為 Redis 實例和相關資源的 Azure 緩存使用自訂 Azure 策略定義或 Azure 資源管理器範本，請使用 Azure 存儲庫安全地存儲和管理代碼。

如何在 Azure DevOps 中存儲代碼：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存儲庫文檔：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指南**：使用"Microsoft.Cache"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：使用"Microsoft.Cache"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 使用 Azure 策略 [審核]、[拒絕]和 [部署（如果不存在）]自動為 Redis 實例和相關資源強制實施 Azure 緩存的配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 Redis 實例的 Azure 緩存，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護 Azure 緩存，以便 Redis 金鑰管理。 確保啟用金鑰保存庫虛刪除。

如何與 Azure 託管標識集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 Redis 實例的 Azure 緩存，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護 Azure 緩存，以便 Redis 金鑰管理。 確保啟用金鑰保存庫虛刪除。

使用託管標識在 Azure 活動目錄中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 AAD 身份驗證的任何服務（包括 Azure 金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。

如何配置託管標識：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 託管標識集成：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**指導**：不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機（例如 Azure 應用服務）上啟用 Microsoft 反惡意軟體，但不在客戶內容上運行。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上載到非計算 Azure 資源的預掃描檔

**指南**：在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，Redis 的 Azure 緩存），但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源的任何內容，如應用服務、資料湖存儲、Blob 存儲、用於 PostgreSQL 的 Azure 資料庫等。在這種情況下，Microsoft 無法訪問您的資料。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指導**：不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，Redis 的 Azure 緩存），但它不在客戶內容上運行。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指導**：啟用 Redis 持久性。 Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

您還可以使用 Azure 緩存進行雷瑞斯匯出。 匯出可讓您將儲存在 Azure Cache for Redis 中的資料匯出至與 Redis 相容的 RDB 檔案。 您可以使用這項功能，將資料從一個 Azure Cache for Redis 執行個體移到另一個執行個體，或移到另一個 Redis 伺服器。 在匯出過程中，在承載 Redis 伺服器實例的 Azure 緩存的虛擬機器上創建一個暫存檔案，並將該檔上載到指定的存儲帳戶。 當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

如何啟用 Redis 持久性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure 緩存進行雷定匯出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指導**：啟用 Redis 持久性。 Redis 永續性可讓您保存儲存在 Redis 中的資料。 您也可以擷取快照和備份資料，以在硬體失敗時載入。 這是優於基本或標準層的重大優勢，基本或標準層的所有資料是儲存在記憶體中，若發生快取節點故障的失敗，資料可能會遺失。

您還可以使用 Azure 緩存進行雷瑞斯匯出。 匯出可讓您將儲存在 Azure Cache for Redis 中的資料匯出至與 Redis 相容的 RDB 檔案。 您可以使用這項功能，將資料從一個 Azure Cache for Redis 執行個體移到另一個執行個體，或移到另一個 Redis 伺服器。 在匯出過程中，在承載 Redis 伺服器實例的 Azure 緩存的虛擬機器上創建一個暫存檔案，並將該檔上載到指定的存儲帳戶。 當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

如果使用 Azure 金鑰保存庫為 Redis 實例存儲 Azure 緩存的憑據，請確保定期自動備份金鑰。

如何啟用 Redis 持久性：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

如何使用 Azure 緩存進行雷定匯出：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**： 使用 Azure 緩存進行雷定導入。 導入可用於從在任何雲或環境中運行的任何 Redis 伺服器（包括在 Linux、Windows 或任何雲供應商（如 Amazon Web 服務和其他）上運行的 Redis 伺服器中引入與 Redis 相容的 RDB 檔。 匯入資料是使用預先填入資料建立快取的輕鬆方式。 在進行匯入程序的期間，Azure Cache for Redis 會從 Azure 儲存體將 RDB 檔案載入記憶體，然後將金鑰插入快取中。

定期測試 Azure 金鑰保存庫機密的資料還原。

如何使用 Azure 緩存進行雷瑞斯導入：

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

如何修復金鑰保存庫機密：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**： 來自 Redis 匯出和 Redis 持久性的 Redis 備份的 Azure 緩存存儲在所選的 Azure 存儲帳戶中。 Azure 存儲中的資料使用 256 位 AES 加密進行加密和透明解密，這是可用的最強的塊密碼之一，並且符合 FIPS 140-2 標準。 無法禁用 Azure 存儲加密。 您可以依賴 Microsoft 管理的金鑰來加密存儲帳戶，也可以使用自己的金鑰管理加密。

瞭解 Azure 存儲帳戶的加密：https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure 安全中心監視**：是

**責任**： 微軟

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：為您的組織構建事件回應指南。 確保有書面事件回應計畫，定義人員的所有角色以及事件處理/管理階段，從檢測到事件後審查。

如何在 Azure 安全中心內配置工作流自動化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有關構建您自己的安全事件回應流程的指導：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微軟安全回應中心的事件剖析：

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客戶還可以利用 NIST 的電腦安全性事件處理指南來説明創建他們自己的事件回應計畫：

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：安全中心為每個警報分配嚴重性，以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析，以及導致警報的活動背後存在惡意的置信度。

此外，清楚地標記訂閱（對於前 生產，非 prod），並創建命名系統以明確識別和分類 Azure 資源。

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="103-test-security-response-procedures"></a>10.3： 測試安全回應程式

**指導**：進行練習，以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距，並根據需要修訂計畫。

請參閱 NIST 出版物：IT 計畫和功能測試、培訓和鍛煉計劃指南：

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件連絡人詳細資訊，並配置安全事件的警報通知

**指南**：如果 Microsoft 安全回應中心 （MSRC） 發現客戶的資料已被非法或未經授權的方訪問，Microsoft 將使用安全事件聯繫資訊與您聯繫。  事後查看事件，以確保問題得到解決。

如何設置 Azure 安全中心安全連絡人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 將安全警報納入事件回應系統

**指南**：使用"連續匯出"功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心資料連線器來資料流警報哨兵。

如何配置連續匯出：

https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報資料流到 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自動回應安全警報

**指南**：使用 Azure 安全中心的工作流自動化功能，通過安全警報和建議上的"邏輯應用"自動觸發回應。

如何配置工作流自動化和邏輯應用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊，請參閱[安全控制：滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：對 Azure 資源進行定期滲透測試，並確保在 60 天內補救所有關鍵安全發現

**指南**： 遵循 Microsoft 參與規則，確保您的滲透測試不違反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和執行的詳細資訊，如下所示： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**：不適用

**責任**： 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
