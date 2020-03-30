---
title: 事件中心的 Azure 安全基線
description: 事件中心的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f16f6ef38b221e525340858fc7ba131f8a1bdf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289572"
---
# <a name="azure-security-baseline-for-event-hubs"></a>事件中心的 Azure 安全基線

事件中心 Azure 安全基線包含可説明您改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：將事件中心與虛擬網路服務終結點集成，可以安全地訪問來自工作負載（如綁定到虛擬網路的虛擬機器）的消息傳遞功能，並且網路流量路徑在兩端都受到保護。

綁定到至少一個虛擬網路子網服務終結點後，相應的事件中心命名空間不再接受來自任何位置的流量，而是接受虛擬網路中的授權子網。 從虛擬網路的角度來看，將事件中心命名空間綁定到服務終結點會配置從虛擬網路子網到消息服務的隔離網路隧道。 

您還可以創建專用終結點，這是一個網路介面，通過使用 Azure 專用連結服務將您私下安全地連接到 Azure 事件中心服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 

您還可以使用防火牆保護 Azure 事件中心命名空間。 Azure 事件中心支援基於 IP 的存取控制，用於入站防火牆支援。 可以使用 Azure 門戶、Azure 資源管理器範本或通過 Azure CLI 或 Azure PowerShell 設置防火牆規則。

如何使用 Azure 事件中心中的虛擬網路服務終結點：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

有關詳細資訊，請參閱將 Azure 事件中心與 Azure 專用https://docs.microsoft.com/azure/event-hubs/private-link-service連結集成： 。

在事件中心命名空間上啟用虛擬網路集成和防火牆：https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

如何為 Azure 事件中心命名空間配置 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：使用 Azure 安全中心並遵循網路保護建議來説明保護 Azure 中的事件中心資源。 如果使用 Azure 虛擬機器訪問事件中心，請啟用網路安全性群組 （NSG） 流日誌並將日誌發送到存儲帳戶以進行流量審核。

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 安全中心提供的網路安全：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：在與事件中心關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻斷服務 （DDoS） 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通信。

如何配置 DDoS 保護：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

有關 Azure 安全中心集成威脅情報的詳細資訊：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：如果使用 Azure 虛擬機器訪問事件中心，請啟用網路安全性群組 （NSG） 流日誌並將日誌發送到存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如果需要調查異常活動，則啟用網路觀察程式資料包捕獲。

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics

如何啟用網路觀察程式：https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指南**：如果使用 Azure 虛擬機器訪問事件中心，請從 Azure 應用商店中選擇支援具有有效負載檢查功能的 IDS/IPS 功能的產品/服務。 如果組織不需要基於有效負載檢查的入侵偵測和/或預防，則可以使用 Azure 事件中心的內置防火牆功能。 您可以使用防火牆規則限制對有限範圍 IP 位址或特定 IP 位址的活動中心命名空間的訪問。

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

如何在事件中心為指定的 IP 位址添加防火牆規則：

 https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls#add-firewall-for-specified-ip

**Azure 安全中心監視**：尚未可用

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：不適用，此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：使用 Azure 策略為與 Azure 事件中心命名空間關聯的網路資源定義和實施標準安全配置。 在"Microsoft.EventHub"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行事件中心命名空間的網路設定。 您還可以使用與 Azure 事件中心相關的內置策略定義，例如：

- 事件中心應使用虛擬網路服務終結點。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

事件中心命名空間的 Azure 內置策略：https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



用於網路的 Azure 策略示例：https://docs.microsoft.com/azure/governance/policy/samples/#network



如何創建 Azure 藍圖：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：對虛擬網路以及與事件中心關聯的網路安全和流量流相關的其他資源使用標記。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指導**：使用 Azure 活動日誌監視網路資源配置並檢測與 Azure 事件中心相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。

如何查看和檢索 Azure 活動日誌事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指導**：不適用;Microsoft 維護用於 Azure 資源（如 Azure 事件中心）的時間源，用於日誌中的時間戳記。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指南**：在 Azure 監視器中，在活動日誌和事件中心診斷設置中配置與事件中心相關的日誌，以將日誌發送到日誌分析工作區以進行查詢或發送到存儲帳戶以進行長期存檔存儲。

如何配置 Azure 事件中心的診斷設置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

瞭解 Azure 活動日誌：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指南**：為 Azure 事件中心命名空間啟用診斷設置。 Azure 事件中心有三類診斷設置：存檔日誌、動作記錄和自動縮放日誌。 啟用動作記錄來捕獲有關事件中心操作期間發生的情況的資訊，特別是操作類型，包括事件中心創建、使用的資源和操作的狀態。

此外，您可以啟用 Azure 活動日誌診斷設置並將其發送到 Azure 存儲帳戶、事件中心或日誌分析工作區。 活動日誌提供對 Azure 事件中心和其他資源執行的操作的見解。 使用活動日誌，可以確定 Azure 事件中心命名空間上採用的任何寫入操作（PUT、POST、DELETE）的"什麼、誰和時間"。

如何為 Azure 事件中心啟用診斷設置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何為 Azure 活動日誌啟用診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：在 Azure 監視器中，根據組織的合規性法規設置日誌分析工作區保留期，以捕獲和查看事件中心相關事件。

如何為日誌分析工作區設置日誌保留參數：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指導**：分析和監視日誌是否存在異常行為，並定期查看與事件中心相關的結果。 使用 Azure 監視器的日誌分析查看日誌並執行日誌資料的查詢。 或者，您可以將資料啟用並車載到 Azure Sentinel 或協力廠商 SIEM。
 

有關日誌分析工作區的詳細資訊：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**：在 Azure 監視器中，在活動日誌中配置與 Azure 事件中心相關的日誌，以及事件中心診斷設置，以將日誌發送到日誌分析工作區以進行查詢或發送到存儲帳戶以進行長期存檔存儲。 使用日誌分析工作區為安全性記錄檔和事件中的異常活動創建警報。

或者，您可以將資料啟用和車載到 Azure Sentinel。 

瞭解 Azure 活動日誌：https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

如何配置 Azure 事件中心的診斷設置：https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何對日誌分析工作區日誌資料發出警報：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：尚未可用

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指導**：不適用;事件中心不處理反惡意軟體日誌記錄。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指導**：不適用;事件中心不處理或生成 DNS 相關日誌。

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

**指導**：通過 Azure 活動目錄 （AD） 控制對事件中心的控制平面訪問。 Azure AD 沒有預設密碼的概念。

通過 Azure AD 控制對事件中心的資料平面訪問，具有託管標識或應用註冊以及共用訪問簽名。 連接到事件中心的用戶端使用共用訪問簽名，並可以隨時重新生成。

瞭解事件中心的共用訪問簽名：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心監視**：不適用

**責任**： 客戶

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

**指南**：Microsoft Azure 基於 Azure 活動目錄 （AD） 為資源和應用程式提供集成的存取控制管理。 將 Azure AD 與 Azure 事件中心一起使用的一個關鍵優點是，您不再需要在代碼中存儲憑據。 相反，您可以從 Microsoft 標識平臺請求 OAuth 2.0 訪問權杖。 請求權杖的資源名稱為https://eventhubs.azure.net/。 Azure AD 對運行應用程式的安全主體（使用者、組或服務主體）進行身份驗證。 如果身份驗證成功，Azure AD 會向應用程式返回訪問權杖，然後應用程式可以使用訪問權杖授權對 Azure 事件中心資源的請求。

如何使用 Azure AD 對應用程式進行身份驗證以訪問事件中心資源：https://docs.microsoft.com/azure/event-hubs/authenticate-application

使用 Azure AD 瞭解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure 活動目錄多重要素驗證 （MFA），並遵循 Azure 安全中心標識和訪問管理建議，以説明保護啟用事件集線器的資源。

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用特權訪問工作站 （PAW） 配置為登錄到和配置啟用事件集線器的資源的多重要素驗證 （MFA）。

瞭解特權訪問工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：當環境中發生可疑或不安全活動時，請使用 Azure 活動目錄 （AD） 特權標識管理 （PIM） 生成日誌和警報。 使用 Azure AD 風險檢測查看有關風險使用者行為的警報和報告。 對於其他日誌記錄，請將 Azure 安全中心風險檢測警報發送到 Azure 監視器，並使用操作組配置自訂警報/通知。



如何部署特權標識管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan


瞭解 Azure AD 風險檢測：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

如何為自訂警報和通知配置操作組：https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：使用條件訪問命名位置僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。



如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （AD） 作為 Azure 資源（如事件中心）的中央身份驗證和授權系統。 這允許基於角色的存取控制 （RBAC） 管理敏感資源。

 如何創建和配置 Azure AD 實例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

要瞭解 Azure 事件中心如何與 Azure 活動目錄 （AAD） 集成，請參閱使用 Azure 活動目錄授權訪問事件中心資源：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指南**： Azure 活動目錄 （AD） 提供日誌，以説明您發現陳舊的帳戶。 此外，使用 Azure 標識訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。 可以定期查看使用者存取權限，以確保只有正確的使用者才能繼續訪問。

此外，定期輪換事件中心共用訪問簽名。

瞭解 Azure AD 報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 標識訪問審核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

瞭解事件中心的共用訪問簽名：https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：您可以訪問 Azure 活動目錄 （AD） 登錄活動、審核和風險事件日誌源，這允許您與任何 SIEM/監視工具集成。

您可以通過為 Azure AD 使用者帳戶創建診斷設置並將稽核記錄和登錄日誌發送到日誌分析工作區來簡化此過程。 您可以在日誌分析中配置所需的日誌警報。

如何將 Azure 活動日誌集成到 Azure 監視器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

使用 Azure 活動目錄授權訪問事件中心資源：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**：使用 Azure 活動目錄的標識保護和風險檢測功能，將自動回應配置為檢測到的與啟用事件中心的資源相關的可疑操作。 應通過 Azure Sentinel 啟用自動回應，以實現組織的安全回應。

如何查看 Azure AD 風險登錄：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和啟用身份保護風險策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指南**：當前不可用;事件中心尚不支援客戶密碼箱。

客戶密碼箱支援的服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心監視**：當前不可用

**責任**： 當前不可用

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用與事件中心相關的資源上的標記來説明跟蹤存儲或處理敏感資訊的 Azure 資源。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的訂閱和/或管理組。 事件中心命名空間應由虛擬網路分隔，並啟用服務終結點並正確標記。

您還可以使用防火牆保護 Azure 事件中心命名空間。 Azure 事件中心支援基於 IP 的存取控制，用於入站防火牆支援。 可以使用 Azure 門戶、Azure 資源管理器範本或通過 Azure CLI 或 Azure PowerShell 設置防火牆規則。

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

為 Azure 事件中心命名空間配置 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何創建虛擬網路：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指南**：使用虛擬機器訪問事件中心時，請使用虛擬網路、服務終結點、事件中心防火牆、網路安全性群組和服務標記來降低資料洩露的可能性。

Microsoft 管理 Azure 事件中心的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

為 Azure 事件中心命名空間配置 IP 防火牆規則：https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

使用 Azure 事件中心瞭解虛擬網路服務終結點：https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

將 Azure 事件中心與 Azure 專用連結集成：https://docs.microsoft.com/azure/event-hubs/private-link-service

瞭解網路安全性群組和服務標記：https://docs.microsoft.com/azure/virtual-network/security-overview

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**：預設情況下，Azure 事件中心強制實施 TLS 加密通信。 當前支援 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 1.1 正在全行業範圍內實現棄用，因此，如果可能，請使用 TLS 1.2。

要瞭解事件中心的安全功能，請參閱網路安全：https://docs.microsoft.com/azure/event-hubs/network-security

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：Azure 事件中心尚不可用資料標識、分類和損失預防功能。 如果需要符合性，則實施協力廠商解決方案。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指南**： Azure 事件中心支援使用 Azure 活動目錄 （AD） 授權對事件中心資源的請求。 使用 Azure AD，可以使用基於角色的存取控制 （RBAC） 向安全主體（可能是使用者）或應用程式服務主體授予許可權。

瞭解 Azure AD RBAC 和 Azure 事件中心可用的角色：https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指導**：不適用;本指南用於計算資源。

Microsoft 管理事件中心的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**：Azure 事件中心支援使用 Microsoft 管理的金鑰或客戶管理的金鑰加密靜態資料的選項。 此功能使您能夠創建、旋轉、禁用和撤銷對用於加密靜態 Azure 事件中心資料的客戶託管金鑰的存取權限。

如何配置客戶管理的金鑰以加密 Azure 事件中心：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指南**：將 Azure 監視器與 Azure 活動日誌一起用於創建對 Azure 事件中心和其他關鍵或相關資源的生產實例進行更改時的警報。

如何為 Azure 活動日誌事件創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指導**：不適用;Microsoft 在支援事件中心的基礎系統上執行漏洞管理。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化作業系統修補程式管理解決方案

**指導**：不適用;Microsoft 在支援事件中心的基礎系統上執行修補程式管理。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**：不適用;基準測試適用于計算資源。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比較背對背漏洞掃描

**指導**：不適用;Microsoft 在支援事件中心的基礎系統上執行漏洞管理。

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用風險評級流程確定修復已發現漏洞的優先順序

**指導**：不適用;Microsoft 在支援事件中心的基礎系統上執行漏洞管理。

**Azure 安全中心監視**：不適用

**責任**： 微軟

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 資源圖查詢和發現訂閱中的所有資源（包括 Azure 事件中心命名空間）。 確保租戶中具有適當的（讀取）許可權，並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 訂閱：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：將標記應用於 Azure 資源，使中繼資料以邏輯方式將它們組織到分類中。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：在適當情況下，使用標記、管理組和單獨的訂閱來組織和跟蹤 Azure 事件中心命名空間和相關資源。 定期協調庫存，確保及時從訂閱中刪除未經授權的資源。

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

- 不允許的資源類型

- 允許的資源類型

此外，使用 Azure 資源圖查詢/發現訂閱中的資源。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

- 不允許的資源類型

- 允許的資源類型

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒絕特定資源類型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="610-implement-approved-application-list"></a>6.10： 實施已批准的申請清單

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>限制使用者通過腳本與 Azure 資源管理器交互的能力</div>

**指南**：配置 Azure 條件訪問，通過為"Microsoft Azure 管理"應用配置"阻止訪問"來限制使用者與 Azure 資源管理器交互的能力。

如何配置條件訪問以阻止對 Azure 資源管理器的訪問：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**指南**：為 Azure 事件中心部署定義和實施標準安全配置。 在"Microsoft.EventHub"命名空間中使用 Azure 策略別名創建自訂策略以審核或強制執行配置。 您還可以使用 Azure 事件中心的內置策略定義，例如：

- 應啟用事件中樞內的診斷記錄

- 事件中樞應該使用虛擬網路服務端點

事件中心命名空間的 Azure 內置策略：https://docs.microsoft.com/en-\我們/azure/治理/策略/示例/內置策略_事件中心

如何查看可用的 Azure 策略別名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

**指導**：使用 Azure 策略 [拒絕] 和 [部署（如果不存在））在啟用事件中心的資源中強制實施安全設置。 

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
有關 Azure 策略效果的詳細資訊：https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

**指南**：如果對事件中心或相關資源使用自訂 Azure 策略定義，請使用 Azure 存儲庫安全地存儲和管理代碼。

如何在 Azure DevOps 中存儲代碼：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存儲庫文檔：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指南**：使用"Microsoft.EventHub"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：使用"Microsoft.EventHub"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 使用 Azure 策略 [審核]、[拒絕]和 [部署（如果不存在））自動強制 Azure 事件中心部署和相關資源的配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問事件中心，請使用 Azure 金鑰保存庫的託管服務標識，以簡化和保護 Azure 事件中心部署的共用訪問簽名管理。 確保啟用金鑰保存庫虛刪除。

使用 Azure 活動目錄對託管標識進行身份驗證以訪問事件中心資源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

為事件中心配置客戶管理的金鑰：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問事件中心，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護 Azure 事件中心。 確保啟用金鑰保存庫虛刪除。

使用託管標識在 Azure 活動目錄 （AD） 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務（包括 Azure 金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。

使用 Azure 活動目錄對託管標識進行身份驗證以訪問事件中心資源：https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

為事件中心配置客戶管理的金鑰：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

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

**指導**：不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機（例如 Azure 應用服務）上啟用 Microsoft 反惡意軟體，但不在客戶內容上運行。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上載到非計算 Azure 資源的預掃描檔

**指南**： 預掃描上載到非計算 Azure 資源的任何內容，如 Azure 事件中心、應用服務、資料存儲、Blob 存儲、用於 PostgreSQL 的 Azure 資料庫等。在這種情況下，Microsoft 無法訪問您的資料。

在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，Redis 的 Azure 緩存），但它不在客戶內容上運行。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指南**：為 Azure 事件中心配置地理災害復原。 當整個 Azure 區域或資料中心 (如果未使用可用性區域) 遇到停機時，最重要的是資料處理作業能夠繼續在不同的區域或資料中心運作。 因此，地質災害恢復和異地複製是任何企業的重要功能。 Azure 事件中樞支援命名空間層級的地理災害復原和異地複寫。 

瞭解 Azure 事件中心的地理災害復原：https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones-preview

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指南**：Azure 事件中心使用 Azure 存儲服務加密 （Azure SSE） 提供靜態資料加密。 事件中心依賴于 Azure 存儲來存儲資料，預設情況下，與 Azure 存儲一起存儲的所有資料都使用 Microsoft 管理的金鑰進行加密。 如果使用 Azure 金鑰保存庫存儲客戶管理的金鑰，請確保定期自動備份金鑰。

使用以下 PowerShell 命令確保金鑰保存庫機密的定期自動備份：備份-AzKeyVault秘密

如何配置客戶管理的金鑰以加密靜態 Azure 事件中心資料：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

如何備份金鑰保存庫機密：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**：測試備份的客戶託管金鑰的恢復。

 

如何在 Azure 中還原金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**：在金鑰保存庫中啟用虛刪除，以保護金鑰免受意外或惡意刪除。 Azure 事件中心要求客戶管理的金鑰必須配置"虛刪除"和"不清除"。

為用於捕獲事件中心資料的 Azure 存儲帳戶配置虛刪除。 請注意，Azure 資料湖存儲第 2 代還不支援此功能。

如何在金鑰保存庫中啟用虛刪除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

使用金鑰設置金鑰保存庫：https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure 存儲 blob 的虛刪除：https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心監視**：是

**責任**： 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：確保有書面的事件回應計畫，定義人員的角色以及事件處理/管理的各個階段。

如何在 Azure 安全中心內配置工作流自動化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：安全中心為警報分配嚴重性，以説明您確定處理每個警報的順序，以便在資源受到威脅時，您可以馬上到達它。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析，以及導致警報的活動背後存在惡意的置信度。

**Azure 安全中心監視**：是

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

**指南**：請遵循 Microsoft 的接用規則，以確保您的滲透測試不違反 Microsoft 策略https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1： .
您可以在此處找到有關 Microsoft 針對 Microsoft 託管雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和實施的詳細資訊：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**：是

**責任**： 客戶

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
