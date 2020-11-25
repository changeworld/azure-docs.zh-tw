---
title: ISO 27001 藍圖範例控制項
description: ISO 27001 藍圖範例的控制項對應。 每個控制措施都會對應至一或多個可協助評量的 Azure 原則定義。
ms.date: 11/05/2020
ms.topic: sample
ms.openlocfilehash: 0c1c20ae8b740ce019441dbee880f883947af160
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842493"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>ISO 27001 藍圖範例的控制項對應

下列文章將詳細說明 Azure 藍圖 ISO 27001 藍圖範例與 ISO 27001 控制項的對應情形。 如需控制項的詳細資訊，請參閱 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

以下是與 **ISO 27001:2013** 控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後，找出並選取 **\[預覽\] 稽核 ISO 27001:2013 控制項並部署特定的 VM 延伸模組，以支援稽核需求** 內建原則方案。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是一對一或完整對應。 因此，Azure 原則中的 **符合規範** 只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md) \(英文\)。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 權責區分

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義，可稽核 Azure 訂用帳戶的擁有者數目，藉以協助您維護適當數量的 Azure 訂用帳戶擁有者。 管理訂用帳戶擁有者權限可協助您實作適當的權責區分。

- 應針對您的訂用帳戶指定最多 3 位擁有者
- 應將一個以上的擁有者指派給您的訂用帳戶

## <a name="a821-classification-of-information"></a>A.8.2.1 資訊分類

Azure 的 [SQL 弱點評量服務](../../../../azure-sql/database/sql-vulnerability-assessment.md)可協助您探索資料庫中所儲存的敏感性資料，並納入該資料的分類建議。 此藍圖會指派 [Azure 原則](../../../policy/overview.md)定義，以稽核在修復 SQL 弱點評量掃描期間所識別出的弱點。

- 應修復 SQL 資料庫的弱點

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 存取網路和網路服務

Azure 會實作 [Azure 角色型存取控制 (Azure RBAC)](../../../../role-based-access-control/overview.md)，以管理有權存取 Azure 資源的人員。 此藍圖指派了七項 [Azure 原則](../../../policy/overview.md)定義以協助您控制對 Azure 資源的存取。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。
了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- 部署必要條件，以稽核有不具密碼帳戶的 Linux VM
- 部署必要條件以稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- 從帳戶沒有密碼的 Linux VM 顯示稽核結果
- 從允許不使用密碼從帳戶遠端連線的 Linux VM 顯示稽核結果
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源
- 虛擬機器應移轉到新的 Azure Resource Manager 資源
- 稽核不是使用受控磁碟的 VM

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特殊權限的存取權

此藍圖指派了四項 [Azure 原則](../../../policy/overview.md)定義，用以稽核具有擁有者及/或寫入權限的外部帳戶，和具有擁有者及/或寫入權限、且未啟用多重要素驗證的帳戶，以協助您限制及控制特殊權限的存取權。 Azure 角色型存取控制 (Azure RBAC) 可協助管理有權存取 Azure 資源的人員。 此藍圖還指派了三項 Azure 原則定義，用以稽核將 Azure Active Directory 驗證用於 SQL Server 和 Service Fabric 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。 此藍圖也指派了一項 Azure 原則定義來稽核自訂 Azure RBAC 規則的使用情形。 了解自訂 Azure RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 Azure RBAC 規則很容易發生錯誤。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證
- 稽核自訂 RBAC 規則的使用方式

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 管理使用者的秘密驗證資訊

此藍圖指派了三項 [Azure 原則](../../../policy/overview.md)定義來稽核未啟用多重要素驗證的帳戶。 多重要素驗證有助於確保帳戶即使有部分驗證資訊遭到入侵，仍然安全無虞。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。 此藍圖也指派了兩項 Azure 原則定義來稽核 Linux VM 密碼檔案權限，以在其設定不正確時發出警示。 此設定可讓您採取更正措施，以確保驗證器不會遭到入侵。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 從密碼檔權限未設為 0644 的 Linux VM 顯示稽核結果

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 檢閱使用者存取權限

[Azure 角色型存取控制 (Azure RBAC)](../../../../role-based-access-control/overview.md) 可協助您管理有權存取 Azure 中資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了四項 [Azure 原則](../../../policy/overview.md)定義來稽核應優先檢閱的帳戶，包括已停用的帳戶和已提高權限的外部帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 移除或調整存取權限

[Azure 角色型存取控制 (Azure RBAC)](../../../../role-based-access-control/overview.md) 可協助您管理有權存取 Azure 中資源的人員。 使用 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 和 Azure RBAC，可更新使用者角色以反映組織變更。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義來稽核應考慮移除的停用帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 安全登入程序

此藍圖指派了三項 Azure 原則定義來稽核未啟用多重要素驗證的帳戶。 Azure AD Multi-Factor Authentication 會藉由要求第二種形式的驗證來提供額外的安全性，並提供增強式驗證。 藉由監視未啟用多重要素驗證的帳戶，您將可識別較可能遭到入侵的帳戶。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA

## <a name="a943-password-management-system"></a>A.9.4.3 密碼管理系統

此藍圖指派了 10 項 [Azure 原則](../../../policy/overview.md)定義，用以稽核未強制執行最低強度和其他密碼需求的 Windows VM，以協助您強制執行強式密碼。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- 從未啟用密碼複雜度設定的 Windows VM 顯示稽核結果
- 從最大密碼存留期不是 70 天的 Windows VM 顯示稽核結果
- 從最小密碼存留期不是 1 天的 Windows VM 顯示稽核結果
- 從最小密碼長度未限制為 14 個字元的 Windows VM 顯示稽核結果
- 從允許重複使用前 24 個舊密碼的 Windows VM 顯示稽核結果

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 使用密碼編譯控制項的原則

此藍圖指派了 13 項 [Azure 原則](../../../policy/overview.md)定義，據以強制執行特定的密碼編譯控制項及稽核弱式密碼編譯設定的使用，以協助您強制執行密碼編譯控制項的使用原則。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則會要求 Blob 儲存體帳戶和 Data Lake Storage 帳戶必須加密；SQL 資料庫必須進行透明資料加密；稽核儲存體帳戶、SQL 資料庫、虛擬機器磁碟和自動化帳戶變數是否缺少加密；稽核儲存體帳戶、函式應用程式、Web 應用程式、API 應用程式和 Redis 快取的連線是否不安全；稽核弱式虛擬機器密碼加密；稽核未加密的 Service Fabric 通訊。

- 函式應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取
- 從未使用可逆加密來儲存密碼的 Windows VM 顯示結果
- 應在虛擬機器上套用磁碟加密
- 應加密自動化帳戶變數
- 只允許對您 Azure Cache for Redis 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應在 SQL 資料庫上啟用透明資料加密

## <a name="a1241-event-logging"></a>A.12.4.1 事件記錄

此藍圖指派了七項 [Azure 原則](../../../policy/overview.md)定義來稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。
診斷記錄能讓您了解 Azure 資源內所執行的作業。

- 稽核 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核診斷設定
- 應啟用 SQL 伺服器上的稽核

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 系統管理員與操作員的記錄

此藍圖指派了七項 Azure 原則定義來稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 診斷記錄能讓您了解 Azure 資源內所執行的作業。

- 稽核 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核診斷設定
- 應啟用 SQL 伺服器上的稽核

## <a name="a1244-clock-synchronization"></a>A.12.4.4 時鐘同步處理

此藍圖指派了七項 Azure 原則定義來稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 Azure 記錄會依賴已同步處理的內部時鐘，來對所有資源內的事件建立與時間相互關聯的記錄。

- 稽核 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Dependency Agent 部署 - 未列出的 VM 映像 (OS)
- [預覽]：稽核記錄分析代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核虛擬機器擴展集中的 Log Analytics 代理程式部署 - 未列出的 VM 映像 (OS)
- 稽核診斷設定
- 應啟用 SQL 伺服器上的稽核

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 在作業系統上安裝軟體

自適性應用程式控制是 Azure 資訊安全中心提供的解決方案，可協助您控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 此藍圖指派了一項 Azure 原則定義，用以監視允許的應用程式集所進行的變更。 這項功能可協助您控制 Azure VM 上軟體和應用程式的安裝。

- 您的電腦應啟用自適性應用程式控制，以定義安全應用程式

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 管理技術弱點

此藍圖指派了五項 [Azure 原則](../../../policy/overview.md)定義，用以監視 Azure 資訊安全中心內的遺漏系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，藉此來協助您管理資訊系統弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 您應在機器上安裝系統更新
- 您應在機器上修復安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 軟體安裝的限制

自適性應用程式控制是 Azure 資訊安全中心提供的解決方案，可協助您控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 此藍圖指派了一項 Azure 原則定義，用以監視允許的應用程式集所進行的變更。 軟體安裝的限制有助於降低引入軟體弱點的可能性。

- 您的電腦應啟用自適性應用程式控制，以定義安全應用程式

## <a name="a1311-network-controls"></a>A.13.1.1 網路控制

此藍圖指派了一項 [Azure 原則](../../../policy/overview.md)定義來監視使用寬鬆規則的網路安全性群組，以協助您管理和控制網路。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。 此藍圖還指派了三項 Azure 原則定義，以監視未受保護的端點、應用程式和儲存體帳戶。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- 應限制透過網際網路面向端點存取
- 儲存體帳戶應限制網路存取

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 資訊傳輸原則和程序

此藍圖指派了兩項 [Azure 原則](../../../policy/overview.md)定義來稽核不安全的儲存體帳戶和 Redis 快取連線，以協助您確保 Azure 服務的資訊傳輸安全無虞。

- 只允許對您 Azure Cache for Redis 的安全連線
- 應啟用儲存體帳戶的安全傳輸

## <a name="next-steps"></a>後續步驟

您已檢閱過 ISO 27001 藍圖的控制項對應，接下來請瀏覽下列文章以了解架構及如何部署此範例：

> [!div class="nextstepaction"]
> [ISO 27001 藍圖 - 概觀](./index.md)
> [ISO 27001 藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
