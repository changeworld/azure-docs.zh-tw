---
title: MariaDB Azure 資料庫的 Azure 安全基線
description: MariaDB Azure 資料庫的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d9cff47c61fc08305427b25f949884b1ef089fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289663"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>MariaDB Azure 資料庫的 Azure 安全基線

MariaDB Azure 資料庫的 Azure 安全基線包含一些建議，可説明您改進部署的安全狀態。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：使用專用終結點為 MariaDB 配置 Azure 資料庫的專用連結。 Private Link 可讓您透過私人端點連線到 Azure 中的各種 PaaS 服務。 Azure 專用連結實質上將 Azure 服務引入專用虛擬網路 （VNet）。 虛擬網路和 MariaDB 實例之間的流量將傳輸 Microsoft 骨幹網路。

或者，您可以使用虛擬網路服務終結點來保護和限制對 Azure 資料庫的網路訪問，以便實現 MariaDB 實現。 虛擬網路規則是一個防火牆安全功能，用於控制 MariaDB 的 Azure 資料庫是否接受從虛擬網路中的特定子網發送的通信。

還可以使用防火牆規則保護 MariaDB 的 Azure 資料庫。 在指定哪些電腦具有許可權之前，伺服器防火牆會阻止對資料庫伺服器的所有訪問。 若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

如何為 MariaDB 配置 Azure 資料庫的專用連結：https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

如何在 MariaDB 伺服器的 Azure 資料庫中創建和管理 VNet 服務終結點和 VNet 規則：https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

如何為 MariaDB 防火牆規則配置 Azure 資料庫：https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：當 MariaDB 伺服器的 Azure 資料庫安全到專用終結點時，可以在同一虛擬網路中部署虛擬機器。 您可以使用網路安全性群組 （NSG） 來降低資料洩露的風險。 啟用 NSG 流日誌並將日誌發送到存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何為 MariaDB 配置 Azure 資料庫的專用連結：https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

如何啟用 NSG 流日誌https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal：如何啟用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：對 MariaDB 的 Azure 資料庫使用高級威脅保護。 高級威脅防護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

在與 MariaDB 實例的 Azure 資料庫關聯的虛擬網路上啟用 DDoS 保護標準，以防止 DDoS 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通信。

如何為 MariaDB 配置 Azure 資料庫的高級威脅保護：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置 DDoS 保護：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：當 MariaDB 伺服器的 Azure 資料庫安全到專用終結點時，可以在同一虛擬網路中部署虛擬機器。 然後，您可以配置網路安全性群組 （NSG）以降低資料洩露的風險。 啟用 NSG 流日誌並將日誌發送到存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到日誌分析工作區，並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 流日誌https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal：如何啟用和使用流量分析：https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指南**：對 MariaDB 的 Azure 資料庫使用高級威脅保護。 高級威脅防護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。
如何為 MariaDB 配置 Azure 資料庫的高級威脅保護：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指導**：不適用;此建議適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：對於需要訪問 MariaDB 實例的 Azure 資料庫的資源，請使用虛擬網路服務標記在網路安全性群組或 Azure 防火牆上定義網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過指定服務標記名稱（例如 SQL。WestUs） 在規則的相應源或目標欄位中，您可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。
注意：MariaDB 的 Azure 資料庫使用"Microsoft.Sql"服務標記。

有關使用服務標記的詳細資訊：https://docs.microsoft.com/azure/virtual-network/service-tags-overview瞭解 MariaDB Azure 資料庫的服務標記使用方式：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：使用 Azure 策略為 MariaDB 實例定義和實施與 Azure 資料庫關聯的網路設置和網路資源的標準安全配置。 在"Microsoft.DBforMariaDB"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略，以審核或強制執行 MariaDB 實例的 Azure 資料庫的網路設定。 您還可以使用與網路相關的內置策略定義或 MariaDB 實例的 Azure 資料庫，例如：

- 應啟用 DDoS 保護標準

- 應為 MariaDB 伺服器啟用專用終結點

- MariaDB 伺服器應使用虛擬網路服務終結點

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

用於網路的 Azure 策略示例：https://docs.microsoft.com/azure/governance/policy/samples/#network

如何創建 Azure 藍圖：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：對 MariaDB 實例的網路安全和流量流相關的資源使用標記來提供中繼資料和邏輯組織。

使用與標記相關的任何內置 Azure 策略定義（如"要求標記及其值"）確保所有資源都使用標記創建，並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指南**：使用 Azure 活動日誌監視網路資源配置，並檢測與 MariaDB 實例的 Azure 資料庫相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。
如何查看和檢索 Azure 活動日誌事件https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view：如何在 Azure 監視器中創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指南**：Microsoft 維護用於 Azure 資源的時間源，例如日誌中時間戳記的 MariaDB 的 Azure 資料庫。


**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指南**：啟用診斷設置和伺服器日誌和引入日誌以聚合由 Azure 資料庫生成的 MariaDB 實例的安全資料。 在 Azure 監視器中，使用日誌分析工作區來查詢和執行分析，並使用 Azure 存儲帳戶進行長期/存檔存儲。 或者，您可以將資料啟用到 Azure Sentinel 或協力廠商 SIEM。
如何為 MariaDB 配置和訪問 Azure 資料庫的伺服器日誌：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何為 MariaDB 配置和訪問 Azure 資料庫的https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal稽核記錄：如何將 Azure Sentinel 上載：https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure 安全中心監視**：不可用

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指南**：在 Azure 資料庫上啟用 MariaDB 實例的診斷設置，以訪問審核、安全和診斷日誌。 確保專門啟用 MariaDB 稽核記錄。 活動日誌自動可用，包括事件源、日期、使用者、時間戳記、源位址、目標位址和其他有用元素。 您還可以啟用 Azure 活動日誌診斷設置，並將日誌發送到相同的日誌分析工作區或存儲帳戶。

如何為 MariaDB 配置和訪問 Azure 資料庫的https://docs.microsoft.com/azure/mariadb/concepts-server-logs伺服器日誌：如何為 MariaDB 配置和訪問 Azurehttps://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal資料庫的稽核記錄：如何為 Azure 活動日誌配置診斷設置：https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure 安全中心監視**：不可用

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指導**：不適用;此建議用於計算資源。


**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：在 Azure 監視器中，對於用於保存 MariaDB 日誌的 Azure 資料庫的日誌分析工作區，請根據組織的合規性法規設置保留期。 使用 Azure 存儲帳戶進行長期/存檔存儲。
如何為日誌分析工作區設置日誌保留參數：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period將資源日誌存儲在 Azure 存儲帳戶中：https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指南**：分析和監視來自 MariaDB 實例的日誌是否存在異常行為。 使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌資料的查詢。 或者，您可以將資料啟用並車載到 Azure Sentinel 或協力廠商 SIEM。

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

有關日誌分析工作區的詳細資訊：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**： 為 MariaDB 啟用高級威脅防護。 MariaDB Azure 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

此外，您還可以為 MariaDB 啟用伺服器日誌和診斷設置，並將日誌發送到日誌分析工作區。 將日誌分析工作區添加到 Azure Sentinel 中，因為它提供安全編排自動回應 （SOAR） 解決方案。 這允許創建行動手冊（自動解決方案），並用於修復安全問題。

如何為 MariaDB 啟用高級威脅防護：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置和訪問 MariDB 的伺服器日誌：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何配置和訪問 MariaDB 的稽核記錄：https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指導**：不適用;MariaDB 不處理或生成與惡意軟體相關的日誌。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指導**：不適用;MariaDB 不處理或生成 DNS 相關日誌。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

**指導**：不適用;基準測試適用于計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊，請參閱[安全控制：標識和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

**指南**：維護對 MariaDB 實例的管理平面（Azure 門戶/Azure 資源管理器）具有管理存取權限的使用者帳戶的清單。 此外，請維護有權訪問 MariaDB 實例的資料平面的管理帳戶的清單。 （創建 MariaDB 伺服器時，為管理員使用者提供憑據。 此管理員可用於創建其他 MariaDB 使用者。

瞭解 MariaDB 的訪問管理：https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

瞭解 Azure 訂閱的內置 RBAC 角色：https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

**指南**：Azure 活動目錄沒有預設密碼的概念。

創建 MariaDB 資源本身後，Azure 將強制創建具有強式密碼的管理使用者。 但是，創建 MariaDB 實例後，您可以使用您創建帳戶的第一個伺服器管理員帳戶來創建其他使用者並授予對它們的管理存取權限。 創建這些帳戶時，請確保為每個帳戶配置不同的強式密碼。

如何為 MariaDB 創建其他帳戶：https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

**指南**：圍繞使用有權訪問 MariaDB 實例的專用管理帳戶創建標準操作過程。 使用 Azure 安全中心標識和訪問管理監視管理帳戶數。

瞭解 Azure 安全中心標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

**指南**：對 MariaDB 的資料包平面訪問由資料庫中存儲的標識控制，不支援 SSO。 MariaDB 的控制平面訪問可通過 REST API 獲得，並支援 SSO。 要進行身份驗證，請將請求的授權標頭設置為從 Azure 活動目錄獲取的 JSON Web 權杖。

瞭解 MariaDB REST API 的 Azure 資料庫：https://docs.microsoft.com/rest/api/mariadb/

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure AD MFA 並遵循 Azure 安全中心標識和訪問管理建議。

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用具有 MFA 的 PAW（特權訪問工作站）來登錄和配置 Azure 資源。

瞭解特權訪問工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：為 MariaDB 啟用高級威脅防護，以生成可疑活動的警報。

此外，當環境中發生可疑或不安全活動時，可以使用 Azure AD 特權標識管理 （PIM） 生成日誌和警報。 使用 Azure AD 風險檢測查看有關風險使用者行為的警報和報告。

如何為 MariaDB 設置高級威脅防護：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何部署特權標識管理 （PIM）：https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

瞭解 Azure AD 風險檢測：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：使用條件訪問命名位置允許僅從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問，以限制對 Azure 資源（如 MariaDB）的訪問。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （AAD） 作為中央身份驗證和授權系統。 AAD 對靜態和傳輸中的資料使用增強式加密來保護資料。 AAD 還對使用者憑據進行鹽漬、雜湊和安全地存儲。

Azure AD 身份驗證不能用於直接存取 MariaDB 資料平面，但是，Azure AD 憑據可用於管理平面級別（例如 Azure 門戶）的管理來控制 MariaDB 管理員帳戶。

如何更新 MariaDB 的管理密碼：https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指導**：查看 Azure 活動目錄日誌，以説明發現陳舊的帳戶，其中可能包括具有 MariaDB 管理角色的帳戶。 此外，使用 Azure 標識訪問審核可有效地管理組成員身份、訪問可用於訪問 MariaDB 的企業應用程式以及角色指派。 使用者存取權限應定期審查，例如每 90 天一次，以確保只有正確的使用者才能繼續訪問。

瞭解 Azure 廣告報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 標識訪問審核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：啟用 MariaDB 和 Azure 活動目錄的診斷設置，將所有日誌發送到日誌分析工作區。 在日誌分析工作區中配置所需的警報（如身份驗證嘗試失敗）。

如何配置和訪問 MariaDB 的伺服器日誌：https://docs.microsoft.com/azure/mariadb/concepts-server-logs

如何配置和訪問 MariaDB 的稽核記錄：https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

如何將 Azure 活動日誌集成到 Azure 監視器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**：不可用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**： 為 MariaDB 啟用高級威脅防護。 MariaDB Azure 資料庫的高級威脅保護可檢測異常活動，指示訪問或利用資料庫的異常和潛在有害嘗試。

使用 Azure 活動目錄的標識保護和風險檢測功能配置自動回應以檢測出的可疑操作。 您可以通過 Azure Sentinel 啟用自動回應，以實現組織的安全回應。

如何為 MariaDB 啟用高級威脅防護：https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

如何配置和啟用身份保護風險策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何查看 Azure AD 風險登錄：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何登上 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**：不可用

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指導**：不適用;MariaDB 的 Azure 資料庫尚不支援客戶密碼箱。

客戶密碼箱支援服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用標記説明跟蹤用於存儲或處理敏感資訊的 MariaDB 實例或相關資源的 Azure 資料庫。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的訂閱和/或管理組。 使用專用鏈路、服務終結點和/或 MariaDB 防火牆規則的組合來隔離和限制對 MariaDB 實例的網路訪問。

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何為 MariaDB 配置 Azure 資料庫的專用連結：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

如何為 MariaDB 配置 Azure 資料庫的服務終結點：https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

如何為 MariaDB 配置 Azure 資料庫的防火牆規則：https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure 安全中心監視**：不可用

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指南**：當使用 Azure VM 訪問 MariaDB 實例時，請使用專用鏈路、MariaDB 網路設定、網路安全性群組和服務標記來降低資料洩露的可能性。

Microsoft 管理 MariaDB 的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

如何緩解 MariaDB Azure 資料庫的資料外泄：https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**： MariaDB 的 Azure 資料庫支援使用安全通訊端層 （SSL） 將 MariaDB 伺服器的 Azure 資料庫連接到用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 在 Azure 門戶中，確保所有 MariaDB 實例都啟用了"強制 SSL 連線"。

如何為 MariaDB 配置傳輸中的加密：https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure 安全中心監視**：不可用

**責任**： 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：MariaDB 的 Azure 資料庫尚未提供資料標識、分類和損失預防功能。 如果需要符合性，則實施協力廠商解決方案。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不可用

**責任**： 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指導**：使用 Azure AD RBAC 控制對 MariaDB 管理層（Azure 門戶/Azure 資源管理器）的 Azure 資料庫的訪問。 對於資料平面訪問（在資料庫本身中），使用 SQL 查詢創建使用者並配置使用者許可權。

如何在 Azure 中配置 RBAC：https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

如何使用 MariaDB 的 SQL 配置使用者訪問：https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指導**：不適用;本指南用於計算資源。

Microsoft 管理 MariaDB 的基礎基礎結構，並實施了嚴格的控制，以防止客戶資料丟失或暴露。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**： MariaDB 服務的 Azure 資料庫使用 FIPS 140-2 驗證加密模組對靜態資料進行存儲加密。 資料（包括備份）在磁片上加密，但執行查詢時創建的暫存檔案除外。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。

瞭解 MariaDB 的靜態加密：https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure 安全中心監視**：不適用

**責任**： 微軟

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指南**：將 Azure 監視器與 Azure 活動日誌一起用於創建警報，用於何時對 MariaDB 和其他關鍵或相關資源的 Azure 資料庫的生產實例進行更改。

如何為 Azure 活動日誌事件創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指南**：當前不可用;Azure 安全中心尚不支援 MariaDB 伺服器 Azure 資料庫的漏洞評估。


**Azure 安全中心監視**：是

**責任**： 客戶

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

**指南**：Microsoft 在支援 MariaDB 伺服器 Azure 資料庫的基礎系統上執行漏洞管理。


**Azure 安全中心監視**：不適用

**責任**： 微軟

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 資源圖查詢和發現訂閱中的所有資源（包括 MariaDB 伺服器的 Azure 資料庫）。 確保租戶中具有適當的（讀取）許可權，並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 訂閱：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：將標記應用於 MariaDB 伺服器和其他相關資源的 Azure 資料庫，從而提供中繼資料，以邏輯方式將它們組織到分類中。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：在適當情況下，使用標記、管理組和單獨的訂閱來組織和跟蹤 MariaDB 伺服器和相關資源的 Azure 資料庫。 定期協調庫存，確保及時從訂閱中刪除未經授權的資源。

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已批准的 Azure 資源和軟體標題的清單

**指導**：不適用;此建議適用于計算資源和整個 Azure。

**Azure 安全中心監視**：不適用

**責任**： 客戶

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

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>限制使用者通過腳本與 Azure 資源管理器交互的能力</div>

**指南**：使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止訪問"來限制使用者與 Azure 資源管理器交互的能力。 這可以防止在高安全性環境中創建和更改資源，例如包含敏感資訊的 MariaDB 伺服器的 Azure 資料庫。

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

**指南**：使用 Azure 策略為 MariaDB 實例定義和實現 Azure 資料庫的標準安全配置。 在"Microsoft.DBforMariaDB"命名空間中使用 Azure 策略別名創建自訂策略，以審核或強制執行 MariaDB 伺服器 Azure 資料庫的網路設定。 您還可以使用與 MariaDB 伺服器 Azure 資料庫相關的內置策略定義，例如：

- 應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份

如何查看可用的 Azure 策略別名：https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

**指導**：使用 Azure 策略 [拒絕] 和 [部署（如果不存在））在整個 Azure 資源中強制實施安全設置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

**指南**：如果對 MariaDB 伺服器和相關資源的 Azure 資料庫使用自訂 Azure 策略定義，請使用 Azure 存儲庫安全地存儲和管理代碼。

如何在 Azure DevOps 中存儲代碼：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存儲庫文檔：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指南**：使用"Microsoft.DBforMariaDB"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：使用"Microsoft.DBforMariaDB"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 使用 Azure 策略 [審核]、[拒絕]和 [部署（如果不存在））自動為 MariaDB 實例和相關資源的 Azure 資料庫強制實施配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指導**：不適用;此建議用於計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**：對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 MariaDB 伺服器的 Azure 資料庫，請使用 Azure 金鑰保存庫的託管服務標識來簡化和保護用於 MariaDB 伺服器金鑰管理的 Azure 資料庫。 確保啟用金鑰保存庫虛刪除。

如何與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：https://docs.microsoft.com/azure/key-vault/managed-identity 



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**： MariaDB 伺服器的 Azure 資料庫當前不支援 Azure 活動目錄身份驗證以訪問資料庫。  為 MariaDB 伺服器創建 Azure 資料庫時，可以為管理員使用者提供憑據。 此管理員可用於創建其他 MariaDB 使用者。  

對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式，用於訪問 MariaDB 伺服器的 Azure 資料庫，請使用 Azure 金鑰保存庫的託管服務標識來存儲和檢索 MariaDB 的 Azure 資料庫憑據伺服器。  確保啟用金鑰保存庫虛刪除。

使用託管標識在 Azure 活動目錄 （AD） 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務（包括金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。如何配置託管標識：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmHow與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity



**Azure 安全中心監視**：不適用

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

**指南**：在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，MariaDB 伺服器的 Azure 資料庫），但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源的任何內容，如應用服務、資料湖存儲、Blob 存儲、MariaDB 伺服器的 Azure 資料庫等。在這種情況下，Microsoft 無法訪問您的資料。

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指導**：不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，MariaDB 伺服器的 Azure 資料庫），但它不在客戶內容上運行。


**Azure 安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指南**： MariaDB 的 Azure 資料庫採用完整、差異和事務記錄備份。  適用於 MariaDB 的 Azure 資料庫會自動建立伺服器備份，並將其儲存在使用者設定的本地備援或異地備援儲存體中。 備份可以用來將伺服器還原至某個時間點。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。  預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。

瞭解 MariaDB 的備份：https://docs.microsoft.com/azure/mariadb/concepts-backup

瞭解 MariaDB 的初始配置：https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Azure 安全中心監視**：是

**責任**： 共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指南**： MariaDB 的 Azure 資料庫會自動創建伺服器備份，並將它們存儲在使用者配置的本地冗余或異地冗余存儲中。 備份可以用來將伺服器還原至某個時間點。  備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。

如果對存儲在 MariaDB 伺服器中的資料使用金鑰保存庫進行用戶端資料加密，請確保定期自動備份金鑰。

瞭解 MariaDB 的備份：https://docs.microsoft.com/azure/mariadb/concepts-backup

如何備份金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure 安全中心監視**：是

**責任**： 共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**：在 MariaDB 的 Azure 資料庫中，從原始伺服器的備份執行還原，以便定期測試備份。 有兩種類型的還原可用：時間點還原和地理還原。 時間點還原可搭配任一備份備援選項使用，並在您原始伺服器所在區域中建立新的伺服器。 異地還原只能您將伺服器設定為使用異地備援儲存體時使用，這可讓您將伺服器還原至不同的區域。

預估的復原時間取決於數個因素，包括資料庫大小、交易記錄大小、網路頻寬，以及在相同區域中同時進行復原的資料庫總數。 復原時間通常不到 12 小時。

瞭解 MariaDB Azure 資料庫中的備份和還原：https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**： MariaDB 的 Azure 資料庫採用完整、差異和事務記錄備份。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。

瞭解 MariaDB Azure 資料庫中的備份和還原：https://docs.microsoft.com/azure/mariadb/concepts-backup


**Azure 安全中心監視**：是

**責任**： 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：為您的組織構建事件回應指南。 確保有書面事件回應計畫，定義人員的所有角色以及事件處理/管理階段，從檢測到事件後審查。
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：安全中心為每個警報分配嚴重性，以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析，以及導致警報的活動背後存在惡意的置信度。 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

使用標記來組織 Azure 資源：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="103-test-security-response-procedures"></a>10.3： 測試安全回應程式

**指南**：執行練習，以常規節奏測試系統的事件回應功能，以説明保護 Azure 資源。 找出薄弱環節和差距，並根據需要修訂計畫。
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件連絡人詳細資訊，並配置安全事件的警報通知

**指南**：如果 Microsoft 安全回應中心 （MSRC） 發現您的資料已被非法或未經授權的方訪問，Microsoft 將使用安全事件聯繫資訊與您聯繫。 事後查看事件，以確保問題得到解決。
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 將安全警報納入事件回應系統

**指南**：使用"連續匯出"功能匯出 Azure 安全中心警報和建議，以説明識別 Azure 資源的風險。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心資料連線器將警報資料流到 Azure 哨兵。
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自動回應安全警報

**指南**：使用 Azure 安全中心的工作流自動化功能通過安全警報和建議上的"邏輯應用"自動觸發回應，以保護 Azure 資源。
    

如何配置工作流自動化和邏輯應用：https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊，請參閱[安全控制：滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：對 Azure 資源進行定期滲透測試，並確保在 60 天內補救所有關鍵安全發現

**指南**： 遵循 Microsoft 參與規則，確保您的滲透測試不違反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和執行的詳細資訊，如下所示：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure 安全中心監視**：不適用

**責任**： 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
