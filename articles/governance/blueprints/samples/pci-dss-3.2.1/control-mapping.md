---
title: PCI-DSS v3.2.1 藍圖範例控制項
description: 支付卡產業資料安全性標準 v3.2.1 藍圖範例與 Azure 原則和 Azure RBAC 的控制項對應。
ms.date: 08/19/2020
ms.topic: sample
ms.openlocfilehash: 3d7bdd62dcc5b65b0978444e74013d289f03ed6a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541640"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI-DSS v3.2.1 藍圖範例的控制項對應

下列文章將詳細說明 Azure 藍圖 PCI-DSS v3.2.1 藍圖範例與 PCI-DSS v3.2.1 控制項的對應情形。 如需控制項的詳細資訊，請參閱 [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)。

以下是 **PCI-DSS v3.2.1:2018** 控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。 然後，找出並選取 **\[預覽\] 稽核 PCI v3.2.1:2018 控制項並部署特定的 VM 擴充功能，以支援稽核需求**內建原則計畫。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md) \(英文\)。

## <a name="132-and-134-boundary-protection"></a>1.3.2 和 1.3.4 界限保護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以監視使用寬鬆規則的網路安全性群組，藉此協助您管理和控制網路。 太寬鬆的規則可能會允許非預期的網路存取，您應加以檢閱。 此藍圖可指派一項 Azure 原則定義，以監視未受保護的端點、應用程式和儲存體帳戶。 不受防火牆保護的端點和應用程式，以及存取不受限制的儲存體帳戶，都可能允許資訊系統內所包含的資訊受到非預期的存取。

- 稽核不受限制的儲存體帳戶網路存取
- 應限制透過網際網路面向端點存取

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a、4.1、4.1.g、4.1.h 和 6.5.3 密碼編譯保護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以強制執行特定的密碼編譯控制項及稽核弱式密碼編譯設定的使用，藉此協助您強制執行密碼編譯控制項的使用原則。 了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則定義會要求在 SQL 資料庫上進行透明資料加密，以及稽核儲存體帳戶和自動化帳戶變體是否缺少加密。 另外還有一些原則，可稽核與儲存體帳戶、Function Apps、WebApp、API Apps 和 Redis 快取之間的不安全連線，以及稽核未加密的 Service Fabric 通訊。

- 函式應用程式應只可經由 HTTPS 存取
- Web 應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取
- 應在 SQL 資料庫上啟用透明資料加密
- 應在虛擬機器上套用磁碟加密
- 應加密自動化帳戶變數
- 應該只允許對 Redis Cache 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應在 SQL 資料庫上啟用透明資料加密
- 部署 SQL DB 透明資料加密

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、6.2、6.6 和 11.2.1 弱點掃描和系統更新

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以監視 Azure 資訊安全中心內的遺漏系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，藉此協助您管理資訊系統弱點。 Azure 資訊安全中心提供報告功能，可讓您即時深入檢視已部署 Azure 資源的安全性狀態。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組
- 在 SQL Server 上部署威脅偵測
- 您應在機器上安裝系統更新
- 您應在機器上修復安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1。 7.1.2 和 7.1.3 職責區分

只有一個 Azure 訂用帳戶擁有者時，無法建立系統管理備援。 相反地，若有過多 Azure 訂用帳戶擁有者，因擁有者帳戶遭到入侵而產生缺口的可能性就會增加。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以稽核 Azure 訂用帳戶的擁有者數目，藉此協助您維護適當數量的 Azure 訂用帳戶擁有者。 管理訂用帳戶擁有者權限可協助您實作適當的權責區分。

- 應將一個以上的擁有者指派給您的訂用帳戶
- 應針對您的訂用帳戶指定最多 3 位擁有者 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、7.2.1、8.3.1.a 和 8.3.1.b 管理特殊權限的存取權

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，用以稽核具有擁有者、寫入及/或讀取權限的外部帳戶，和具有擁有者及/或寫入權限且未啟用多重要素驗證的員工帳戶，以協助您限制及控制特殊權限的存取權。 Azure 角色型存取控制 (Azure RBAC) 可協助管理有權存取 Azure 資源的人員。 了解自訂 Azure RBAC 規則的實作之處，有助於您確認需求和適當的實作，因為自訂的 Azure RBAC 規則很容易發生錯誤。 此藍圖還指派了 [Azure 原則](../../../policy/overview.md)定義，用以稽核將 Azure Active Directory 驗證用於 SQL Server 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別  
。
 
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- 稽核自訂 RBAC 規則的使用方式

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 和 8.1.5 最低權限和檢閱使用者存取權限

Azure 角色型存取控制 (Azure RBAC) 可協助您管理有權存取 Azure 中資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義來稽核應優先檢閱的帳戶，包括已停用的帳戶和已提高權限的外部帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 移除或調整存取權限

Azure 角色型存取控制 (Azure RBAC) 可協助您管理有權存取 Azure 中資源的人員。 使用 Azure Active Directory 和 Azure RBAC，可更新使用者角色以反映組織變更。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義來稽核應考慮移除的停用帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b、8.2.4.a,b 和 8.2.5 密碼式驗證

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，用以稽核未強制執行最低強度和其他密碼需求的 Windows VM，以協助您強制執行強式密碼。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- \[預覽\]：稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：部署需求以稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：稽核最小密碼長度未限制為 14 個字元的 Windows 虛擬機器
- \[預覽\]：部署需求以稽核最小密碼長度未限制為 14 個字元的 Windows VM
- \[預覽\]：稽核允許重複使用前 24 個舊密碼的 Windows 虛擬機器
- \[預覽\]：部署需求以稽核允許重複使用前 24 個舊密碼的 Windows VM

## <a name="103-and-1054-audit-generation"></a>10.3 和 10.5.4 稽核產生

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。
診斷記錄能讓您了解 Azure 資源內所執行的作業。 Azure 記錄會依賴已同步處理的內部時鐘，來對所有資源內的事件建立與時間相互關聯的記錄。

- 應該在 SQL Server 上的進階資料安全性設定上啟用稽核
- 稽核診斷設定
- 稽核 SQL 伺服器層級稽核設定
- 在 SQL 伺服器上部署稽核
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源
- 虛擬機器應移轉到新的 Azure Resource Manager 資源

## <a name="1236-and-1237-information-security"></a>12.3.6 和12.3.7 資訊安全性

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以稽核可接受的網路位置及該環境允許的已核准公司產品，藉此協助您管理和控制您的網路。 每家公司都可以透過上述每個原則中的原則參數來自訂這些定義。

- 允許的位置
- 允許資源群組的位置

## <a name="next-steps"></a>後續步驟

您已檢閱過 PCI-DSS v3.2.1 藍圖的控制項對應，請瀏覽下列文章以了解概觀及如何部署此範例：

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 藍圖 - 概觀](./index.md)
> [PCI-DSS v3.2.1 藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。