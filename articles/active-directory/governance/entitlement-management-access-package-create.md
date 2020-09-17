---
title: 在權利管理中建立新的存取套件-Azure AD
description: 瞭解如何在 Azure Active Directory 權利管理中，建立您想要共用之資源的新存取套件。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0126c5348a2acaebea0400a94ca0a1d14a1bf6ff
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707538"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中建立新的存取套件

存取套件可讓您對存取套件的存留期自動管理存取權的資源和原則進行一次設定。 本文說明如何建立新的存取套件。

## <a name="overview"></a>概觀

所有存取套件都必須放在名為「目錄」的容器中。 目錄會定義您可以新增至存取套件的資源。 如果您未指定目錄，則會將您的存取套件放入一般目錄。 目前，您無法將現有的存取套件移至不同的目錄。

如果您是存取套件管理員，則無法將您擁有的資源新增至目錄。 您只能使用目錄中可用的資源。 如果您需要將資源新增至目錄，您可以要求目錄擁有者。

所有存取套件都必須至少有一個原則。 原則會指定誰可以要求存取套件，也會指定核准和生命週期設定。 當您建立新的存取套件時，您可以為目錄中的使用者建立初始原則、為不在目錄中的使用者建立初始原則、僅限系統管理員直接指派，或者您可以選擇稍後再建立原則。

![建立存取套件](./media/entitlement-management-access-package-create/access-package-create.png)

以下是建立新存取套件的高階步驟。

1. 在身分識別管理中，啟動建立新存取套件的程式。

1. 選取您要在其中建立存取套件的目錄。

1. 從目錄將資源新增至您的存取套件。

1. 為每個資源指派資源角色。

1. 指定可要求存取權的使用者。

1. 指定任何核准設定。

1. 指定生命週期設定。

## <a name="start-new-access-package"></a>開始新的存取套件

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [存取套件]  。

1. 按一下 [新增存取套件]  。
   
    ![Azure 入口網站中的權利管理](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>基本概念

在 [ **基本** ] 索引標籤上，您會提供存取套件的名稱，並指定要在其中建立存取套件的目錄。

1. 輸入存取套件的顯示名稱和描述。 當使用者提交存取套件的要求時，會看到這則資訊。

1. 在 [ **類別目錄** ] 下拉式清單中，選取您要在其中建立存取套件的目錄。 例如，您的目錄擁有者可能會管理所有可要求的行銷資源。 在此情況下，您可以選取行銷目錄。

    您只會看到您有權在中建立存取套件的目錄。 若要在現有的目錄中建立存取套件，您必須是全域管理員或使用者管理員，或者您必須是目錄擁有者或該目錄中的存取套件管理員。

    ![存取套件-基本](./media/entitlement-management-access-package-create/basics.png)

    如果您是全域管理員、使用者系統管理員或目錄建立者，並且想要在未列出的新目錄中建立存取套件，請按一下 [ **建立新目錄**]。 輸入目錄名稱和描述，然後按一下 [ **建立**]。

    您正在建立的存取套件和其中包含的任何資源，將會新增至新的目錄。 您也可以稍後再加入其他目錄擁有者。

1. 按一下 [下一步]。

## <a name="resource-roles"></a>資源角色

在 [ **資源角色** ] 索引標籤上，您可以選取要包含在存取套件中的資源。 要求和接收存取套件的使用者將會收到存取套件中的所有資源角色。

1. 按一下您想要新增 (**群組和小組**、 **應用程式**或 **SharePoint 網站**) 的資源類型。

1. 在出現的選取窗格中，從清單中選取一或多個資源。

    ![存取套件-資源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果您要在一般目錄或新的目錄中建立存取套件，您將能夠從您所擁有的目錄中挑選任何資源。 您至少必須是全域管理員、使用者系統管理員或目錄建立者。

    如果您要在現有的目錄中建立存取套件，您可以選取已在目錄中的任何資源，而不會擁有該資源。

    如果您是全域管理員、使用者系統管理員或目錄擁有者，您可以選擇其他選項來選取您所擁有但尚未存在於目錄中的資源。 如果您選取目前不在所選目錄中的資源，則也會將這些資源新增至目錄，供其他目錄管理員用來建立存取套件。 若要查看所有可新增至目錄的資源，請勾選 [選取] 窗格頂端的 [ **全部查看** ] 核取方塊。 如果您只想要選取目前在所選目錄中的資源，請將核取方塊 **顯示** 為 [所有未核取的 (預設狀態]) 。

1. 當您選取資源之後，請在 [ **角色** ] 清單中選取您想要為資源指派給使用者的角色。

    ![存取套件-資源角色選取](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 按一下 [下一步]。

## <a name="requests"></a>Requests

在 [ **要求** ] 索引標籤上，您可以建立第一個原則來指定誰可以要求存取套件，也可以核准設定。 稍後，您可以建立更多要求原則，以允許其他使用者群組使用自己的核准設定來要求存取套件。

![存取套件-[要求] 索引標籤](./media/entitlement-management-access-package-create/requests.png)

視您想要能夠要求此存取套件的使用者而定，執行下列其中一節中的步驟。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>檢閱 + 建立

在 [ **審核 + 建立** ] 索引標籤上，您可以檢查您的設定，並檢查是否有任何驗證錯誤。

1. 檢查存取套件的設定

    ![存取套件-原則-啟用原則設定](./media/entitlement-management-access-package-create/review-create.png)

1. 按一下 [ **建立** ] 以建立存取套件。

    新的存取套件會出現在存取套件清單中。

## <a name="creating-an-access-package-programmatically"></a>以程式設計方式建立存取套件

您也可以使用 Microsoft Graph 建立存取套件。  具有具有委派許可權之應用程式的適當角色中的使用者 `EntitlementManagement.ReadWrite.All` 可以呼叫 API 來

1. [列出目錄中的 accessPackageResources](https://docs.microsoft.com/graph/api/accesspackagecatalog-list?view=graph-rest-beta&tabs=http) ，並針對尚未存在於目錄中的任何資源 [建立 accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&tabs=http) 。
1. [列出](https://docs.microsoft.com/graph/api/accesspackage-list-accesspackageresourcerolescopes?view=graph-rest-beta&tabs=http) accessPackageCatalog 中每個 AccessPackageResource 的 accessPackageResourceRoles。 接著，在建立 accessPackageResourceRoleScope 時，會使用此角色清單來選取角色。
1. [建立 accessPackage](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta)。
1. [建立 accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta&tabs=http)。
1. 針對存取套件中所需的每個資源角色[建立 accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta&tabs=http) 。

## <a name="next-steps"></a>後續步驟

- [要求存取套件的共用連結](entitlement-management-access-package-settings.md)
- [變更存取套件的資源角色](entitlement-management-access-package-resources.md)
- [直接將使用者指派給存取套件](entitlement-management-access-package-assignments.md)