---
title: 適用於 Key Vault 的 Azure 安全性基準
description: 適用於 Key Vault 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616888"
---
# <a name="azure-security-baseline-for-key-vault"></a>適用於 Key Vault 的 Azure 安全性基準

金鑰保管庫的 Azure 安全基線包含可説明您改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview),它提供了有關如何使用最佳實務指南在 Azure 上保護雲端解決方案的建議。

有關詳細資訊,請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊,請參閱[安全控制:網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:使用虛擬網路上的網路安全組或 Azure 防火牆保護資源

**指南**:將 Azure 密鑰保管庫與 Azure 專用連結集成。 

Azure 專用連結服務使您能夠透過虛擬網路中的專用終結點存取 Azure 服務(例如,Azure 密鑰保管庫)和 Azure 託管的客戶/合作夥伴服務。

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如何將金鑰保管庫與 Azure 專用連結整合:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: 監視和紀錄 Vnet、子網和 NIC 的配置和流量

**指南**:使用 Azure 安全中心並遵循網路保護建議來幫助保護 Azure 中的關鍵保管庫配置的資源。 

有關 Azure 安全中心提供的網路安全的詳細資訊: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

**指南**:在與密鑰保管庫實例關聯的 Azure 虛擬網路上啟用 Azure DDoS 保護標準,以抵禦分散式拒絕服務攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通訊。

 
使用 Azure 門戶管理 Azure DDoS 保護標準:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 安全中心中的 Azure 服務層的威脅檢測:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 記錄網路封包與串流紀錄

**指南**:Azure 密鑰保管庫不使用網路安全組 (NSG),並且未捕獲 Azure 密鑰保管庫的流日誌。 相反,請使用 Azure 專用連結來保護 Azure 密鑰保管庫實例,並使診斷設置能夠記錄指標和審核事件。

將金鑰保管庫與 Azure 專用連結整合:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure 金鑰保管庫紀錄記錄:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統(IDS/IPS)

**指南**:可通過為 Azure 密鑰保管庫配置高級威脅保護 (ATP) 來滿足此要求。 ATP 提供了額外的安全智能層。 此工具檢測訪問或利用 Azure 密鑰保管庫帳戶的潛在有害嘗試。

當 Azure 安全中心檢測到異常活動時,它會顯示警報。 它還向訂閱管理員發送電子郵件,提供可疑活動的詳細資訊以及如何調查和修復已識別的威脅的建議。

為 Azure 金鑰保管庫設定進階威脅保護:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 管理 Web 應用程式的流量

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

**指南**:對於需要存取 Azure 密鑰保管庫實例的資源,請使用 Azure 密鑰保管庫的 Azure 服務標記在網路安全組或 Azure 防火牆上定義網路存取控制件。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱(例如 ApiManagement),可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

Azure 服務標記概述:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

**指南**:使用 Azure 策略為與 Azure 密鑰保管庫實例關聯的網路資源定義和實施標準安全配置。 在「Microsoft.KeyVault」和「Microsoft.Network」命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行 Azure 密鑰保管庫實例的網路配置。 您還可以使用與 Azure 金鑰保管庫相關的內建策略定義,例如:

Key Vault 應該使用虛擬網路服務端點

教學:建立與管理原則以強制實施合規性:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 政策範例:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

快速入門:在門戶中定義和分配藍圖:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

**指南**:對 Azure 密鑰保管庫實例的網路安全和流量流相關的資源使用標記來提供元數據和邏輯組織。

使用與標記相關的任何內置 Azure 策略定義(如"要求標記及其值")確保所有資源都使用標記創建,並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

使用標記來組織 Azure 資源:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

**指南**:使用 Azure 活動日誌監視網路資源配置,並檢測與 Azure 密鑰保管庫實例相關的網路資源的更改。 在 Azure 監視器中創建警報,這些警報將在對關鍵網路資源的更改時觸發。

檢視並取得檢索 Azure 活動紀錄事件:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

使用 Azure 監視器建立、檢視與管理活動紀錄警報:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊,請參閱[安全控制:日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 使用批准的時間同步來源

**指導**:不適用;Microsoft 維護用於 Azure 資源(如 Azure 密鑰保管庫)的時間源,用於日誌中的時間戳。


**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="22-configure-central-security-log-management"></a>2.2: 配置中央安全日誌管理

**指南**:通過 Azure 監視器引入日誌以聚合 Azure 密鑰保管庫生成的安全數據。 在 Azure 監視器中,使用 Azure 日誌分析工作區來查詢和執行分析,並使用 Azure 儲存帳戶進行長期/存檔存儲。 或者,您可以將資料啟用到 Azure Sentinel 或第三方 SIEM。 

Azure 金鑰保管庫紀錄記錄:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

快速入門:如何登上 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:為 Azure 資源啟用稽核紀錄記錄

**指南**:在 Azure 密鑰保管庫實例上啟用診斷設置,以訪問審核、安全和診斷日誌。 活動日誌自動可用,包括事件來源、日期、使用者、時間戳、源位址、目標位址和其他有用元素。

Azure 金鑰保管庫紀錄記錄:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 從作業系統收集安全日誌

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5: 設定安全紀錄儲存保留

**指南**:在 Azure 監視器中,對於用於保存 Azure 密鑰保管庫日誌的日誌分析工作區,請根據組織的合規性法規設置保留期。 使用 Azure 儲存帳戶進行長期/存檔存儲。

變更資料保留期:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="26-monitor-and-review-logs"></a>2.6: 監視與檢視紀錄

**指南**:分析和監視日誌是否存在異常行為,並定期查看 Azure 密鑰保管庫保護資源的結果。 使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌數據的查詢。 或者,您可以將數據啟用並車載到 Azure Sentinel 或第三方 SIEM。 

快速入門:板載 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

開始在 Azure 監視器中的紀錄分析:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

開始在 Azure 監視器中的紀錄查詢:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 啟用例外活動的警示

**指南**:在 Azure 安全中心中,為金鑰保管庫啟用高級威脅保護 (ATP)。 在 Azure 金鑰保管庫中啟用診斷設置,並將日誌發送到日誌分析工作區。 將日誌分析工作區添加到 Azure Sentinel 中,因為它提供安全編排自動回應 (SOAR) 解決方案。 這允許創建行動手冊(自動解決方案),並用於修復安全問題。

快速入門:板載 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

管理和回應 Azure 安全中心中的安全警報:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

使用 Azure 監視器警報回應事件:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8: 集中反惡意軟體紀錄記錄

**指導**:不適用;Azure 密鑰保管庫不處理或生成與惡意軟體相關的日誌。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="29-enable-dns-query-logging"></a>2.9: 啟用 DNS 查詢紀錄記錄

**指導**:不適用;Azure 密鑰保管庫不處理或生成與 DNS 相關的日誌。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10: 啟用命令列稽核紀錄記錄

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊,請參閱[安全控制:識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 維持管理帳戶的清單

**指南**:維護 Azure 活動目錄註冊應用程式的清單,以及有權訪問 Azure 密鑰保管庫密鑰、機密和證書的任何使用者帳戶。 您可以使用 Azure 門戶或 PowerShell 查詢和協調密鑰保管庫訪問許可權。 要在 PowerShell 中查看存取權限,請使用以下指令:

(獲取 Az 資源 - 資源 Id [金鑰庫資源 ID])。屬性.存取原則

將應用程式註冊到 Azure 活動目錄:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

安全存取金鑰保管庫:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

**指導**:不適用;Azure 密鑰保管庫沒有預設密碼的概念,因為身份驗證由 Active Directory 提供,並且使用基於角色的訪問控制進行保護。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 使用專用管理帳戶

**指南**:圍繞使用有權訪問 Azure 密鑰保管庫實例的專用管理帳戶創建標準操作過程。 使用 Azure 安全中心識別和存取管理(目前處於預覽狀態)監視活動管理帳戶的數量。

監視身分和存取(預覽):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: 使用 Azure 的目錄的單一登入 (SSO)

**指南**:將 Azure 服務主體與 AppId、租戶 ID 和用戶端機密結合使用,以無縫驗證應用程式並檢索將用於訪問 Azure 密鑰保管庫機密的權杖。

使用 .NET 對 Azure 金鑰保管庫的服務到服務身份驗證:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的存取使用多重身份驗證

**指南**:啟用 Azure 活動目錄多重身份驗證,並遵循 Azure 安全中心標識和訪問管理(當前預覽版)建議,以説明保護啟用事件集線器的資源。

規劃基於雲的 Azure 多重身份驗證部署:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

監視身分和存取(預覽):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

**指南**:使用特權存取工作站 (PAW) 與 Azure 多重身份驗證 (MFA) 設定為登入和配置啟用金鑰保管庫的資源。 

特權存取工作站:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

規劃基於雲的 Azure 多重身份驗證部署:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 從管理帳戶記錄和警報可疑活動

**指南**:當環境中發生可疑或不安全活動時,請使用 Azure 活動目錄 (AAD) 特權標識管理 (PIM) 生成日誌和警報。 使用 AAD 風險檢測查看有關風險用戶行為的警報和報告。 對於其他日誌記錄,請將 Azure 安全中心風險檢測警報發送到 Azure 監視器,並使用操作組配置自定義警報/通知。

為 Azure 金鑰保管庫啟用高級威脅保護 (ATP) 以生成可疑活動的警報。

部署 Azure AD 特權識別管理 (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

為 Azure 金鑰保管庫設定進階威脅保護(預覽):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure 金鑰保管庫的警報(預覽):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure 活動目錄風險偵測:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

在 Azure 門戶建立與管理操作群組:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

**指南**:設定條件存取策略的位置條件並管理命名位置。 使用命名位置,您可以創建 IP 位址範圍或國家和地區的邏輯分組。 您可以限制對敏感資源(如密鑰保管庫機密)的訪問,以存取配置的命名位置。

Azure 活動目錄條件訪問中的位置條件是什麼?https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="39-use-azure-active-directory"></a>3.9: 使用 Azure 的項目

**指南**:使用 Azure 活動目錄 (AAD) 作為 Azure 資源(如密鑰保管庫)的中央身份驗證和授權系統。 這允許基於角色的訪問控制 (RBAC) 管理敏感資源。

 

快速入門:在 Azure 活動目錄中建立新租戶:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期審查和協調使用者存取

**指導**:查看 Azure 活動目錄 (AAD) 日誌,以幫助發現具有 Azure 密鑰保管庫管理角色的陳舊帳戶。 此外,使用 AAD 訪問審核可有效地管理組成員身份、訪問可用於訪問 Azure 密鑰保管庫的企業應用程式以及角色分配。 應定期審查使用者訪問許可權,例如每 90 天一次,以確保只有正確的使用者才能繼續訪問。

Azure 活動目錄報告與監視文件:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

什麼是 Azure AD 存取審核?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 監視訪問已停用帳戶的嘗試

**指南**:啟用 Azure 密鑰保管庫和 Azure 活動目錄的診斷設置,將所有日誌發送到日誌分析工作區。 在日誌分析中配置所需的警報(例如嘗試存取禁用的機密)。

將 Azure AD 紀錄與 Azure 監視器紀錄整合:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

從舊的金鑰保管庫解決方案移轉:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

**指南**:使用 Azure 活動目錄的標識保護和風險檢測功能,可配置自動回應,以檢測與 Azure 密鑰保管庫保護資源相關的可疑操作。 應透過 Azure Sentinel 啟用自動回應,以實現組織的安全回應。 

Azure 活動目錄門戶中的有風險登錄報告:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

如何:設定與啟用風險政策:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 在支援方案期間向 Microsoft 提供對相關客戶資料的存取

**指導**:不適用;Azure 密鑰保管庫不支援客戶密碼箱。

支援的服務和機制在一般可用性中:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="data-protection"></a>資料保護

*有關詳細資訊,請參閱[安全控制:資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊的清單

**指南**:使用標記來協助追蹤在啟用 Azure 密鑰保管庫的資源上儲存或處理敏感資訊的 Azure 資源。 

使用標記來組織 Azure 資源:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

**指南**:您可以使用配置為限制對特定子網的訪問的虛擬網路服務終結點來保護對 Azure 密鑰保管庫的訪問。

防火牆規則生效后,僅當請求源自允許的子網或IP 位址範圍時,才能執行Azure密鑰保管庫數據平面操作。 這也適用於 Azure 門戶中的 Azure 密鑰保管庫訪問。 儘管可以從 Azure 門戶流覽到密鑰保管庫,但如果用戶端電腦不在允許清單中,則可能無法列出密鑰、機密或證書。 這也會影響 Azure 密鑰保管庫選取器和其他 Azure 服務。 如果防火牆規則阻止用戶端計算機執行此操作,則可以查看密鑰保管庫的清單,但不能查看清單密鑰。

設定 Azure 金鑰保管庫防火牆和虛擬網路:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 金鑰保管庫的虛擬網路服務終結點:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊

**指南**:存儲在 Azure 密鑰保管庫中的所有數據都被視為敏感數據。 使用 Azure 金鑰保管庫數據平面訪問控制項控制對 Azure 密鑰保管庫機密的訪問。 您還可以使用密鑰保管庫的內置防火牆來控制網路層的訪問。 要監視對 Azure 密鑰保管庫的訪問,請啟用密鑰保管庫診斷設置,並將日誌發送到 Azure 儲存帳戶或日誌分析工作區。

安全存取金鑰保管庫:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

設定 Azure 金鑰保管庫防火牆和虛擬網路:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 金鑰保管庫紀錄記錄:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

**指南**:所有到 Azure 密鑰保管庫的流量進行身份驗證、管理和數據平面訪問,都經過加密並經過 HTTPS:埠 443。 (但是,CRL 偶爾會有 HTTP [埠 80] 流量。 

在防火牆後面存取 Azure 金鑰保管庫:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 使用活動探索工具辨識敏感資料

**指導**:不適用;Azure 密鑰保管庫中的所有數據(機密、密鑰和證書)都被視為敏感數據。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: 使用 Azure RBAC 控制對資源的存取

**指南**:安全訪問 Azure 密鑰保管庫實例的管理和數據平面。

安全存取金鑰保管庫:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

**指南**:Microsoft 管理 Azure 密鑰保管庫的基礎基礎結構,並實施了嚴格的控制,以防止客戶數據丟失或暴露。

何謂 Azure Key Vault？

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

**指南**:所有託管物件(密鑰、證書和機密)在 Azure 密鑰保管庫中靜態加密。

支援文件:

- [加密模型與金鑰管理表](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

**指導**:使用 Azure 監視器中的 Azure 密鑰保管庫分析解決方案查看 Azure 密鑰保管庫審核事件日誌。

Azure 監視器中的 Azure 金鑰保管庫分析解決方案:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊,請參閱[安全控制:漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 執行自動漏洞掃描工具

**指南**:Microsoft 在支援 Azure 密鑰保管庫的基礎系統上執行漏洞管理。


**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:部署自動化作業系統修補程式管理解決方案

**指導**:不適用;Microsoft 對支援金鑰保管庫的基礎系統執行修補程式管理。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:部署自動化的第三方軟體修補程式管理解決方案

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 比較背對背漏洞掃描

**指南**:微軟對支援金鑰保管庫的基礎系統執行漏洞管理。

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**:使用 Azure 安全中心提供的預設風險評級(安全分數)。

提高 Azure 安全中心的安全分數:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊,請參閱[安全控制:庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1: 使用 Azure 資產發現

**指南**:使用 Azure 資源圖查詢和發現訂閱中的所有資源(包括 Azure 密鑰保管庫實例)。 確保租戶中具有適當的(讀取)許可權,並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

快速入門:使用 Azure 資源圖資源管理員執行第一個資源圖查詢:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

取得目前的帳戶可以存取的訂閱:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

什麼是 Azure 資源的角色型存取控制 (RBAC)？

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

**指南**:將標記應用於 Azure 密鑰保管庫資源,使元數據以邏輯方式將它們組織到分類中。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

**指南**:在適當情況下,使用標記、管理組和單獨的訂閱來組織和跟蹤 Azure 密鑰保管庫實例和相關資源。 定期協調庫存,確保及時從訂閱中刪除未經授權的資源。

建立其他 Azure 訂閱:

https://docs.microsoft.com/azure/billing/billing-create-subscription

為資源組織與管理建立管理群組:

https://docs.microsoft.com/azure/governance/management-groups/create

使用標記來組織 Azure 資源:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:維護已批准的 Azure 資源和軟體標題的清單

**指南**:為計算資源定義已批准的 Azure 資源和已批准的軟體的清單

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型

- 允許的資源類型

此外,使用 Azure 資源圖查詢/發現訂閱中的資源。

教學:建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入門:使用 Azure 資源圖資源管理員執行第一個資源圖查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中未經批准的軟體應用程式

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

**指導**:不適用;此建議適用於整個 Azure 以及計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="68-use-only-approved-applications"></a>6.8: 僅使用已批准的應用程式

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="69-use-only-approved-azure-services"></a>6.9: 僅使用已批准的 Azure 服務

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型

- 允許的資源類型

教學:建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 政策範例:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="610-implement-approved-application-list"></a>6.10: 實施已批准的申請清單

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11:限制使用者透過文稿與 AzureResource Manager 互動的能力

**指南**:使用 Azure 條件訪問通過為「Microsoft Azure 管理」應用配置「阻止存取」來限制使用者與 Azure 資源管理員 (ARM) 互動的能力。 這可以防止在高安全性環境中創建和更改資源,例如具有密鑰保管庫配置的資源。

使用條件存取管理對 Azure 管理的存取:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯上隔離高風險應用程式

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊,請參閱[安全控制:安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

**指南**:使用「Microsoft.KeyVault」命名空間中的 Azure 策略別名創建自訂策略以審核或強制執行 Azure 密鑰保管庫實例的配置。 您還可以對 Azure 金鑰保管庫使用內建 Azure 策略定義,例如:

Key Vault 物件應該要可復原

將 Key Vault 的診斷設定部署至 Log Analytics 工作區

應啟用 Key Vault 中的診斷記錄

Key Vault 應該使用虛擬網路服務端點

將 Key Vault 的診斷設定部署至事件中樞

使用 Azure 安全中心的建議作為 Azure 密鑰保管庫實例的安全配置基線。

如何檢視可用的 Azure 政策別名:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教學:建立與管理原則以強制實施合規性:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 建立安全的作業系統設定

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:維護安全的 Azure 資源配置

**指導**:使用 Azure 策略 [拒絕] 和 [部署(如果不存在))在啟用 Azure 密鑰保管庫的資源中強制實施安全設置。 

教學:建立與管理原則以強制實施合規性:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
瞭解 Azure 政策效果: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 維護安全的作業系統設定

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源的設定

**指南**:如果對啟用 Azure 密鑰保管庫的資源使用自定義 Azure 策略定義,請使用 Azure 儲存庫安全地存儲和管理代碼。

如何在 Azure DevOps 中儲存代碼: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure 儲存函式庫文件: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 部署系統設定管理工具

**指南**:使用「Microsoft.KeyVault」命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統配置。 此外,開發用於管理策略異常的流程和管道。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:為作業系統部署系統設定管理工具

**指導**:不適用;此建議用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:為 Azure 服務實現自動化配置監視

**指南**:使用 Azure 安全中心對 Azure 金鑰保管庫保護的資源執行基線掃描 

  

如何修復 Azure 安全中心中的建議: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 對作業系統執行自動設定監控

**指導**:不適用;此基準測試用於計算資源。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11: 安全管理 Azure 機密

**指南**:將託管服務標識與 Azure 密鑰保管庫結合使用,以簡化和保護雲應用程式的秘密管理。 確保啟用 Azure 金鑰保管庫軟刪除。

如何與 Azure 託管識別整合:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立金鑰保存庫:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管識別提供金鑰保管庫身份驗證: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 安全、自動地管理身份

**指南**:將託管服務標識與 Azure 密鑰保管庫結合使用,以簡化和保護雲應用程式的秘密管理。 

  

如何與 Azure 託管識別整合: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

如何建立金鑰保存庫: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

如何使用託管識別提供金鑰保管庫身份驗證:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**指導**:不適用;此建議用於計算資源。 Microsoft 處理底層平臺的反惡意軟體。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:要上傳到非計算 Azure 資源的預入檔案

**指南**:在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,Azure 密鑰保管庫),但是,它不在客戶內容上運行。

預掃描上載或發送到非計算 Azure 資源(如 Azure 密鑰保管庫)的任何內容。 在這種情況下,Microsoft 無法訪問您的數據。

瞭解適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意軟體:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 確保更新反惡意軟體和簽名

**指導**:不適用;此建議用於計算資源。 Microsoft 處理底層平臺的反惡意軟體。


**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊,請參閱[安全控制:資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

**指南**:使用以下 PowerShell 命令,確保定期自動備份金鑰保管庫證書、密鑰、託管儲存帳戶和機密:

- 備份-AzKeyVault 憑證

- 備份-阿茲基庫鍵

- 備份-AzKeyVault 託管儲存帳戶

- 備份-阿茲基庫特秘密

或者,您可以將密鑰保管庫備份存儲在 Azure 備份中。

如何備份金鑰保管庫證書:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份金鑰保存庫金鑰:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份金鑰保管庫託管儲存帳戶:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份金鑰保管庫機密:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何開啟 Azure 備份:https://docs.microsoft.com/azure/backup



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

**指南**: 使用以下 PowerShell 命令執行金鑰保管庫證書、金鑰、託管儲存帳戶和機密的備份:

- 備份-AzKeyVault 憑證

- 備份-阿茲基庫鍵

- 備份-AzKeyVault 託管儲存帳戶

- 備份-阿茲基庫特秘密

或者,您可以將密鑰保管庫備份存儲在 Azure 備份中。

如何備份金鑰保管庫證書:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

如何備份金鑰保存庫金鑰:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

如何備份金鑰保管庫託管儲存帳戶:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

如何備份金鑰保管庫機密:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

如何開啟 Azure 備份:https://docs.microsoft.com/azure/backup



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

**指南**: 定期使用以下 PowerShell 命令對金鑰保管庫證書、金鑰、託管儲存帳戶和機密執行資料回復:

- 回復-AzKeyVault 憑證

- 恢復-阿茲基庫鍵

- 回復 -AzKeyVault 管理儲存帳戶

- 恢復-阿茲基庫秘密

如何還原金鑰保管庫證書:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

如何回復金鑰保存庫金鑰:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

如何還原金鑰保管庫託管儲存帳戶:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

如何復原金鑰保存庫機密:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

**指南**:確保為 Azure 密鑰保管庫啟用軟刪除。 軟刪除允許恢復已刪除的密鑰保管庫和保管庫物件,如密鑰、機密和證書。 

如何使用 Azure 金鑰保存庫的軟刪除: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊,請參閱[安全控制:事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1:建立事件回應指南

**指導**:為您的組織構建事件回應指南。 確保有書面事件回應計劃,定義人員的所有角色以及事件處理/管理階段,從檢測到事件後審查。 這些進程應側重於保護敏感系統,例如使用密鑰保管庫機密的系統。

如何在 Azure 安全中心內配置工作流自動化: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

有關構建您自己的安全事件回應流程的指導:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微軟安全回應中心的事件剖析:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

客戶還可以利用 NIST 的電腦安全事件處理指南來幫助創建他們自己的事件回應計劃: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 建立事件評分與優先順序處理程序

**指南**:安全中心為每個警報分配嚴重性,以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。 此外,清楚地標記訂閱(對於前 生產,非 prod),並創建命名系統以明確識別和分類 Azure 資源,特別是那些處理敏感數據(如 Azure 金鑰保管庫機密)的資源。


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

**指南**:執行練習,以常規節奏測試系統的事件回應功能,以幫助保護 Azure 密鑰保管庫實例和相關資源。 找出薄弱環節和差距,並根據需要修訂計劃。

請參閱 NIST 出版物:IT 計劃和功能測試、培訓和鍛煉計劃指南: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:提供安全事件聯繫人詳細資訊,並配置安全事件的警報通知

**指南**:如果 Microsoft 安全回應中心 (MSRC) 發現您的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。  事後查看事件,以確保問題得到解決。

如何設置 Azure 安全中心安全連絡人:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

**指南**:使用"連續匯出"功能匯出 Azure 安全中心警報和建議,以幫助識別啟用 Azure 密鑰庫的資源的風險。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。  您可以使用 Azure 安全中心數據連接器將警報流式傳輸到 Azure 哨兵。 

 

如何設定連續匯出: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

如何將警報流式傳輸到 Azure 哨兵: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

**指南**:使用 Azure 安全中心的工作流自動化功能通過安全警報和建議上的「邏輯應用」自動觸發回應,以保護受 Azure 密鑰保管庫保護的資源。 

 

如何設定工作流自動化和邏輯應用: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊,請參閱[安全控制:滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:對 Azure 資源進行定期滲透測試,並確保在 60 天內補救所有關鍵安全發現

**指南**:您不應直接對 Azure 密鑰保管庫服務執行筆測試,但建議測試使用密鑰保管庫以確保機密安全的 Azure 資源。

您需要遵守 Microsoft 參與規則,以確保您的滲透測試不違反 Microsoft 策略:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲端基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的策略和執行的詳細資訊,如下所示: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**:不適用

**責任**: 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
