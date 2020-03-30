---
title: 教程 - 創建 Azure 活動目錄 B2C 租戶
description: 了解如何使用 Azure 入口網站建立 Azure Active Directory B2C 租用戶，以準備註冊應用程式。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186398"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Azure Active Directory B2C 租用戶

在應用程式可以與 Azure 活動目錄 B2C（Azure AD B2C）交互之前，它們必須註冊到您管理的租戶中。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換到包含 Azure AD B2C 租戶的目錄
> * 將 Azure AD B2C 資源添加為 Azure 門戶中的**我的最愛**

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 登錄到 Azure[門戶](https://portal.azure.com/)。 使用至少分配了訂閱中的["參與者"](../role-based-access-control/built-in-roles.md)角色或訂閱中的資源組的 Azure 帳戶登錄。

1. 選擇包含訂閱的目錄。

    在 Azure 門戶工具列中，選擇 **"目錄 + 訂閱"** 圖示，然後選擇包含訂閱的目錄。 此目錄與包含 Azure AD B2C 租戶的目錄不同。

    ![訂閱租戶，目錄 + 訂閱篩選器，選擇訂閱租戶](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]****。
1. 搜索**Azure 活動目錄 B2C，** 然後選擇 **"創建**"。
1. 選取 [建立新的 Azure AD B2C 租用戶]****。

    ![創建在 Azure 門戶中選擇的新 Azure AD B2C 租戶](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 輸入**組織名稱**和**初始功能變數名稱**。 選擇**國家或地區**（以後無法更改），然後選擇 **"創建**"。

    功能變數名稱用作完整租戶功能變數名稱的一部分。 在此示例中，租戶名稱*contosob2c.onmicrosoft.com*：

    ![在 Azure 門戶中使用示例值在 其中創建租戶表單](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. 租戶創建完成後，選擇租戶創建頁頂部的"**創建新 B2C 租戶"或"連結到現有租戶**"連結。

    ![在 Azure 門戶中突出顯示的連結租戶痕跡連結](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. 選擇**將現有的 Azure AD B2C 租戶連結到我的 Azure 訂閱**。

   ![在 Azure 門戶中連結現有訂閱選擇](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 選擇您創建的**Azure AD B2C 租戶**，然後選擇**訂閱**。

    在 [資源群組]**** 中，選取 [新建]****。 輸入將包含租戶的資源組**的名稱**，選擇**資源組位置**，然後選擇 **"創建**"。

    ![在 Azure 門戶中連結訂閱設置表單](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    您可以將多個 Azure AD B2C 租戶連結到單個 Azure 訂閱，以便計費。

## <a name="select-your-b2c-tenant-directory"></a>選擇 B2C 租戶目錄

要開始使用新的 Azure AD B2C 租戶，需要切換到包含租戶的目錄。

在 Azure 門戶的頂部功能表中選擇**目錄 + 訂閱**篩選器，然後選擇包含 Azure AD B2C 租戶的目錄。

如果最初在清單中看不到新的 Azure B2C 租戶，請刷新瀏覽器視窗，然後在頂部功能表中再次選擇 **"目錄 + 訂閱**"篩選器。

![在 Azure 門戶中選擇的包含 B2C 租戶的目錄](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>添加 Azure AD B2C 作為我的最愛（可選）

此可選步驟便於在以下教程和所有後續教程中選擇 Azure AD B2C 租戶。

與租戶一起使用時，您無需在 **"所有服務"** 中搜索*Azure AD B2C，* 而是喜歡資源。 然後，您可以從門戶功能表的 **"我的最愛"** 部分選擇它，以便快速流覽到 Azure AD B2C 租戶。

您只需執行此操作一次。 在執行這些步驟之前，請確保已切換到包含 Azure AD B2C 租戶的目錄，如上一節所述，[請選擇 B2C 租戶目錄](#select-your-b2c-tenant-directory)。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在 Azure 門戶功能表中，選擇 **"所有服務**"。
1. 在"**所有服務**"搜索框中，搜索**Azure AD B2C**，將滑鼠懸停在搜尋結果上，然後選擇工具提示中的星形圖示。 **Azure AD B2C**現在顯示在 **"我的最愛**"下的 Azure 門戶中。
1. 如果要更改新我的最愛的位置，請轉到 Azure 門戶功能表，選擇 Azure AD **B2C**，然後將其向上或向下拖動到所需位置。

    ![Azure AD B2C，我的最愛功能表，微軟 Azure 門戶](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換到包含 Azure AD B2C 租戶的目錄
> * 將 Azure AD B2C 資源添加為 Azure 門戶中的**我的最愛**

接下來，瞭解如何在新租戶中註冊 Web 應用程式。

> [!div class="nextstepaction"]
> [註冊申請>](tutorial-register-applications.md)
