---
title: 在授權管理中創建新的訪問包 - Azure AD
description: 瞭解如何在 Azure 活動目錄授權管理中創建新的資源訪問包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262005"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中創建新的訪問包

訪問包使您能夠對資源和策略進行一次性設置，從而自動管理訪問包的生命週期。 本文介紹如何創建新的訪問包。

## <a name="overview"></a>總覽

所有訪問包必須放在一個稱為目錄的容器中。 目錄定義可以添加到訪問包的資源。 如果不指定目錄，您的訪問包將放入常規目錄中。 目前，無法將現有訪問包移動到其他目錄。

如果您是訪問包管理器，則無法將您擁有的資源添加到目錄中。 您僅限於使用目錄中可用的資源。 如果需要將資源添加到目錄，可以詢問目錄擁有者。

所有訪問包必須至少有一個策略。 策略指定誰可以請求訪問包以及審批和生命週期設置。 創建新訪問包時，可以為目錄中的使用者、不在目錄中的使用者、僅為管理員直接分配創建初始策略，也可以選擇稍後創建策略。

![建立存取套件](./media/entitlement-management-access-package-create/access-package-create.png)

下面是創建新訪問包的高級步驟。

1. 在標識治理中，啟動進程以創建新的訪問包。

1. 選擇要在其中創建訪問包的目錄。

1. 將資源從目錄添加到訪問包。

1. 為每個資源配置資源角色。

1. 指定可以請求存取權限的使用者。

1. 指定任何審批設置。

1. 指定生命週期設置。

## <a name="start-new-access-package"></a>啟動新的訪問包

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 [存取套件]****。

1. 按一下 [新增存取套件]****。
   
    ![Azure 入口網站中的權利管理](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>基本概念

在 **"基本"** 選項卡上，為訪問包指定名稱並指定要在哪個目錄中創建訪問包。

1. 輸入訪問包的顯示名稱和說明。 當使用者提交訪問包請求時，將看到此資訊。

1. 在 **"目錄**"下拉清單中，選擇要在其中創建訪問包的目錄。 例如，您可能有一個目錄擁有者來管理所有可請求的行銷資源。 在這種情況下，您可以選擇市場行銷目錄。

    您只能看到具有在 其中創建訪問包的許可權的目錄。 要在現有目錄中創建訪問包，您必須是全域管理員或使用者管理員，或者必須是該目錄中的目錄擁有者或訪問包管理器。

    ![訪問包 - 基礎知識](./media/entitlement-management-access-package-create/basics.png)

    如果您是全域管理員、使用者管理員或目錄建立者，並且希望在新目錄中創建未列出的訪問包，請按一下"**創建新目錄**"。 輸入目錄名稱和說明，然後按一下"**創建**"。

    您正在創建的訪問包及其中包含的任何資源都將添加到新目錄中。 您還可以稍後添加其他目錄擁有者。

1. 按 [下一步]****。

## <a name="resource-roles"></a>資源角色

在"**資源角色**"選項卡上，您可以選擇要包含在訪問包中的資源。 請求和接收訪問包的使用者將收到訪問包中的所有資源角色。

1. 按一下要添加的資源類型（**組和團隊**、**應用程式**或**SharePoint 網站**）。

1. 在顯示的"選擇"窗格中，從清單中選擇一個或多個資源。

    ![訪問包 - 資源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果要在常規目錄中創建訪問包或新目錄中，則可以從您擁有的目錄中選擇任何資源。 您必須至少是全域管理員、使用者管理員或目錄建立者。

    如果要在現有目錄中創建訪問包，則可以選擇目錄中已存在的任何資源，而無需擁有它。

    如果您是全域管理員、使用者管理員或目錄擁有者，則可以選擇目錄中尚未擁有的資源的其他選項。 如果選擇當前不在所選目錄中的資源，這些資源也將添加到目錄中，供其他目錄管理員使用中構建訪問包。 如果只想選擇當前位於所選目錄中的資源，請在"選擇"窗格頂部選中"**僅看到"** 核取方塊。

1. 選擇資源後，在 **"角色**"清單中選擇要為資源配置使用者的角色。

    ![訪問包 - 資源角色選擇](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 按 [下一步]****。

## <a name="requests"></a>Requests

在"**請求"** 選項卡上，您將創建第一個策略來指定誰可以請求訪問包以及審批設置。 稍後，您可以創建更多請求策略，以允許其他使用者組使用自己的審批佈建要求訪問包。

![訪問包 - 請求選項卡](./media/entitlement-management-access-package-create/requests.png)

根據您希望向誰請求此訪問包，請在以下部分之一執行這些步驟。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>檢閱 + 建立

在 **"審核 + 創建**"選項卡上，您可以查看設置並檢查是否存在任何驗證錯誤。

1. 查看訪問包的設置

    ![訪問包 - 策略 - 啟用策略設置](./media/entitlement-management-access-package-create/review-create.png)

1. 按一下 **"創建**"以創建訪問包。

    新的訪問包將顯示在訪問包清單中。

## <a name="creating-an-access-package-programmatically"></a>以程式設計方式創建訪問包

您還可以使用 Microsoft 圖形創建訪問包。  具有已委派`EntitlementManagement.ReadWrite.All`許可權的應用程式具有適當角色的使用者可以將 API 呼叫

1. [在目錄中列出訪問包資源](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta)，並為目錄中尚未存在的任何資源[創建訪問包資源請求](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)。
1. 在訪問包目錄中列出每個訪問包資源的訪問[包資源角色](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta)。 然後，在隨後創建訪問包資源功能範圍時，此角色清單將用於選擇角色。
1. [創建訪問包](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta)。
1. [創建訪問包分配策略](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta)。
1. 為訪問包中所需的每個資源角色[創建訪問包資源功能範圍](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta)。

## <a name="next-steps"></a>後續步驟

- [共用連結以請求訪問包](entitlement-management-access-package-settings.md)
- [更改訪問包的資源角色](entitlement-management-access-package-resources.md)
- [直接將使用者分配給訪問包](entitlement-management-access-package-assignments.md)
