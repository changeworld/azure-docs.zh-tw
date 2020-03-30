---
title: 可用的自訂管理員角色許可權 - Azure AD |微軟文檔
description: 用於委派標識管理的自訂管理員角色許可權。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025155"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure 活動目錄中的應用程式註冊子類型和許可權

本文包含 Azure 活動目錄 （Azure AD） 中自訂角色定義的當前可用應用註冊許可權。

## <a name="permissions-for-managing-single-directory-applications"></a>管理單目錄應用程式的許可權

為自訂角色選擇許可權時，您可以選擇授予僅管理單目錄應用程式的存取權限。 單目錄應用程式僅對註冊應用程式的 Azure AD 組織中的使用者可用。 單目錄應用程式定義為**將受支援的帳戶類型**設置為"僅在此組織目錄中的帳戶"。 在圖形 API 中，單目錄應用程式將符號 InAudience 屬性設置為"AzureADMyOrg"。

要授予僅管理單目錄應用程式的存取權限，請使用以下許可權與子類型**應用程式。** 例如，microsoft.directory/應用程式.my組織/基本/更新。

有關常規術語子類型、許可權和屬性集的含義的說明，請參閱[自訂角色概述](roles-custom-overview.md)。 以下資訊特定于應用程式註冊。

### <a name="create-and-delete"></a>創建和刪除

有兩種許可權可用於授予創建應用程式註冊的許可權，每個許可權具有不同的行為：

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>微軟.目錄/應用程式/創建擁有者

分配此許可權會導致建立者被添加為已創建應用註冊的第一個擁有者，並且創建的應用註冊將計入建立者的 250 個創建物件配額。

#### <a name="microsoftdirectoryapplicationscreate"></a>微軟.目錄/應用程式/創建

分配此許可權會導致建立者未添加為已創建應用註冊的第一個擁有者，並且創建的應用註冊不會計入建立者創建的 250 個創建物件配額。 請謹慎使用此許可權，因為在命中目錄級配額之前，不會阻止受讓人創建應用註冊。 如果同時分配了兩個許可權，則此許可權優先。

如果同時分配了兩個許可權，則 /create 許可權將優先。 儘管 /createAsOwner 許可權不會自動將建立者添加為第一個擁有者，但在創建應用註冊期間，可以使用圖形 API 或 PowerShell Cmdlet 指定擁有者。

創建授予對 **"新建註冊"** 命令的許可權。

[這些許可權授予對"新註冊"門戶命令的存取權限](./media/roles-create-custom/new-custom-role.png)

有兩種許可權可用於授予刪除應用註冊的許可權：

#### <a name="microsoftdirectoryapplicationsdelete"></a>微軟.目錄/應用程式/刪除

授予刪除應用註冊的能力，而不考慮子類型;即單租戶和多租戶應用程式。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>微軟.目錄/應用程式.my組織/刪除

授予刪除僅限於組織中的帳戶或單租戶應用程式（my組織子類型）的應用註冊的許可權。

![這些許可權授予對"刪除應用註冊"命令的存取權限](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 分配包含創建許可權的角色時，必須在目錄作用域中分配角色。 在資源作用域中分配的創建許可權不會授予創建應用註冊的許可權。

### <a name="read"></a>讀取

預設情況下，組織中的所有成員使用者可以讀取應用註冊資訊。 但是，來賓使用者和應用程式服務主體不能。 如果計畫將角色指派給來賓使用者或應用程式，則必須包含相應的讀取權限。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>微軟.目錄/應用程式/所有屬性/讀取

能夠讀取在憑據等任何情況下無法讀取的屬性之外的單租戶和多租戶應用程式的所有屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>微軟.目錄/應用程式.my組織/所有屬性/讀取

授予與 Microsoft.directory/應用程式/allProperties/read 相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationsownersread"></a>微軟.目錄/應用程式/擁有者/讀取

授予在單租戶和多租戶應用程式上讀取擁有者屬性的能力。 授予對應用程式註冊擁有者頁面上所有欄位的存取權限：

![此許可權授予對應用註冊擁有者頁面的存取權限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>微軟.目錄/應用程式/標準/讀取

授予對讀取標準應用程式註冊屬性的存取權限。 這包括跨應用程式註冊頁的屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>微軟.目錄/應用程式.my組織/標準/讀取

授予與 Microsoft.directory/應用程式/標準/讀取相同的許可權，但僅適用于單租戶應用程式。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>微軟.目錄/應用程式/所有屬性/更新

能夠更新單目錄和多目錄應用程式上的所有屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/應用程式.my組織/所有屬性/更新

授予與 Microsoft.directory/應用程式/allProperties/更新相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>微軟.目錄/應用程式/觀眾/更新

能夠在單目錄和多目錄應用程式上更新受支援的帳戶類型（signInAudience）屬性。

![此許可權授予對身份驗證頁上應用註冊支援的帳戶類型屬性的存取權限](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>微軟.目錄/應用程式.my組織/觀眾/更新

授予與 Microsoft.directory/應用程式/訪問/更新相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>微軟.目錄/應用程式/身份驗證/更新

能夠在單租戶和多租戶應用程式上更新回復 URL、登出 URL、隱式流和發行者域屬性。 授予對應用程式註冊身份驗證頁上所有欄位的存取權限，但受支援的帳戶類型除外：

![授予對應用註冊身份驗證（但不支援的帳戶類型）的存取權限](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>微軟.目錄/應用程式.my組織/認證/更新

授予與 Microsoft.directory/應用程式/身份驗證/更新相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>微軟.目錄/應用程式/基本/更新

能夠更新單租戶和多租戶應用程式的名稱、徽標、主頁 URL、服務條款 URL 和隱私權聲明 URL 屬性。 授予對應用程式註冊品牌頁面上所有欄位的存取權限：

![此許可權授予對應用註冊品牌頁面的存取權限](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>微軟.目錄/應用程式.my組織/基本/更新

授予與 Microsoft.directory/應用程式/基本/更新相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>微軟.目錄/應用程式/憑據/更新

能夠更新單租戶和多租戶應用程式上的證書和用戶端機密屬性。 授予對應用程式註冊證書上所有欄位的訪問，&機密頁上：

![此許可權授予對應用註冊證書&機密頁的訪問](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>微軟.目錄/應用程式.my組織/憑據/更新

授予與 Microsoft.directory/應用程式/憑據/更新相同的許可權，但僅適用于單目錄應用程式。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>微軟.目錄/應用程式/擁有者/更新

能夠在單租戶和多租戶上更新擁有者屬性。 授予對應用程式註冊擁有者頁面上所有欄位的存取權限：

![此許可權授予對應用註冊擁有者頁面的存取權限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>微軟.目錄/應用程式.my組織/擁有者/更新

授予與 Microsoft.directory/應用程式/擁有者/更新相同的許可權，但僅適用于單租戶應用程式。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>微軟.目錄/應用程式/許可權/更新

能夠更新單租戶和多租戶應用程式的委派許可權、應用程式許可權、授權用戶端應用程式、所需許可權和授予同意屬性。 不授予執行同意的許可權。 授予對應用程式註冊 API 許可權和公開 API 頁上所有欄位的許可權：

![此許可權授予對應用註冊 API 許可權頁的許可權](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此許可權授予對應用註冊的許可權 公開 API 頁](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>微軟.目錄/應用程式.my組織/許可權/更新

授予與 Microsoft.directory/應用程式/許可權/更新相同的許可權，但僅適用于單租戶應用程式。

## <a name="required-license-plan"></a>所需的許可證計畫

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用 Azure[門戶、Azure AD 電源外殼和圖形 API](roles-create-custom.md)創建自訂角色
- [查看自訂角色的分配](roles-view-assignments.md)
