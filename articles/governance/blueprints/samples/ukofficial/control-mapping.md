---
title: UK OFFICIAL 與 UK NHS 藍圖範例控制項
description: UK OFFICIAL 與 UK NHS 藍圖範例的控制項對應。 每個控制項都會對應至一或多個可協助評量的 Azure 原則。
ms.date: 05/08/2020
ms.topic: sample
ms.openlocfilehash: 88f9606df5c3dcbca6ade05be918e3500a6ba64c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005604"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>UK OFFICIAL 與 UK NHS 藍圖範例的控制項對應

下列文章詳細說明 UK OFFICIAL 和 UK NHS 藍圖範例如何對應至 UK OFFICIAL 和 UK NHS 控制項。 如需控制項的詳細資訊，請參閱 [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications)。

以下是 **UK OFFICIAL** 和 **UK NHS** 控制項的對應。 使用右側的導覽區可直接跳到特定的控制項對應。 許多對應的控制項都是以 [Azure 原則](../../../policy/overview.md)方案進行實作的。 若要檢閱完整方案，請在 Azure 入口網站中開啟 [原則]  ，然後選取 [定義]  頁面。 然後，找出並選取 **\[預覽\] 稽核 UK OFFICIAL 和 UK NHS 控制項並部署特定的 VM 擴充功能，以支援稽核需求**內建原則計畫。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../../../policy/overview.md)定義相關聯。 這些原則可協助您使用工具[存取合規性](../../../policy/how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是 1：1 或完整對應。 因此，Azure 原則中的**符合規範**只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性藍圖範例的控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md) \(英文\)。

## <a name="1-data-in-transit-protection"></a>1 傳輸中的資料保護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以稽核不安全的儲存體帳戶和 Redis 快取連線，接此協助您確保 Azure 服務的資訊傳輸安全無虞。

- 應該只允許對 Redis Cache 的安全連線
- 應啟用儲存體帳戶的安全傳輸
- 從未使用安全通訊協定的 Windows 網頁伺服器顯示稽核結果
- 部署必要條件以稽核未使用安全通訊協定的 Windows 網頁伺服器

## <a name="23-data-at-rest-protection"></a>2.3 待用資料保護

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以強制執行特定的密碼編譯控制項及稽核弱式密碼編譯設定的使用，藉此協助您強制執行密碼編譯控制項的使用原則。
了解您的 Azure 資源在哪些層面可能使用非最佳化的密碼編譯組態，有助於您採取更正措施，以確保資源會根據您的資訊安全性原則進行設定。 具體而言，此藍圖所指派的原則會要求 Data Lake Storage 帳戶進行加密；SQL 資料庫必須進行透明資料加密；稽核儲存體帳戶、SQL 資料庫、虛擬機器磁碟和自動化帳戶變數是否缺少加密；稽核儲存體帳戶和 Redis 快取的連線是否不安全；稽核弱式虛擬機器密碼加密；稽核未加密的 Service Fabric 通訊。

- 應在虛擬機器上套用磁碟加密
- 應加密自動化帳戶變數
- Service Fabric 叢集應將 ClusterProtectionLevel 屬性設定為 EncryptAndSign
- 應在 SQL 資料庫上啟用透明資料加密
- 部署 SQL DB 透明資料加密
- Data Lake Store 帳戶需要加密
- 允許的位置 (已硬式編碼為 "UK SOUTH" 和 "UK WEST")
- 資源群組允許的位置 (已硬式編碼為 "UK SOUTH" 和 "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 弱點管理

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以監視遺漏的端點保護、遺漏的系統更新、作業系統弱點、SQL 弱點和虛擬機器弱點，以協助您管理資訊系統弱點。 這些深入解析可讓您即時檢視已部署資源的安全性狀態，並且可協助您排定補救措施的優先順序。

- 在 Azure 資訊安全中心中監視缺少的 Endpoint Protection
- 您應在機器上安裝系統更新
- 應在虛擬機器擴展集上安裝系統更新
- 您應在機器上修復安全性組態的弱點
- 應修復 SQL 資料庫的弱點
- 弱點評量解決方案應修復弱點
- 弱點評定應於您的 SQL 伺服器上啟用
- SQL 受控執行個體上應啟用弱點評定
- 應修復虛擬機器擴展集上安全性組態的弱點
- 應在 SQL 受控執行個體上啟用進階資料安全性
- 應在 SQL 伺服器上啟用進階資料安全性

## <a name="53-protective-monitoring"></a>5.3 防護監視

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以提供不受限存取、允許清單活動和威脅的防護監視，藉此協助您保護資訊系統資產。

- 稽核不受限制的儲存體帳戶網路存取
- 應在虛擬機器上啟用自適性應用程式控制
- 稽核未設定災害復原的虛擬機器
- 應啟用 DDoS 保護標準
- 在 SQL 受控執行個體的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 伺服器的進階資料安全性設定中，進階威脅防護類型應設定為 [全部]
- 在 SQL 伺服器上部署威脅偵測
- 為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組

## <a name="9-secure-user-management"></a>9 安全的使用者管理 

Azure 會實作角色型存取控制 (RBAC)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以稽核具有擁有者及/或讀取/寫入權限的外部帳戶，及具有擁有者、讀取及/或寫入權限且未啟用多重要素驗證的帳戶，藉此協助您限制及控制特殊權限的存取權。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除

## <a name="10-identity-and-authentication"></a>10 身分識別和驗證

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，以稽核具有擁有者及/或讀取/寫入權限的外部帳戶，及具有擁有者、讀取及/或寫入權限且未啟用多重要素驗證的帳戶，藉此協助您限制及控制特殊權限的存取權。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除

此藍圖指派了 Azure 原則定義，以稽核將 Azure Active Directory 驗證用於 SQL Server 和 Service Fabric 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證

此藍圖也指派了 Azure 原則定義來稽核應優先檢閱的帳戶，包括已停用的帳戶和外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派了兩項 Azure 原則定義來稽核應考慮移除的停用帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖也指派了 Azure 原則定義來稽核 Linux VM 密碼檔案權限，以在其設定不正確時發出警示。 此設計可讓您採取更正措施，以確保驗證器不會遭到入侵。

- \[預覽\]：從密碼檔權限未設為 0644 的 Linux VM 顯示稽核結果

此藍圖指派了 Azure 原則定義，用以稽核未強制執行最低強度和其他密碼需求的 Windows VM，以協助您強制執行強式密碼。 確知有哪些 VM 違反密碼強度原則可協助您採取更正措施，以確保所有 VM 使用者帳戶的密碼均符合原則。

- \[預覽\]：部署必要條件以稽核未啟用密碼複雜度設定的 Windows VM
- \[預覽\]：部署必要條件以稽核最大密碼存留期不是 70 天的 Windows VM
- \[預覽\]：部署必要條件以稽核最小密碼存留期不是 1 天的 Windows VM
- \[預覽\]：部署必要條件以稽核最小密碼長度未限制為 14 個字元的 Windows VM
- \[預覽\]：部署必要條件以稽核允許重複使用前 24 個舊密碼的 Windows VM
- \[預覽\]：從未啟用密碼複雜度設定的 Windows VM 顯示稽核結果
- \[預覽\]：從最大密碼存留期不是 70 天的 Windows VM 顯示稽核結果
- \[預覽\]：從最小密碼存留期不是 1 天的 Windows VM 顯示稽核結果
- \[預覽\]：從最小密碼長度未限制為 14 個字元的 Windows VM 顯示稽核結果
- \[預覽\]：從允許重複使用前 24 個舊密碼的 Windows VM 顯示稽核結果

此藍圖也指派了 Azure 原則定義來協助您控制對 Azure 資源的存取。 這些原則會稽核資源類型和組態的使用方式是否可能會允許更寬鬆資源存取。 了解有哪些資源違反這些原則可協助您採取修正動作，以確保 Azure 資源僅限定給經授權的使用者存取。

- \[預覽\]：部署需求以稽核有不具密碼帳戶的 Linux VM
- \[預覽\]：部署需求以稽核允許不使用密碼從帳戶遠端連線的 Linux VM
- \[預覽\]：從帳戶沒有密碼的 Linux VM 顯示稽核結果
- \[預覽\]：從允許不使用密碼從帳戶遠端連線的 Linux VM 顯示稽核結果
- 儲存體帳戶應移轉至新的 Azure Resource Manager 資源
- 虛擬機器應移轉到新的 Azure Resource Manager 資源
- 稽核不是使用受控磁碟的 VM

## <a name="11-external-interface-protection"></a>11 外部介面保護

除了使用超過 25 個原則來進行適當的安全使用者管理以外，此藍圖也指派了 [Azure 原則](../../../policy/overview.md)定義以監視不受限的儲存體帳戶，藉此協助您保護服務介面免於遭受未經授權的存取。 存取不受限制的儲存體帳戶可允許資訊系統內所包含的資訊受到非預期的存取。 此藍圖也指派一個原則，以在虛擬機器上啟用自適性應用程式控制。

- 稽核不受限制的儲存體帳戶網路存取
- 應在虛擬機器上啟用自適性應用程式控制
- 應限制透過網際網路面向端點存取
- 應在網際網路對應虛擬機器中套用自適性網路強化建議
- 應在虛擬機器擴展集上安裝端點保護解決方案
- Just-In-Time 網路存取控制應套用在虛擬機器上
- 函式應用程式的遠端偵錯應關閉
- Web 應用程式的遠端偵錯應關閉
- 應關閉 API 應用程式的遠端偵錯
- Web 應用程式應只可經由 HTTPS 存取
- 函式應用程式應只可經由 HTTPS 存取
- API 應用程式應只可經由 HTTPS 存取

## <a name="12-secure-service-administration"></a>12 安全的服務管理

Azure 會實作角色型存取控制 (RBAC)，以協助您管理有權存取 Azure 資源的人員。 您可以使用 Azure 入口網站，檢閱可存取 Azure 資源的人員及其權限。 此藍圖指派了五項 [Azure 原則](../../../policy/overview.md)定義，以稽核具有擁有者及/或寫入權限的外部帳戶，及具有擁有者及/或寫入權限且未啟用多重要素驗證的帳戶，藉此協助您限制及控制特殊權限的存取權。

用於雲端服務管理的系統將會有該服務的高度存取權限。 其洩露會有重大影響，包括規避安全性控制及竊取或操控大量資料。 服務提供者的系統管理員用來管理操作服務的方法，其設計訴求應該是降低任何可能破壞服務安全性的攻擊風險。 如果未實作此準則，攻擊者可能有辦法規避安全性控制及竊取或操控大量資料。

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖指派了 Azure 原則定義，以稽核將 Azure Active Directory 驗證用於 SQL Server 和 Service Fabric 的情形。 使用 Azure Active Directory 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。

- 應針對 SQL 伺服器佈建 Azure Active Directory 管理員
- Service Fabric 叢集應只能使用 Azure Active Directory 進行用戶端驗證

此藍圖也指派了 Azure 原則定義來稽核應優先檢閱的帳戶，包括已停用的帳戶和已提高權限的外部帳戶。 必要時可以封鎖帳戶的登入 (或移除帳戶)，而立即移除對 Azure 資源的存取權限。 此藍圖指派了兩項 Azure 原則定義來稽核應考慮移除的停用帳戶。

- 已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除
- 應從訂用帳戶移除具有寫入權限的外部帳戶

此藍圖也指派了 Azure 原則定義來稽核 Linux VM 密碼檔案權限，以在其設定不正確時發出警示。 此設計可讓您採取更正措施，以確保驗證器不會遭到入侵。

- \[預覽\]：從密碼檔權限未設為 0644 的 Linux VM 顯示稽核結果

## <a name="13-audit-information-for-users"></a>13 稽核使用者的資訊

此藍圖指派了 [Azure 原則](../../../policy/overview.md)定義，稽核 Azure 資源的記錄設定，以協助您確保會記錄系統事件。 指派的原則也會稽核虛擬機器是否未將記錄傳送至指定的記錄分析工作區。

- 應在 SQL 伺服器上啟用進階資料安全性
- 稽核診斷設定
- \[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式
- \[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式
- 於虛擬網路建立時部署網路監看員

## <a name="next-steps"></a>後續步驟

您已檢閱過 UK OFFICIAL 和 UK NHS 藍圖的控制項對應，請瀏覽下列文章以了解概觀及如何部署此範例：

> [!div class="nextstepaction"]
> [UK OFFICIAL 與 UK NHS 藍圖 - 概觀](./index.md)
> [UK OFFICIAL 與 UK NHS 藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
