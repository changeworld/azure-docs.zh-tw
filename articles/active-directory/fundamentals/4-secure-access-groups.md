---
title: 使用 Azure Active Directory 和 Microsoft 365 的群組保護外部存取
description: 當外部使用者存取您的資源時，Azure Active Directory 和 Microsoft 365 群組可以用來提高安全性。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83e5584f8f9c6823e1259cb5e6034d8b13ae3a6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222372"
---
# <a name="securing-external-access-with-groups"></a>使用群組保護外部存取 

群組是任何存取控制策略不可或缺的一部分。 Azure Active Directory (Azure AD) 安全性群組和 Microsoft 365 (M365) 群組可作為保護資源存取的基礎。

群組是用來作為下列存取控制機制基礎的最佳選項：

* 條件式存取原則

* 權利管理存取套件 

* M365 資源、Microsoft 小組和 SharePoint 網站的存取權

群組具有下列角色：

* 擁有者–群組擁有者會管理群組設定及其成員資格。

* 成員–繼承指派給群組之許可權和存取權的成員。

* 來賓-來賓是您組織外部的成員。 

## <a name="determine-your-group-strategy"></a>判斷您的群群組原則

當您開發群群組原則來保護對資源的外部存取時，請考慮 [您想](1-secure-access-posture.md) 要的安全性狀態，以判斷下列選項。

* **誰可以建立群組？** 您只想要系統管理員建立群組，還是希望員工和或外部使用者也建立這些群組。

   * *依預設，任何租使用者成員都可以建立 Azure AD 安全性群組*。 

      * 您可以 [限制非系統管理員的入口網站存取](../develop/howto-restrict-your-app-to-a-set-of-users.md) ，並停用 PowerShell 中的群組建立功能 [。](../users-groups-roles/groups-troubleshooting.md) 

      * 您也可以 [在 Azure Active Directory 中設定自助式群組管理](../users-groups-roles/groups-self-service-management.md)。 

   * *依預設，所有使用者都可以針對您租使用者中的所有 (內部和外部) 使用者來建立 M365 群組和群組*。 

      * [您可以限制](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) 特定安全性群組的成員建立 Microsoft 365 群組。 使用 Windows PowerShell 來設定此設定。 

* **誰可以邀請人員加入群組？** 所有群組成員都可以新增其他成員，還是只能群組擁有者新增成員？

* **誰可以受邀加入群組？** 依預設，您可以將外部使用者新增至群組。 

### <a name="assign-users-to-groups"></a>將使用者指派給群組

您可以根據使用者物件中的使用者屬性或其他準則，以手動方式將使用者指派給群組。 使用者只能根據其屬性，以動態方式指派給群組。

例如，您可以根據使用者的群組，將使用者指派給群組：

* 特定的職稱或部門

* 它們所屬的夥伴組織 (手動或透過已連線的組織) 

* 使用者類型 (成員或來賓) 

* 以手動方式參與特定專案 () 

* location

動態群組可以包含使用者或裝置，但不能同時包含兩者。 您可以新增以使用者屬性為基礎的查詢，以將使用者指派至動態群組。 下列範例會顯示將使用者新增至群組的查詢，如果它們是 (不是來賓) 和財務部門的成員。

![設定動態成員資格規則的螢幕擷取畫面。](media/secure-external-access/4-dynamic-membership-rules.png)

如需動態群組的詳細資訊，請參閱 [在 Azure Active Directory 中建立或更新動態群組。](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>請勿將群組用於多個用途

針對安全性或資源存取用途使用群組時，請務必要有單一函式。 如果群組用來授與資源的存取權，則不應將其用於任何其他用途。 如果群組是用於一般用途（例如定義位置或小組成員資格），則也不應該使用它來保護存取的安全。 

我們建議安全性群組的命名慣例，讓目的更清楚。 例如：

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>群組的類型

您可以從 Azure AD 入口網站或 M365 系統管理員入口網站建立 Azure AD 安全性群組和 Microsoft 365 群組。 這兩種類型都可用來做為保護外部存取的基礎：

|考量 | Azure AD 安全性群組 (手動和動態) | Microsoft 365 群組 |
| - | - | - |
| 群組可以包含哪些專案？| 使用者<br>群組<br>服務主體<br>裝置| 僅限使用者 |
| 群組在哪裡建立？| Azure AD 入口網站<br>如果要啟用郵件功能，M365 入口網站 () <br>PowerShell<br>Microsoft Graph<br>終端使用者入口網站| M365 入口網站<br>Azure AD 入口網站<br>PowerShell<br>Microsoft Graph<br>在 Microsoft 365 應用程式中 |
| 預設會建立誰？| Administrators <br>使用者| Administrators<br>使用者 |
| 誰可以依預設新增？| 內部使用者 (成員) | 來自任何組織的租使用者成員和來賓 |
| 它授與存取權的專案為何？| 只有指派給它的資源。| 所有群組相關資源：<br> (群組信箱、網站、小組、聊天室和其他內含的 M365 資源) <br>新增群組的任何其他資源 |
| 可以搭配使用| 條件式存取<br>權利管理<br>群組授權| 條件式存取<br>權利管理<br>敏感度標籤 |



使用 Microsoft 365 群組來建立和管理一組 Microsoft 365 資源，例如小組及其相關聯的網站和內容。 對於以專案為基礎的工作來說，這是很好的選擇。 

 

## <a name="azure-ad-security-groups"></a>Azure AD 安全性群組 

[Azure AD 安全性群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) 可以包含使用者或裝置，而且可以用來管理存取權 

* Azure 資源（例如 Microsoft 365 apps、自訂應用程式和軟體即服務） (SaaS) 應用程式，例如 Dropbox 的 ServiceNow。

* Azure 資料與訂用帳戶。

* Azure 服務。

Azure AD 安全性群組也可以用來：

* 指派 M365、Dynamics 365 和企業行動力和安全性等服務的授權。 如需詳細資訊，請參閱以 [群組為基礎的授權](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)。

* 指派較高的許可權。 如需詳細資訊，請參閱 [使用雲端群組管理角色指派 (預覽](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)) 。 

若要 [在 Azure 入口網站中](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 建立群組，請流覽至 [Azure Active Directory]，然後流覽至 [群組]。 您也可以使用 [PowerShell Cmdlet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)來建立 Azure AD 安全性群組。 

> [!NOTE]
> 安全性群組可以用來指派最多1500應用程式，但不能超過其他。 

![建立安全性群組的螢幕擷取畫面。](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **若要建立擁有郵件功能的安全性群組，請移至 [Microsoft 365 系統管理中心](https://admin.microsoft.com/)**。 您無法在 Azure AD 入口網站中建立它。 
<br>您必須在建立時啟用 mail 的安全性群組。 您稍後無法啟用。

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>混合式組織和 Azure AD 安全性群組

混合式組織具有內部部署基礎結構和 Azure AD 雲端基礎結構。 許多使用 Active Directory 的混合式組織都會在內部部署建立安全性群組，並將其同步至雲端。 使用這個方法時，只能將內部部署環境中的使用者新增至安全性群組。

**保護您的內部部署基礎結構免于洩漏，因為內部部署的缺口可用來取得 Microsoft 365 租使用者的存取權**。 如需指引，請參閱 [保護內部部署攻擊的 Microsoft 365](https://aka.ms/protectm365) 。

## <a name="microsoft-365-groups"></a>Microsoft 365 群組

[Microsoft 365 群組](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) 是基礎成員資格服務，可驅動跨 M365 的所有存取。 您可以從 [Azure 入口網站](https://portal.azure.com/)或 [M365 入口網站](https://admin.microsoft.com/)建立它們。 建立 M365 群組時，您會將存取權授與用來共同作業的資源群組。 如需這些資源的完整清單，請參閱系統 [管理員 Microsoft 365 群組的總覽](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) 。

M365 群組的角色具有下列細微差異

* **擁有** 者-群組擁有者可以新增或移除成員，以及具有唯一的許可權，例如從共用收件匣刪除交談或變更群組設定的功能。 群組擁有者可以重新命名群組、更新描述或圖片等等。

* **成員** -成員可以存取群組中的所有專案，但無法變更群組設定。 依預設，群組成員可以邀請來賓加入您的群組，但您可以 [控制該設定](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide)。

* **來賓** -群組來賓是來自組織外部的成員。 來賓預設會對小組的功能有一些限制。

 

### <a name="m365-group-settings"></a>M365 群組設定

您可以選取 [電子郵件別名]、[隱私權]，以及是否要在設定時為小組啟用群組。 

![編輯 Microsoft 365 群組設定的螢幕擷取畫面](media/secure-external-access/4-edit-group-settings.png)

安裝之後，您可以新增成員，以及設定電子郵件使用方式等設定。

### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷您所需的外部存取安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. 在這裡[使用安全性群組](4-secure-access-groups.md) (。 ) 

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)