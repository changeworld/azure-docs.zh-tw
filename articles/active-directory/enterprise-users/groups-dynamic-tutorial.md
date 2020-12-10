---
title: 將使用者新增至動態群組 - 教學課程 - Azure AD |Microsoft Docs
description: 在本教學課程中，您會使用群組與使用者成員資格規則自動新增或移除使用者
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc891a7b07305fc0ef55376d7b6fbb262d740bb5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547486"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>教學課程：自動新增或移除群組成員

在 Azure Active Directory (Azure AD) 中，您可以自動新增或移除安全性群組或 Microsoft 365 群組的使用者，而無須一直以手動方式處理。 每當使用者或裝置的任何屬性有所變更時，Azure AD 就會評估您 Azure AD 組織中的所有動態群組規則，以查看變更是否會新增或移除成員。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 從合作夥伴公司建立自動填入的來賓使用者群組
> * 為來賓使用者群組指派存取合作夥伴專屬功能的授權
> * 額外好處：藉由移除來賓使用者，以便您為成員指定僅限內部網站的使用者存取權 (舉例而言)，以保障 **所有使用者** 群組的安全性

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

您必須是組織的全域管理員，且具有一個 Azure AD Premium 授權，才能使用此功能。 如果您沒有授權，請在 Azure AD 中選取 [授權]   > [產品]   > [試用/購買]  。

您不需要為使用者指派授權，他們即會成為動態群組的成員。 只要您在組織中具有最低數量的可用 Azure AD Premium P1 授權，即可涵蓋所有這類的使用者。 

## <a name="create-a-group-of-guest-users"></a>建立來賓使用者的群組

首先，您將為全部來自單一合作夥伴公司的來賓使用者建立一個群組。 他們需要特殊的授權，因此，針對此用途建立一個群組通常會較有效率。

1. 使用具備組織全域管理員身分的帳戶來登入 Azure 入口網站 (https://portal.azure.com) 。
2. 選取 [Azure Active Directory]   > [群組]   > [新增群組]  。
   ![選取命令來開始一個新的群組](./media/groups-dynamic-tutorial/new-group.png)
3. 在 [群組]  刀鋒視窗上：
  
   * 選取 [安全性]  作為群組類型。
   * 輸入 `Guest users Contoso` 作為群組的名稱和描述。
   * 將 [成員資格類型]  變更為 [動態使用者]  。
   
4. 選取 [擁有者]  **，然後在 [新增擁有者]** 刀鋒視窗中搜尋任何想加入的擁有者。 按一下想加入的擁有者，以新增至選取項目。
5. 按一下 [選取]  以關閉 [新增擁有者]  刀鋒視窗。  
6. 選取 [動態使用者成員]  方塊中的 [編輯動態查詢]  。
7. 在 [動態成員資格規則]  刀鋒視窗上：

   * 在 [屬性]  欄位中按一下現有的值，然後選取 [userType]  。 
   * 確認 [運算子]  欄位已選取 [等於]  。  
   * 選取 [值]  欄位，然後輸入 **Guest**。 
   * 按一下 [新增運算式]  超連結以加入另一行。
   * 在 [And/Or]  欄位中選取 [And]  。
   * 在 [屬性]  欄位中選取 [companyName]  。
   * 確認 [運算子]  欄位已選取 [等於]  。
   * 在 [值]  欄位中輸入  。
   * 按一下 [儲存]  以關閉 [動態成員資格規則]  刀鋒視窗。
   
8. 在 [群組]  刀鋒視窗上選取 [建立]  ，以建立群組。

## <a name="assign-licenses"></a>指派授權

現在您已有新的群組，接下來可以套用這些合作夥伴使用者需要的授權。

1. 在 Azure AD 中選取 [授權]  ，選取一或多個授權，然後選取 [指派]  。
2. 選取 [使用者和群組]  ，然後選取 [來賓使用者 Contoso]  群組，並儲存您的變更。
3. [指派選項]  可讓您開啟或關閉您所選授權中包含的服務方案。 當您進行變更時，請務必按一下 [確定]  以儲存變更。
4. 若要完成指派，在 [指派授權]  窗格中，按一下窗格底部的 [指派]  。

## <a name="remove-guests-from-all-users-group"></a>從所有使用者群組中移除來賓

您最終的管理計劃可能是要依公司將所有的來賓使用者指派給其本身的群組。 現在您也可以變更 **所有使用者** 群組，使其保留給組織中的成員使用者專用。 然後，您可以使用它來指派您的主要組織專屬的應用程式和授權。

   ![將所有使用者群組變更為僅限成員](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>清除資源

**移除來賓使用者群組**

1. 使用具備組織全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory]   > [群組]  。 依序選取 [來賓使用者 Contoso]  群組、省略符號 (...) 和 [刪除]  。 當您刪除群組時，任何已指派的授權都會移除。

**還原所有使用者群組**
1. 選取 [Azure Active Directory]   > [群組]  。 從 [所有使用者]  群組的名稱，以開啟該群組。
1. 選取 [動態成員資格規則]  ，清除規則中的所有文字，然後選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：
> [!div class="checklist"]
> * 建立來賓使用者的群組
> * 將授權指派給新群組
> * 將所有使用者群組變更為僅限成員

繼續參考下一篇文章，以深入了解群組型授權的基本概念
> [!div class="nextstepaction"]
> [群組授權基本概念](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



