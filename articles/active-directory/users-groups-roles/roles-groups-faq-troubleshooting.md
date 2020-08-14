---
title: 針對指派給雲端群組的角色進行疑難排解常見問題-Azure Active Directory |Microsoft Docs
description: 在 Azure 入口網站、PowerShell 或圖形 API 中，將 Azure AD 角色指派給角色可指派的群組。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bf041f64a2f85f3aa3eada1dc1955c93dc034a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208195"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>針對指派給雲端群組的角色進行疑難排解

以下是在 Azure Active Directory (Azure AD) 中，將角色指派給群組的一些常見問題和疑難排解秘訣。

**問：** 我是群組系統管理員，但看不到 **Azure AD 角色可以指派給群組** 交換器。

**答：** 只有特殊許可權角色管理員或全域管理員可以建立符合角色指派資格的群組。 只有那些角色中的使用者會看到此控制項。

**問：** 誰可以修改指派給 Azure AD 角色之群組的成員資格？

**答：** 根據預設，只有特殊許可權角色管理員和全域管理員會管理角色可指派群組的成員資格，但是您可以藉由新增群組擁有者來委派角色可指派群組的管理。

**問**：我是組織中的技術服務管理員，但無法更新身為目錄讀取者的使用者密碼。 為什麼會發生這種情況？

**答**：使用者可能已透過角色可指派的群組取得目錄讀取程式。 角色可指派群組的所有成員和擁有者都會受到保護。 只有具備特殊許可權的驗證管理員或全域管理員角色的使用者，才可以重設受保護使用者的認證。

**問：** 我無法更新使用者的密碼。 他們沒有指派任何較高的特殊許可權角色。 為什麼會發生這種情況？

**答：** 使用者可以是角色可指派群組的擁有者。 我們會保護角色可指派群組的擁有者，以避免權限提高。 例如，如果將群組 Contoso_Security_Admins 指派給安全性系統管理員角色，其中 Bob 是群組擁有者，而 Alice 是在組織中密碼管理員。 如果此保護不存在，Alice 可能會重設 Bob 的認證，並接受他的身分識別。 之後，Alice 就可以將自己或任何人新增至群組 Contoso_Security_Admins 群組，成為組織中的安全性系統管理員。 若要找出使用者是否為群組擁有者，請取得該使用者擁有之物件的清單，並查看是否有任何群組的 isAssignableToRole 設定為 true。 如果是，則該使用者會受到保護，而且行為是依設計而成。 請參閱下列檔以取得擁有的物件：

- [Get-AzureADUserOwnedObject](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [清單 ownedObjects](https://docs.microsoft.com/graph/api/user-list-ownedobjects?view=graph-rest-1.0&tabs=http)

**問：** 我可以在可以指派給 Azure AD 角色的群組上建立存取權審查 (具體來說，isAssignableToRole 屬性設定為 true) 的群組？  

**答：** 是的，您可以。 如果您使用的是最新版本的存取權審查，則您的審核者預設會被導向我的存取權，而且只有全域系統管理員可以在可指派角色的群組上建立存取權審查。 不過，如果您使用的是較舊版本的存取權審查，則您的審核者預設會導向至存取面板，全域管理員和使用者系統管理員都可以在可指派角色的群組上建立存取權審查。 新體驗將會在2020年7月28日推出給所有客戶，但如果您想要更快升級，請要求 [Azure AD 存取審查-我的存取權註冊的已更新審查者體驗](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

**問：** 我可以建立存取封裝，並將可指派給它的 Azure AD 角色放入群組嗎？

**答：** 是的，您可以。 全域管理員和使用者系統管理員有能力將任何群組放在存取套件中。 全域管理員沒有任何變更，但使用者系統管理員角色許可權的變更也稍微改變。 若要將可指派角色的群組放入存取套件，您必須是使用者系統管理員，以及角色可指派群組的擁有者。 以下是顯示哪些人可以在企業授權管理中建立存取套件的完整表格：

Azure AD 目錄角色 | 權利管理角色 | 可以新增安全性群組\* | 可以新增 Microsoft 365 群組\* | 可以新增應用程式 | 可以新增 SharePoint Online 網站
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
全域管理員 | n/a | ✔️ | ✔️ | ✔️  | ✔️
使用者管理員  | n/a  | ✔️  | ✔️  | ✔️
Intune 管理員 | 目錄擁有者 | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange 系統管理員  | 目錄擁有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Microsoft Teams 服務管理員 | 目錄擁有者  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint 管理員 | 目錄擁有者 | &nbsp; | ✔️  | &nbsp;  | ✔️ 
應用程式管理員 | 目錄擁有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
雲端應用程式系統管理員 | 目錄擁有者  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
User | 目錄擁有者 | 只有在群組擁有者 | 只有在群組擁有者 | 只有應用程式擁有者  | &nbsp;

\*群組不是可指派的角色;也就是 isAssignableToRole = false。 如果群組可指派角色，則建立存取套件的人員也必須是可指派角色之群組的擁有者。

**問：** 在 [指派的角色] 中找不到 [移除指派] 選項。 如何? 刪除使用者的角色指派嗎？

**答：** 此答案僅適用于 Azure AD Premium P1 組織。

1. 登入 [Azure 入口網站](https://portal.azure.com) 並開啟 **Azure Active Directory**。
1. 選取 [使用者] 並開啟使用者設定檔。
1. 選取 [ **指派的角色**]。
1. 選取齒輪圖示。 窗格隨即開啟，可提供此資訊。 直接指派旁會出現 [移除] 按鈕。 若要移除間接角色指派，請從已指派角色的群組中移除使用者。

**問：** 如何? 查看所有可指派角色的群組嗎？

**答：** 請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com) 並開啟 **Azure Active Directory**。
1. 選取 [**群組**] [  >  **所有群組**]。
1. 選取 [ **新增篩選**]。
1. 篩選為可 **指派的角色**。

**問：** 如何? 知道哪些角色會直接和間接指派給主體？

**答：** 請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com) 並開啟 **Azure Active Directory**。
1. 選取 [使用者] 並開啟使用者設定檔。
1. 選取 [ **指派的角色**]，然後：

    - 在 [Azure AD Premium P1 授權的組織] 中：選取齒輪圖示。 窗格隨即開啟，可提供此資訊。
    - 在 Azure AD Premium P2 授權的組織中：您可以在 [ **成員資格** ] 資料行中找到直接和繼承的授權資訊。

**問：** 為什麼要建立新的雲端群組來將它指派給角色？  

**答：** 如果您將現有的群組指派給某個角色，現有的群組擁有者就可以在此群組中新增其他成員，而不會有新的成員意識到他們會擁有該角色。 因為角色可指派群組的功能強大，所以我們會將許多限制放在一起來保護它們。 您不想要對管理群組的人員感到驚訝的變更群組。

## <a name="next-steps"></a>後續步驟

- [使用雲端群組來管理角色指派](roles-groups-concept.md)
- [建立可指派角色的群組](roles-groups-create-eligible.md)
