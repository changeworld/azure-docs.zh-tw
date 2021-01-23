---
title: 預設使用者權限 - Azure Active Directory | Microsoft Docs
description: 深入了解 Azure Active Directory 中可用的不同使用者權限。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724266"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory 中的預設使用者權限是什麼？
在 Azure Active Directory (Azure AD) 中，所有使用者都會獲得一組預設權限。 使用者的存取權包含使用者類型、其[角色指派](active-directory-users-assign-role-azure-portal.md)，以及個別物件的擁有權。 本文說明這些預設權限，並包含成員與來賓使用者預設值的比較。 只能在 Azure AD 中的使用者設定中變更預設使用者權限。

## <a name="member-and-guest-users"></a>成員和來賓使用者
使用者所收到的預設權限集取決於使用者是租用戶的原生成員 (成員使用者)，還是來自另一個目錄的 B2B 共同作業來賓 (來賓使用者)。 如需新增來賓使用者的詳細資訊，請參閱[什麼是 Azure AD B2B 共同作業？](../external-identities/what-is-b2b.md)。
* 成員使用者可以註冊應用程式、管理自己的設定檔相片和行動電話號碼、變更自己的密碼，以及邀請 B2B 來賓。 此外，使用者還可讀取所有目錄資訊 (但有一些例外狀況)。 
* 來賓使用者具有受限的目錄權限。 他們可以管理自己的設定檔、變更自己的密碼，以及取得其他使用者、群組和應用程式的一些相關資訊，但他們無法讀取所有目錄資訊。 例如，來賓使用者無法列舉所有使用者、群組和其他目錄物件的清單。 您可將來賓新增至系統管理員角色，將角色內含的完整讀取和寫入權限授予他們。 來賓也可以邀請其他來賓。

## <a name="compare-member-and-guest-default-permissions"></a>比較成員與來賓預設的權限

**領域** | **成員使用者權限** | **預設來賓使用者權限** | **受限制的來賓使用者權限 (預覽)**
------------ | --------- | ---------- | ----------
使用者和連絡人 | <ul><li>列舉所有使用者和連絡人的清單<li>讀取使用者和連絡人的所有公用屬性</li><li>邀請來賓<li>變更自己的密碼<li>管理自己的行動電話號碼<li>管理自己的相片<li>使自己的重新整理權杖失效</li></ul> | <ul><li>讀取自己的屬性<li>讀取顯示名稱、電子郵件、登入名稱、相片、使用者主體名稱，以及其他使用者和連絡人的使用者類型屬性<li>變更自己的密碼<li>如果允許，請依 ObjectId (搜尋其他使用者) <li>讀取其他使用者的管理員和直屬報告資訊</li></ul> | <ul><li>讀取自己的屬性<li>變更自己的密碼</li></ul>
群組 | <ul><li>建立安全性群組<li>建立 Microsoft 365 群組<li>列舉所有群組的清單<li>讀取群組的所有屬性<li>讀取非隱藏的群組成員資格<li>讀取已加入群組的隱藏 Microsoft 365 群組成員資格<li>管理使用者擁有之群組的屬性、擁有權及成員資格<li>將來賓新增至擁有的群組<li>管理動態成員資格設定<li>刪除擁有的群組<li>還原擁有的 Microsoft 365 群組</li></ul> | <ul><li>讀取非隱藏群組的屬性，包括成員資格和擁有權 (甚至未加入的群組) <li>讀取已加入群組的隱藏 Microsoft 365 群組成員資格<li>依顯示名稱或 ObjectId (搜尋群組（如果允許）) </li></ul> | <ul><li>讀取已加入群組的物件識別碼<li>如果允許，請在部分 Microsoft 365 應用程式 (中，讀取已加入群組的成員資格和擁有權) </li></ul>
應用程式 | <ul><li>註冊 (建立) 新的應用程式<li>列舉所有應用程式的清單<li>讀取已註冊和企業應用程式的屬性<li>管理擁有之應用程式的應用程式屬性、指派及認證<li>建立或刪除使用者的應用程式密碼<li>刪除擁有的應用程式<li>還原擁有的應用程式</li></ul> | <ul><li>讀取已註冊和企業應用程式的屬性</li></ul> | <ul><li>讀取已註冊和企業應用程式的屬性
裝置</li></ul> | <ul><li>列舉所有裝置的清單<li>讀取裝置的所有屬性<li>管理擁有之裝置的所有屬性</li></ul> | 沒有權限 | 沒有權限
目錄 | <ul><li>讀取所有公司資訊<li>讀取所有網域<li>讀取所有夥伴合約</li></ul> | <ul><li>讀取公司顯示名稱<li>讀取所有網域</li></ul> | <ul><li>讀取公司顯示名稱<li>讀取所有網域</li></ul>
角色和範圍 | <ul><li>讀取所有系統管理角色和成員資格<li>讀取系統管理單元的所有屬性和成員資格</li></ul> | 沒有權限 | 沒有權限
訂用帳戶 | <ul><li>讀取所有訂用帳戶<li>啟用服務方案成員</li></ul> | 沒有權限 | 沒有權限
原則 | <ul><li>讀取原則的所有屬性<li>管理擁有之原則的所有屬性</li></ul> | 沒有權限 | 沒有權限

## <a name="restrict-member-users-default-permissions"></a>限制成員使用者的預設許可權 

成員使用者的預設許可權可透過下列方式加以限制：

權限 | 設定說明
---------- | ------------
使用者可以註冊應用程式 | 將此選項設定為 [否] 可防止使用者建立應用程式註冊。 然後，您可以將其新增至應用程式開發人員角色，以將其授與給特定個人。
允許使用者將工作或學校帳戶與 LinkedIn 連線 | 將此選項設定為 [否] 可防止使用者將其工作或學校帳戶與其 LinkedIn 帳戶連接。 如需詳細資訊，請參閱 [LinkedIn 帳戶連接資料共用和同意](../enterprise-users/linkedin-user-consent.md)。
建立安全性群組的能力 | 將此選項設定為 [否] 可防止使用者建立安全性群組。 全域系統管理員和使用者系統管理員仍可建立安全性群組。 請參閱[用於進行群組設定的 Azure Active Directory Cmdlet](../enterprise-users/groups-settings-cmdlets.md) 以了解詳情。
建立 Microsoft 365 群組的能力 | 將此選項設定為 [否] 可防止使用者建立 Microsoft 365 群組。 將此選項設定為 [部分]，可讓一組選取的使用者建立 Microsoft 365 群組。 全域系統管理員和使用者系統管理員仍然可以建立 Microsoft 365 群組。 請參閱[用於進行群組設定的 Azure Active Directory Cmdlet](../enterprise-users/groups-settings-cmdlets.md) 以了解詳情。
限制 Azure AD 系統管理入口網站的存取 | 將此選項設定為 [否]，非系統管理員可使用 Azure AD 系統管理入口網站來讀取和管理 Azure AD 資源。 [是] 會限制所有非系統管理員存取系統管理入口網站中的任何 Azure AD 資料。<p>**注意**：此設定不會限制使用 PowerShell 或其他用戶端（例如 Visual Studio）來 Azure AD 資料的存取權。設定為 [是] 時，若要授與特定的非系統管理員使用者，請使用 Azure AD administration portal 指派任何系統管理角色，例如目錄讀取者角色。<p>此角色可讓您讀取基本目錄資訊（成員使用者預設擁有的 (來賓和服務主體不) ）。
讀取其他使用者的能力 | 此設定僅在 PowerShell 中可用。 將此旗標設定為 $false 可防止所有非系統管理員讀取目錄中的使用者資訊。 此旗標不會防止讀取其他 Microsoft 服務（例如 Exchange Online）中的使用者資訊。 這項設定適用于特殊情況，因此不建議將此旗標設定為 $false。

## <a name="restrict-guest-users-default-permissions"></a>限制來賓使用者的預設許可權

來賓使用者的預設許可權可透過下列方式受到限制：

>[!NOTE]
>來賓使用者存取限制設定取代了「 **來賓使用者」許可權** 的設定限制。 如需使用這項功能的指引，請參閱 [Azure Active Directory 中的限制來賓存取許可權 (預覽) ](../enterprise-users/users-restrict-guest-permissions.md)。

權限 | 設定說明
---------- | ------------
來賓使用者存取限制 (預覽)  | 將此選項設定為 **來賓使用者時，與成員相同的存取權** 是依預設將所有成員使用者權限授與來賓使用者。<p>將此選項設定為 **[來賓使用者存取權] 會限制為其本身目錄物件的屬性和成員資格** ，預設只會限制來賓存取自己的使用者設定檔。 即使是依使用者主體名稱、ObjectId 或顯示名稱進行搜尋，也不再允許存取其他使用者。 也不再允許存取包含群組成員資格的群組資訊。<p>**注意**：此設定不會防止存取某些 Microsoft 365 服務（例如 Microsoft 小組）中加入的群組。 若要深入瞭解，請參閱 [Microsoft 團隊的來賓存取權](/MicrosoftTeams/guest-access) 。<p>無論此許可權設定為何，來賓使用者仍可新增至系統管理員角色。
來賓可邀請 | 將此選項設定為 [是] 可讓來賓邀請其他來賓。 若要深入瞭解，請參閱 [委派 B2B 共同作業的邀請](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) 。
成員可邀請 | 將此選項設定為 [是] 可讓您目錄的非系統管理員成員邀請來賓。 若要深入瞭解，請參閱 [委派 B2B 共同作業的邀請](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) 。
系統管理員和來賓邀請者角色中的使用者可邀請 | 將此選項設定為 [是] 可讓「來賓邀請者」角色中的系統管理員和使用者邀請來賓。 若設定為 [是]，則不論成員是否可以邀請設定，來賓邀請者角色中的使用者仍然可以邀請來賓。 若要深入瞭解，請參閱 [委派 B2B 共同作業的邀請](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) 。

## <a name="object-ownership"></a>物件擁有權

### <a name="application-registration-owner-permissions"></a>應用程式註冊擁有者權限
當使用者註冊應用程式時，系統會將他們自動新增為應用程式的擁有者。 身為擁有者的他們可以管理應用程式的中繼資料，例如應用程式要求的名稱和權限。 他們也可以管理應用程式的租用戶專屬設組態定，例如 SSO 組態和使用者指派。 擁有者也可以新增或移除其他擁有者。 不同於全域管理員，擁有者只能管理自己的應用程式。

### <a name="enterprise-application-owner-permissions"></a>企業應用程式擁有者許可權
當使用者新增企業應用程式時，系統會自動將他們新增為擁有者。 作為擁有者，他們可以管理應用程式的租使用者特定設定，例如 SSO 設定、布建和使用者指派。 擁有者也可以新增或移除其他擁有者。 不同于全域管理員，擁有者只能管理他們擁有的應用程式。

### <a name="group-owner-permissions"></a>群組擁有者權限
當使用者建立群組時，他們會自動新增為該群組的擁有者。 身為擁有者的他們可以管理群組的屬性 (例如名稱)，以及管理群組成員資格。 擁有者也可以新增或移除其他擁有者。 不同于全域管理員和使用者系統管理員，擁有者只能管理他們擁有的群組。 若要指派群組擁有者，請參閱[管理群組的擁有者](active-directory-accessmanagement-managing-group-owners.md)。

### <a name="ownership-permissions"></a>擁有權許可權
下表說明 Azure Active Directory 成員使用者對擁有的物件所擁有的特定許可權。 使用者只具有他們所擁有之物件的這些許可權。

#### <a name="owned-application-registrations"></a>擁有的應用程式註冊
使用者可以對所擁有的應用程式註冊執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/applications/audience/update | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/應用程式/還原 | 還原 Azure Active Directory 中的 applications。 |

#### <a name="owned-enterprise-applications"></a>擁有的企業應用程式
使用者可以在擁有的企業應用程式上執行下列動作。 企業應用程式是由服務主體、一或多個應用程式原則，有時是與服務主體位於相同租使用者中的應用程式物件所組成。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.directory/policies/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |

#### <a name="owned-devices"></a>擁有的裝置
使用者可以在所擁有的裝置上執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/devices/disable | 停用 Azure Active Directory 中的 devices。 |

#### <a name="owned-groups"></a>擁有的群組
使用者可以對擁有的群組執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/Groups.dynamicmembershiprule/更新 | 更新 Azure Active Directory 中的 groups.dynamicMembershipRule 屬性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解來賓使用者存取限制設定，請參閱 [Azure Active Directory 中的限制來賓存取許可權 (預覽) ](../enterprise-users/users-restrict-guest-permissions.md)。
* 如需深入了解如何指派 Azure AD 管理員角色，請參閱[在 Azure Active Directory 中將使用者指派給系統管理員角色](active-directory-users-assign-role-azure-portal.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)
* [管理使用者](add-users-azure-active-directory.md)