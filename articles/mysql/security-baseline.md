---
title: MySQL Azure 資料庫的 Azure 安全基線
description: MySQL Azure 資料庫的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3e79d6efcbb72b73dedb5b8dfdf965736e7de3e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757208"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>MySQL Azure 資料庫的 Azure 安全基線

MySQL Azure 資料庫的 Azure 安全基線包含一些建議,可説明您改進部署的安全狀態。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview),它提供了有關如何使用最佳實務指南在 Azure 上保護雲端解決方案的建議。

有關詳細資訊,請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊,請參閱[安全控制:網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:使用虛擬網路上的網路安全組或 Azure 防火牆保護資源

**指南**:為具有專用終結點的 MySQL 配置 Azure 資料庫的專用連結。 Private Link 可讓您透過私人端點連線到 Azure 中的各種 PaaS 服務。 Azure 專用連結實質上將 Azure 服務引入專用虛擬網路 (VNet)。 虛擬網路和 MySQL 實例之間的流量將傳輸 Microsoft 骨幹網路。

或者,您可以使用虛擬網路服務終結點來保護和限制對 Azure 資料庫的網路存取,以便實現 MySQL 實現。 虛擬網路規則是一項防火牆安全性功能，可控制適用於 MySQL 的 Azure 資料庫伺服器是否接受從虛擬網路中特定子網路所傳來的通訊。

您還可以使用防火牆規則保護 MySQL 伺服器的 Azure 資料庫。 在指定哪些電腦具有許可權之前,伺服器防火牆會阻止對資料庫伺服器的所有訪問。 若要設定您的防火牆，您可以建立防火牆規則，指定可接受的 IP 位址範圍。 您可以在伺服器層級建立防火牆規則。

如何為 MySQL 設定 Azure 資料庫的專用連結:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

如何在 MySQL 的 Azure 資料庫中建立和管理 VNet 服務終結點和 VNet 規則:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

如何為 MySQL 防火牆規則配置 Azure 資料庫:https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: 監視和紀錄 Vnet、子網和 NIC 的配置和流量

**指南**:當 MySQL 實例的 Azure 資料庫安全到專用終結點時,可以在同一虛擬網路中部署虛擬機器。 您可以使用網路安全組 (NSG) 來降低數據洩露的風險。 啟用 NSG 串流日誌並將日誌發送到儲存帳戶以進行流量審核。 您還可以將 NSG 串流日誌發送到日誌分析工作區,並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠可視化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何為 MySQL 設定 Azure 資料庫的專用連結:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

如何啟用 NSG 串流紀錄:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用與使用流量分析:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

**指南**:對 MySQL 的 Azure 資料庫使用進階威脅保護。 高級威脅防護可檢測異常活動,指示訪問或利用資料庫的異常和潛在有害嘗試。

在與 MySQL 資料庫關聯的虛擬網路上啟用 DDoS 保護標準,以防止 DDoS 攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通訊。

如何為 MySQL 設定 Azure 資料庫的進階威脅保護:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

如何設定 DDoS 保護:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 記錄網路封包與串流紀錄

**指南**:當 MySQL 實例的 Azure 資料庫安全到專用終結點時,可以在同一虛擬網路中部署虛擬機器。 然後,您可以配置網路安全組 (NSG)以降低數據洩露的風險。 啟用 NSG 串流日誌並將日誌發送到儲存帳戶以進行流量審核。 您還可以將 NSG 串流日誌發送到日誌分析工作區,並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠可視化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

如何啟用 NSG 串流紀錄:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用與使用流量分析:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統(IDS/IPS)

**指南**:對 MySQL 的 Azure 資料庫使用進階威脅保護。 高級威脅防護可檢測異常活動,指示訪問或利用資料庫的異常和潛在有害嘗試。

如何為 MySQL 設定 Azure 資料庫的進階威脅保護:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 管理 Web 應用程式的流量

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

**指南**:對於需要存取MySQL實例的Azure資料庫的資源,請使用虛擬網路服務標記在網路安全組或 Azure 防火牆上定義網路存取控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過指定服務標記名稱(例如 SQL。WestUs) 在規則的相應源或目標欄位中,您可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

注意:MySQL 的 Azure 資料庫使用"Microsoft.Sql"服務標記。

有關使用服務標記的詳細資訊:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

瞭解 MySQL Azure 資料庫的服務標記使用方式:https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

**指南**:使用 Azure 策略為 MySQL 實例定義和實施與 Azure 資料庫關聯的網路設置和網路資源的標準安全配置。 在「Microsoft.DBforMySQL」和「Microsoft.Network」命名空間中使用 Azure 策略別名來創建自訂策略,以審核或強制執行 MySQL 實例的 Azure 資料庫的網路配置。 您還可以使用與網路相關的內建策略定義或 MySQL 實體的 Azure 資料庫,例如:

- 應啟用 DDoS 保護標準

- 應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

網路的 Azure 政策範例:https://docs.microsoft.com/azure/governance/policy/samples/

如何建立 Azure 藍圖:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

**指南**:對 MySQL 實例的 Azure 資料庫的網路安全和流量流相關的資源使用標記來提供元數據和邏輯組織。

使用與標記相關的任何內置 Azure 策略定義(如"要求標記及其值")來確保所有資源都使用標記創建,並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

**指南**:使用 Azure 活動日誌監視網路資源配置,並檢測與 MySQL 實例的 Azure 資料庫相關的網路資源的更改。 在 Azure 監視器中創建警報,這些警報將在對關鍵網路資源的更改時觸發。

如何檢視和檢索 Azure 活動日誌事件:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊,請參閱[安全控制:日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 使用批准的時間同步來源

**指南**:Microsoft 維護用於 Azure 資源的時間源,例如日誌中時間戳的 MySQL 的 Azure 資料庫。

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="22-configure-central-security-log-management"></a>2.2: 配置中央安全日誌管理

**指南**:啟用診斷設定和伺服器日誌以及引入日誌以聚合由 Azure 資料庫為 MySQL 實例生成的安全數據。 在 Azure 監視器中,使用日誌分析工作區來查詢和執行分析,並使用 Azure 儲存帳戶進行長期/存檔儲存。 或者,您可以將資料啟用到 Azure Sentinel 或第三方 SIEM。

瞭解 MySQL Azure 資料庫的伺服器紀錄:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何登上 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:為 Azure 資源啟用稽核紀錄記錄

**指南**:在 Azure 資料庫上為 MySQL 實例啟用診斷設置,以造訪審核、慢速查詢和 MySQL 指標日誌。 確保專門啟用 MySQL 審核日誌。 活動日誌自動可用,包括事件來源、日期、使用者、時間戳、源位址、目標位址和其他有用元素。 您還可以啟用 Azure 活動日誌診斷設定,並將日誌發送到相同的日誌分析工作區或儲存帳戶。

瞭解 MySQL Azure 資料庫的伺服器紀錄:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何為 MySQL 設定與存取 Azure 資料庫的慢速查詢紀錄:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

如何為 MySQL 設定與存取 Azure 資料庫的稽核紀錄:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

如何設定 Azure 活動紀錄的診斷設定:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 從作業系統收集安全日誌

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5: 設定安全紀錄儲存保留

**指南**:在 Azure 監視器中,對於用於保存 MySQL 日誌的 Azure 資料庫的日誌分析工作區,請根據組織的合規性法規設置保留期。 使用 Azure 儲存帳戶進行長期/存檔存儲。

如何為紀錄分析工作區設定紀錄保留參數:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

在 Azure 儲存帳戶中儲存資源紀錄:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="26-monitor-and-review-logs"></a>2.6: 監視與檢視紀錄

**指南**:分析和監視 Azure 資料庫中的 MySQL 實例的日誌異常行為。 使用 Azure 監視器的日誌分析查看日誌並執行日誌數據的查詢。 或者,您可以將數據啟用並車載到 Azure Sentinel 或第三方 SIEM。

如何登上 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

關於紀錄分析的詳細資訊:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 啟用例外活動的警示

**指南**:為 MySQL 啟用 Azure 資料庫的高級威脅保護。 高級威脅防護可檢測異常活動,指示訪問或利用資料庫的異常和潛在有害嘗試。

此外,您還可以為 MySQL 啟用伺服器日誌和診斷設置,並將日誌發送到日誌分析工作區。 將日誌分析工作區添加到 Azure Sentinel 中,因為它提供安全編排自動回應 (SOAR) 解決方案。 這允許創建行動手冊(自動解決方案),並用於修復安全問題。

如何為 MySQL 的 Azure 資料庫啟用進階威脅保護(預覽):https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

瞭解 MySQL Azure 資料庫的伺服器紀錄:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

如何為 MySQL 設定與存取 Azure 資料庫的慢速查詢紀錄:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

如何為 MySQL 設定與存取 Azure 資料庫的稽核紀錄:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

如何設定 Azure 活動紀錄的診斷設定:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

如何登上 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8: 集中反惡意軟體紀錄記錄

**指導**:不適用;MySQL 的 Azure 資料庫不處理或生成與惡意軟體相關的日誌。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="29-enable-dns-query-logging"></a>2.9: 啟用 DNS 查詢紀錄記錄

**指南**:不適用;MySQL 的 Azure 資料庫不處理或生成 DNS 相關日誌。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10: 啟用命令列稽核紀錄記錄

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊,請參閱[安全控制:識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 維持管理帳戶的清單

**指南**:維護對 MySQL實例 Azure 資料庫的管理平面(例如 Azure 門戶)具有管理存取許可權的使用者帳戶的清單。 此外,維護有權訪問 MySQL 實例 Azure 資料庫的數據平面(在資料庫本身內)的管理帳戶的清單。 (創建 MySQL 伺服器時,為管理員使用者提供認證。 此管理員可用於創建其他 MySQL 使用者。

MySQL 的 Azure 資料庫不支援基於內建角色的存取控制,但可以基於特定資源提供程式選項創建自定義角色。

瞭解 Azure 訂閱的自訂角色:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

瞭解 MySQL 資源提供程式操作的 Azure 資料庫:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

瞭解 MySQL Azure 資料庫的存取管理:https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

**指南**:Azure AD 沒有預設密碼的概念。

為 MySQL 資源本身創建 Azure 資料庫後,Azure 將強制創建具有強密碼的管理使用者。 但是,創建 MySQL 實例後,您可以使用您創建的第一個伺服器管理員帳戶來創建其他使用者並授予對它們的管理存取許可權。 創建這些帳戶時,請確保為每個帳戶配置不同的強密碼。

如何為 MySQL 建立 Azure 資料庫的其他帳戶:https://docs.microsoft.com/azure/mysql/howto-create-users

如何更新管理員密碼:https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 使用專用管理帳戶

**指南**:圍繞使用有權訪問 MySQL 實例的 Azure 資料庫的專用管理帳戶創建標準操作過程。 使用 Azure 安全中心識別和訪問管理監視管理帳戶數。

瞭解 Azure 安全中心識別和存取:https://docs.microsoft.com/azure/security-center/security-center-identity-access

瞭解如何在 Azure 資料庫中為 MySQL 建立管理員使用者:https://docs.microsoft.com/azure/mysql/howto-create-users

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: 使用 Azure 的目錄的單一登入 (SSO)

**指南**:支援使用直接在資料庫中配置的使用者名/密碼,以及使用 Azure 活動目錄 (AD) 標識並使用 Azure AD 令牌進行連接,都支援登錄到 MySQL 的 Azure 資料庫。 使用 Azure AD 權杖時,支援不同方法,例如 Azure AD 使用者、Azure AD 組或連接到資料庫的 Azure AD 應用程式。

另外,MySQL 的控制平面訪問可透過 REST API 獲得,並支援 SSO。 要進行身份驗證,請將請求的授權標頭設置為從 Azure 活動目錄獲取的 JSON Web 權杖。

使用 Azure 活動目錄使用 Azure 資料庫進行 MySQL 身份驗證:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

瞭解 MySQL REST API 的 Azure 資料庫:https://docs.microsoft.com/rest/api/mysql/

使用 Azure AD 瞭解 SSO:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的存取使用多重身份驗證

**指南**:啟用 Azure 活動目錄多重身份驗證 (MFA),並遵循 Azure 安全中心標識和訪問管理建議。 使用 Azure AD 權杖登入資料庫時,這允許您要求對資料庫登錄進行多重身份驗證。

如何在 Azure 中啟用 MFA:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

使用 Azure 活動目錄使用 Azure 資料庫進行 MySQL 身份驗證:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

如何在 Azure 安全中心內監視標識和訪問:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

**指南**:使用特權存取工作站 (PAW) 與多因素身份驗證 (MFA) 配置為登入和配置 Azure 資源。

瞭解特權存取工作站:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 從管理帳戶記錄和警報可疑活動

**指南**:為 MySQL 的 Azure 資料庫啟用高級威脅保護,以生成可疑活動的警報。

此外,當環境中發生可疑或不安全活動時,可以使用 Azure AD 特權標識管理 (PIM) 生成日誌和警報。

使用 Azure AD 風險檢測查看有關風險用戶行為的警報和報告。

如何為 MySQL 設定 Azure 資料庫的進階威脅保護:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

如何部署權識別管理 (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

瞭解 Azure AD 風險偵測:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

**指南**:使用條件訪問命名位置允許門戶和 Azure 資源管理員僅從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中設定命名位置:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="39-use-azure-active-directory"></a>3.9: 使用 Azure 的項目

**指南**:使用 Azure 活動目錄 (AD) 作為中央身份驗證和授權系統。 Azure AD 對靜態和傳輸中的數據使用強加密來保護數據。 Azure AD 還會對使用者憑據進行鹽漬、哈希和安全地存儲。

對於 MySQL 登入到 Azure 資料庫,建議使用 Azure AD 並使用 Azure AD 權杖進行連接。 使用 Azure AD 權杖時,支援不同方法,例如 Azure AD 使用者、Azure AD 組或連接到資料庫的 Azure AD 應用程式。 

Azure AD 認證還可用於管理平面等級(例如 Azure 入口)的管理,以控制 MySQL 管理帳戶。

使用 Azure 活動目錄使用 Azure 資料庫進行 MySQL 身份驗證:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期審查和協調使用者存取

**指導**:查看 Azure 活動目錄日誌,以幫助發現陳舊的帳戶,這些帳戶可以包括具有 MySQL 管理角色的 Azure 資料庫的帳戶。 此外,使用 Azure 標識存取審核可有效地管理組成員身份、訪問可用於訪問 MySQL Azure 資料庫的企業應用程式以及角色分配。 使用者訪問許可權應定期審查,例如每 90 天一次,以確保只有正確的使用者才能繼續訪問。

瞭解 Azure AD 報告https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 識別存取稽核:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 監視訪問已停用帳戶的嘗試

**指南**:為 MySQL 和 Azure 活動目錄啟用 Azure 資料庫的診斷設置,將所有日誌發送到日誌分析工作區。 在日誌分析中配置所需的警報(如身份驗證嘗試失敗)。

如何為 MySQL 設定與存取 Azure 資料庫的慢速查詢紀錄:https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

如何為 MySQL 設定與存取 Azure 資料庫的稽核紀錄:https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

如何將 Azure 活動紀錄整合到 Azure 監視器中:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

**指南**:為 MySQL 的 Azure 資料庫啟用高級威脅保護,以生成可疑活動的警報。

使用 Azure 活動目錄的標識保護和風險檢測功能配置自動回應以檢測出的可疑操作。 您可以通過 Azure Sentinel 啟用自動回應,以實現組織的安全回應。

您還可以將日誌引入 Azure Sentinel 以進行進一步調查。

如何為 MySQL 設定 Azure 資料庫的進階威脅保護:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Azure AD 識別保護概述:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

如何檢視 Azure AD 風險登入:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何登上 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 在支援方案期間向 Microsoft 提供對相關客戶資料的存取

**指導**:不適用;MySQL 的 Azure 資料庫尚不支援客戶密碼箱。

客戶密碼箱支援服務清單:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心監視**:不適用

**責任**: 不適用

## <a name="data-protection"></a>資料保護

*有關詳細資訊,請參閱[安全控制:資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊的清單

**指南**:使用標記來協助追蹤 MySQL 實體或儲存或處理敏感資訊的相關資源的 Azure 資料庫。

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

**指導**:為開發、測試和生產實施單獨的訂閱和/或管理組。 使用專用鏈路、服務終結點和/或防火牆規則的組合來隔離和限制對 MySQL 實例的 Azure 資料庫的網路訪問。

如何建立其他 Azure 訂閱:https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:https://docs.microsoft.com/azure/governance/management-groups/create

如何為 MySQL 設定 Azure 資料庫的專用連結:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

如何在 MySQL 的 Azure 資料庫中建立和管理 VNet 服務終結點和 VNet 規則:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

如何為 MySQL 防火牆規則配置 Azure 資料庫:https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Azure 安全中心監視**:不可用

**責任**: 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊

**指南**:當使用 Azure VM 訪問 MySQL 實例的 Azure 資料庫時,請使用專用鏈路、MySQL 網路配置、網路安全組和服務標記來降低數據外洩的可能性。

Microsoft 管理 MySQL Azure 資料庫的基礎基礎結構,並實施了嚴格的控制,以防止客戶數據丟失或暴露。

如何緩解 MySQL Azure 資料庫的數據滲漏:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

**指南**: MySQL 的 Azure 資料庫支援使用安全套接字層 (SSL) 將 MySQL 伺服器連接到用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 在 Azure 門戶中,確保預設情況下為 MySQL 實例的所有 Azure 資料庫啟用「強制 SSL 連接」。。

目前,MySQL Azure 資料庫支援的 TLS 版本為 TLS 1.0、TLS 1.1、TLS 1.2。

如何為 MySQL 設定 Azure 資料庫的傳輸加密:https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Azure 安全中心監視**:不可用

**責任**: 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 使用活動探索工具辨識敏感資料

**指南**:MySQL 的 Azure 資料庫尚未提供數據識別、分類和損失預防功能。 如果需要符合性,則實施第三方解決方案。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:不可用

**責任**: 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: 使用 Azure RBAC 控制對資源的存取

**指導**:使用基於 Azure 角色的存取控制 (RBAC) 來控制對 MySQL 控制平面(例如 Azure 門戶)的 Azure 資料庫的訪問。 對於數據平面訪問(在資料庫本身中),使用 SQL 查詢創建使用者並配置用戶許可權。 RBAC 不會影響資料庫中的用戶許可權。

如何在 Azure 中設定 RBAC:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

如何使用適用於 MySQL 的 Azure 資料庫的 SQL 設定使用者存取:https://docs.microsoft.com/azure/mysql/howto-create-users

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

**指導**:不適用;此建議用於計算資源。

Microsoft 管理 MySQL Azure 資料庫的基礎基礎結構,並實施了嚴格的控制,以防止客戶數據丟失或暴露。

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

**指南**: MySQL 服務的 Azure 資料庫使用 FIPS 140-2 驗證加密模組對靜態數據進行儲存加密。 數據(包括備份)在磁碟上加密,但運行查詢時創建的暫存檔除外。 該服務使用包含在 Azure 儲存體加密中的 AES 256 位元加密，且金鑰是由系統進行管理。 儲存體加密會一律啟用，且無法停用。

使用 MySQL Azure 資料庫的客戶管理金鑰進行資料加密使您能夠自帶密鑰 (BYOK) 以進行靜態數據保護。 此時,您必須請求訪問才能使用此功能。 為此,請聯繫:

AskAzureDBforMySQL@service.microsoft.com

瞭解 MySQL Azure 資料庫的靜態加密:https://docs.microsoft.com/azure/mysql/concepts-security

如何為 MySQL 設定 Azure 資料庫的客戶託管金鑰:https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

**指南**:將 Azure 監視器與 Azure 活動日誌一起用於創建警報,用於當 MySQL 和其他關鍵或相關資源的 Azure 数据庫的生產實例發生更改時。"

如何為 Azure 活動紀錄事件建立警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊,請參閱[安全控制:漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 執行自動漏洞掃描工具

**指南**:當前不可用;Azure 安全中心尚不支援 MySQL Azure 資料庫的漏洞評估。

Azure 安全中心 Azure PaaS 服務的功能覆蓋範圍:https://docs.microsoft.com/azure/security-center/features-paas

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:部署自動化作業系統修補程式管理解決方案

**指導**:不適用;本指南用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:部署自動化的第三方軟體修補程式管理解決方案

**指導**:不適用;本指南用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 比較背對背漏洞掃描

**指導**:不適用;本指南用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**:Microsoft 在支援 MySQL Azure 資料庫的基礎系統上執行漏洞管理。


**Azure 安全中心監視**:不適用

**責任**: 微軟

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊,請參閱[安全控制:庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1: 使用 Azure 資產發現

**指南**:使用 Azure 資源圖查詢和發現訂閱中的所有資源(包括 MySQL 實例的 Azure 資料庫)。 確保租戶中具有適當的(讀取)許可權,並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

如何使用 Azure 資源圖建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂閱:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

**指南**:將標記應用於 MySQL 實例和其他相關資源的 Azure 資料庫,從而提供元數據,以邏輯方式將它們組織到分類中。

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

**指南**:在適當情況下,使用標記、管理組和單獨的訂閱來組織和跟蹤 MySQL 實例和相關資源的 Azure 資料庫。 定期協調庫存,確保及時從訂閱中刪除未經授權的資源。

如何建立其他 Azure 訂閱:https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:維護已批准的 Azure 資源和軟體標題的清單

**指導**:不適用;此建議適用於計算資源和整個 Azure。

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型

- 允許的資源類型

此外,使用 Azure 資源圖查詢/發現訂閱中的資源。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中未經批准的軟體應用程式

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

**指導**:不適用;此建議適用於計算資源和整個 Azure。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="68-use-only-approved-applications"></a>6.8: 僅使用已批准的應用程式

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="69-use-only-approved-azure-services"></a>6.9: 僅使用已批准的 Azure 服務

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型

- 允許的資源類型

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 政策拒絕特定資源類型:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="610-implement-approved-application-list"></a>6.10: 實施已批准的申請清單

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:限制使用者透過文稿與 Azure 資源管理員互動的能力

**指南**:使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止存取"來限制使用者與 Azure 資源管理員互動的能力。 這可以防止在高安全性環境中創建和更改資源,例如包含敏感資訊的 MySQL 的 Azure 資料庫實例。

如何設定條件存取以封鎖對 Azure 資源管理員的存取:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯上隔離高風險應用程式

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**: 不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊,請參閱[安全控制:安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

**指南**:使用 Azure 策略為 MySQL 實例定義和實現 Azure 資料庫的標準安全配置。 在「Microsoft.DBforMySQL」命名空間中使用 Azure 策略別名創建自訂策略,以審核或強制執行 MySQL 實例的 Azure 資料庫的網路配置。 您還可以使用與 MySQL 實體的 Azure 資料庫相關的內建策略定義,例如:

應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]

如何檢視可用的 Azure 政策別名:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 建立安全的作業系統設定

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:維護安全的 Azure 資源配置

**指導**:使用 Azure 策略 [拒絕] 和 [部署(如果不存在))在整個 Azure 資源中強制實施安全設置。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 政策效果:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 維護安全的作業系統設定

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源的設定

**指南**:如果對 MySQL 實體和相關資源的 Azure 資料庫使用自訂 Azure 策略定義,請使用 Azure 儲存庫安全地儲存和管理代碼。

如何在 Azure DevOps 中儲存代碼:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 儲存函式庫文件:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 部署系統設定管理工具

**指南**:使用「Microsoft.DBforMySQL」命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統配置。 此外,開發用於管理策略異常的流程和管道。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:為作業系統部署系統設定管理工具

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:為 Azure 服務實現自動化配置監視

**指南**:使用「Microsoft.DBforMySQL」命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統配置。 使用 Azure 策略 [審核]、[拒絕]和 [部署(如果不存在))自動為 MySQL 實例和相關資源強制實施 Azure 資料庫的配置。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 對作業系統執行自動設定監控

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11: 安全管理 Azure 機密

**指南**:對於在 Azure 應用服務上運行的 Azure 虛擬機或 Web 應用程式,用於造訪 MySQL 實例的 Azure 資料庫,請使用 Azure 密鑰保管庫的託管服務標識來簡化和保護 Azure 資料庫以進行 MySQL 密鑰管理。 確保啟用金鑰保管庫軟刪除。

如何與 Azure 託管識別整合:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立金鑰保存庫:https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管識別提供金鑰保管庫身份驗證:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 安全、自動地管理身份

**指南**: MySQL 實體的 Azure 資料庫支援 Azure 活動目錄身份驗證(預覽版)以存取資料庫。  為 MySQL 實例創建 Azure 資料庫時,可以為管理員使用者提供認證。 此管理員可用於創建其他資料庫使用者。  

對於在 Azure 應用服務上運行的 Azure 虛擬機器或 Web 應用程式,用於造訪 MySQL 實體的 Azure 資料庫,請使用 Azure 金鑰保管庫的託管服務標識來儲存和檢索 MySQL 實例的 Azure 資料庫認證。 確保啟用金鑰保管庫軟刪除。

使用託管識別在 Azure 活動目錄 (AD) 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務(包括密鑰保管庫)進行身份驗證,而無需在代碼中進行任何認證。

如何設定託管識別:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何與 Azure 託管識別整合:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 消除意外的認證

**指南**:實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置,如 Azure 密鑰保管庫。

如何設定認證程式:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊,請參閱[安全控制:惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 使用集中管理的反惡意軟體

**指導**:不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,SQL 的 Azure 資料庫),但它不在客戶內容上運行。

**Azure 安全中心監視**:不適用

**責任**: 不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:要上傳到非計算 Azure 資源的預入檔案

**指南**:在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,MySQL 的 Azure 資料庫),但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源的任何內容,如應用服務、資料湖存儲、Blob 存儲、MySQL 的 Azure 資料庫等。在這種情況下,Microsoft 無法訪問您的數據。

**Azure 安全中心監視**:不適用

**責任**: 共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 確保更新反惡意軟體和簽名

**指導**:不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,MySQL 的 Azure 資料庫),但它不在客戶內容上運行。

**Azure 安全中心監視**:不適用

**責任**: 不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊,請參閱[安全控制:資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

**指南**:MySQL 的 Azure 資料庫獲取數據檔和事務日誌的備份。 根據支援的最大儲存大小,我們要麼進行完整備份和差分備份(最多 4 TB 儲存伺服器)或快照備份(最多 16 TB 的儲存伺服器)。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。

瞭解 MySQL Azure 資料庫的備份:https://docs.microsoft.com/azure/mysql/concepts-backup

瞭解 MySQL 初始配置的 Azure 資料庫:https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Azure 安全中心監視**:是

**責任**: 共用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

**指南**:MySQL 的 Azure 資料庫根據使用者的選擇自動創建伺服器備份並將其存儲在本地冗餘存儲或異地冗餘存儲中。 備份可以用來將伺服器還原至某個時間點。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。 

如果使用 Azure 金鑰保管庫為 MySQL 實例存儲 Azure 資料庫的認證,請確保定期自動備份金鑰。 

瞭解 MySQL Azure 資料庫的備份:https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

如何備份金鑰保存庫金鑰:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure 安全中心監視**:是

**責任**: 共用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

**指南**:在 MySQL 的 Azure 資料庫中,執行還原會從原始伺服器的備份創建新伺服器。 有兩種類型的還原可用:時間點還原和地理還原。 時間點還原可搭配任一備份備援選項使用，並在您原始伺服器所在區域中建立新的伺服器。 異地還原只能您將伺服器設定為使用異地備援儲存體時使用，這可讓您將伺服器還原至不同的區域。

預估的復原時間取決於數個因素，包括資料庫大小、交易記錄大小、網路頻寬，以及在相同區域中同時進行復原的資料庫總數。 復原時間通常不到 12 小時。

定期測試 MySQL 實例 Azure 資料庫的還原。

瞭解 MySQL Azure 資料庫中的備份和還原:https://docs.microsoft.com/azure/mysql/concepts-backup

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

**指南**:MySQL 的 Azure 資料庫需要完整、差異和事務日誌備份。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。 確保啟用金鑰保管庫軟刪除。

瞭解 MySQL Azure 資料庫中的備份和還原:https://docs.microsoft.com/azure/mysql/concepts-backup

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊,請參閱[安全控制:事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1:建立事件回應指南

**指導**:為您的組織構建事件回應指南。 確保有書面事件回應計劃,定義人員的所有角色以及事件處理/管理階段,從檢測到事件後審查。

如何在 Azure 安全中心內配置工作流自動化:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有關構建您自己的安全事件回應流程的指導:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微軟安全回應中心的事件剖析:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客戶還可以利用 NIST 的電腦安全事件處理指南來幫助創建他們自己的事件回應計劃:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 建立事件評分與優先順序處理程序

**指南**:安全中心為每個警報分配嚴重性,以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。

此外,清楚地標記訂閱(對於前 生產,非 prod),並創建命名系統以明確識別和分類 Azure 資源。

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

**指導**:進行練習,以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距,並根據需要修訂計劃。

請參閱 NIST 出版物:IT 計劃和功能測試、培訓和鍛煉計劃指南:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:提供安全事件聯繫人詳細資訊,並配置安全事件的警報通知

**指南**:如果 Microsoft 安全回應中心 (MSRC) 發現客戶的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。  事後查看事件,以確保問題得到解決。

如何設置 Azure 安全中心安全連絡人:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

**指南**:使用「連續匯出」功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心數據連接器來流式傳輸警報哨兵。

如何設定連續匯出:https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報流式傳輸到 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

**指南**:使用 Azure 安全中心的工作流自動化功能,通過安全警報和建議上的「邏輯應用」自動觸發回應。

如何設定工作流自動化和邏輯應用:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊,請參閱[安全控制:滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:對 Azure 資源進行定期滲透測試,並確保在 60 天內補救所有關鍵安全發現

**指南**: 遵循 Microsoft 參與規則,確保您的滲透測試不違反 Microsoft 策略:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲端基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的策略和執行的詳細資訊,如下所示:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**:不適用

**責任**: 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
