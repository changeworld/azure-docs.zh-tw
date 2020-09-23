---
title: 應用程式註冊的自訂角色許可權-Azure AD |Microsoft Docs
description: 委派自訂系統管理員角色許可權來管理應用程式註冊。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 624489033097c0da4d85488b7ae376c5e0f3a56b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967681"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory 中自訂角色的應用程式註冊許可權

本文包含 Azure Active Directory (Azure AD) 中自訂角色定義目前可用的應用程式註冊許可權。

## <a name="permissions-for-managing-single-tenant-applications"></a>管理單一租使用者應用程式的許可權

選擇自訂角色的許可權時，您可以選擇授與僅管理單一租使用者應用程式的存取權。 單一租使用者應用程式僅適用于註冊應用程式之 Azure AD 組織中的使用者。 單一租使用者應用程式的定義是將 **受支援的帳戶類型** 設定為 [僅限此組織目錄中的帳戶]。 在圖形 API 中，單一租使用者應用程式的 signInAudience 屬性設定為 ">azureadmyorg"。

若要授與僅管理單一租使用者應用程式的存取權，請使用下列許可權搭配 **myOrganization**子類型。 例如，myOrganization/basic/update。

請參閱 [自訂角色總覽](roles-custom-overview.md) ，以瞭解一般詞彙的子類型、許可權和屬性集的意義。 以下是應用程式註冊的特定資訊。

### <a name="create-and-delete"></a>建立和刪除

有兩個許可權可授與建立應用程式註冊的能力，每個都有不同的行為：

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

指派此許可權會導致將建立者新增為所建立應用程式註冊的第一位擁有者，而建立的應用程式註冊將會根據建立者的250建立物件配額計算。

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

指派此許可權會導致建立者未新增為所建立應用程式註冊的第一位擁有者，且建立的應用程式註冊不會計入建立者的250建立物件配額。 請謹慎使用此權限，因為在達到目錄層級配額之前，受託人將可不受限地建立應用程式註冊。 如果同時指派這兩種權限，將優先使用此權限。

如果同時指派這兩個許可權，則會優先使用/create 許可權。 雖然/createAsOwner 許可權不會自動將建立者新增為第一個擁有者，但可以在使用圖形 Api 或 PowerShell Cmdlet 時，于建立應用程式註冊期間指定擁有者。

建立許可權授與 **新註冊** 命令的存取權。

[這些許可權會授與新註冊入口網站命令的存取權](./media/roles-create-custom/new-custom-role.png)

有兩個許可權可授與刪除應用程式註冊的能力：

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

授與刪除應用程式註冊的能力，而不考慮子類型;亦即，單一租使用者和多租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft 目錄/應用程式. myOrganization/delete

授與刪除僅限您組織中帳戶或單一租使用者應用程式中的帳戶所能存取之應用程式註冊的能力， (myOrganization 子類型) 。

![這些許可權會授與刪除應用程式註冊命令的存取權](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 指派包含 create 許可權的角色時，必須在目錄範圍中建立角色指派。 在資源範圍指派的 create 許可權不會授與建立應用程式註冊的能力。

### <a name="read"></a>讀取

依預設，組織中的所有成員使用者都可以讀取應用程式註冊資訊。 不過，來賓使用者和應用程式服務主體則不能。 如果您打算將角色指派給來賓使用者或應用程式，則必須包含適當的讀取權限。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft. directory/applications/allProperties/read

能夠在無法于任何情況下讀取的屬性（例如認證）之外，讀取單一租使用者和多租使用者應用程式的所有屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>myOrganization/allProperties/read

授與與 microsoft 目錄/應用程式/allProperties/讀取相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

授與讀取單一租使用者和多租使用者應用程式上擁有者屬性的能力。 在 [應用程式註冊擁有者] 頁面上授與存取權給所有欄位：

![此許可權會授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft. 目錄/應用程式/標準/讀取

授與讀取標準應用程式註冊屬性的存取權。 這包括應用程式註冊頁面之間的屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>myOrganization/standard/read

授與與 microsoft 目錄/應用程式/標準/讀取相同的許可權，但僅適用于單一租使用者應用程式。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft 目錄/應用程式/allProperties/更新

能夠更新單一租使用者和多租使用者應用程式的所有屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>myOrganization/allProperties/update

授與與 microsoft directory/applications/allProperties/update 相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

能夠在單一租使用者和多租使用者應用程式上更新支援的帳戶類型 (signInAudience) 屬性。

![在驗證頁面上，此許可權會授與應用程式註冊支援的帳戶類型屬性的存取權](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft 目錄/應用程式。 myOrganization/物件/更新

授與與 microsoft 目錄/應用程式/物件/更新相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

能夠在單一租使用者和多租使用者應用程式上更新回復 URL、登出 URL、隱含流程和發行者網域屬性。 將存取權授與應用程式註冊驗證頁面上的所有欄位，但支援的帳戶類型除外：

![授與應用程式註冊驗證的存取權，但不支援帳戶類型](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>myOrganization/驗證/更新

授與與 microsoft 目錄/應用程式/驗證/更新相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

能夠在單一租使用者和多租使用者應用程式上更新名稱、標誌、首頁 URL、服務條款 URL，以及隱私權聲明 URL 屬性。 授與應用程式注冊商標頁面上所有欄位的存取權：

![此許可權會授與應用程式注冊商標頁面的存取權](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>myOrganization/basic/update

授與與 microsoft 目錄/applications/basic/update 相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

能夠在單一租使用者和多租使用者應用程式上更新憑證和用戶端密碼屬性。 授與應用程式註冊憑證上所有欄位的存取權 & 秘密頁面：

![此許可權會授與應用程式註冊憑證 & 秘密頁面的存取權](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>myOrganization/認證/更新

授與與 microsoft 目錄/應用程式/認證/更新相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

能夠更新單一租使用者和多租使用者的 owner 屬性。 在 [應用程式註冊擁有者] 頁面上授與存取權給所有欄位：

![此許可權會授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>myOrganization/擁有者/更新

授與與 microsoft 目錄/應用程式/擁有者/更新相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

能夠在單一租使用者和多租使用者應用程式上更新委派的許可權、應用程式許可權、授權用戶端應用程式、必要許可權，以及授與同意屬性。 不會授與執行同意的能力。 授與應用程式註冊 API 許可權的所有欄位存取權，並公開 API 頁面：

![此許可權會授與應用程式註冊 API 許可權頁面的存取權](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此許可權會授與應用程式註冊公開 API 頁面的存取權](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>myOrganization/許可權/更新

授與與 microsoft 目錄/應用程式/許可權/更新相同的許可權，但僅適用于單一租使用者應用程式。

## <a name="required-license-plan"></a>必要授權方案

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 入口網站、Azure AD PowerShell 和圖形 API](roles-create-custom.md)建立自訂角色
- [檢視自訂角色的指派](roles-view-assignments.md)
