---
title: 教學課程 - 建立 Azure Active Directory B2C 租用戶
description: 了解如何使用 Azure 入口網站建立 Azure Active Directory B2C 租用戶，以準備註冊應用程式。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 044a2d2cb23e36234256b9b19363462e542176fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806845"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Active Directory B2C 租用戶

應用程式必須先在您管理的租用戶中完成註冊，才可與 Azure Active Directory B2C (Azure AD B2C) 互動。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換至包含您 Azure AD B2C 租使用者的目錄
> * 將 Azure AD B2C 資源新增為 Azure 入口網站中的 [我的最愛]

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 以訂用帳戶或訂用帳戶內的資源群組中至少已獲指派[參與者](../role-based-access-control/built-in-roles.md)角色的 Azure 帳戶登入。

1. 選取包含您訂用帳戶的目錄。

    在入口網站工具列中，選取 [目錄 + 訂用帳戶] 圖示，然後選取包含訂用帳戶的目錄。 此目錄不同於會包含您 Azure AD B2C 租用戶的目錄。

    ![訂用帳戶租用戶、目錄 + 訂用帳戶篩選，且已選取訂用帳戶租用戶](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。
1. 搜尋 [Active Directory B2C]，然後選取 [建立]。
1. 選取 [建立新的 Azure AD B2C 租用戶]。

    ![建立在 Azure 入口網站中選取的新 Azure AD B2C 租用戶](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 在 [建立目錄] 頁面上，輸入下列內容：

   - **組織名稱** - 輸入您 Azure AD B2C 租用戶的名稱。
   - **初始網域名稱** - 輸入您 Azure AD B2C 租用戶的網域名稱。
   - **國家或區域** 從清單中選取您的國家或區域。 稍後無法變更此選取項目。
   - **訂用帳戶** - 從清單中選取您的訂用帳戶。
   - **資源群組** 選取包含租用戶的資源群組。 或是選取 [新建]，輸入資源群組的 [名稱]、選取 [資源群組位置]，然後選取 [確認]。

    ![使用 Azure 入口網站中的範例值建立租用戶表單](media/tutorial-create-tenant/review-and-create-tenant.png)

1. 選取 [檢閱 + 建立]。
1. 檢閱您的目錄設定。 然後選取 [建立]。

您可以將多個 Azure AD B2C 租用戶連結至單一 Azure 訂用帳戶，以供計費之用。 若要連結租用戶，您必須是 Azure AD B2C 租用戶中的系統管理員，且在 Azure 訂用帳戶中至少指派一個參與者角色。 請參閱[將 Azure AD B2C 租用戶連結至訂用帳戶](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)。

## <a name="select-your-b2c-tenant-directory"></a>選取您的 B2C 租用戶目錄

若要開始使用新的 Azure AD B2C 租用戶，您必須切換至包含租用戶的目錄。

在 Azure 入口網站的頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。

如果您一開始在清單中看不到新的 Azure B2C 租用戶，請重新整理瀏覽器視窗，然後在頂端功能表中，再次選取 [目錄 + 訂用帳戶] 篩選。

![已在 Azure 入口網站中選取包含 B2C 租用戶的目錄](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>將 Azure AD B2C 新增為我的最愛 (選擇性)

這個選擇性步驟可讓您更輕鬆地在下列和所有後續教學課程中選取您的 Azure AD B2C 租用戶。

您不需要在每次想要使用租用戶時，搜尋 [所有服務] 中的 Azure AD B2C，而是可以將資源加入我的最愛。 然後，您可以從入口網站功能表的 [我的最愛] 區段中加以選取，以快速瀏覽至您的 Azure AD B2C 租用戶。

您只需要執行這項作業一次。 執行這些步驟之前，請確定您已切換至包含 Azure AD B2C 租用戶的目錄，如上一節[選取您的 B2C 租用戶目錄](#select-your-b2c-tenant-directory)中所述。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站功能表中，選取 [所有服務]。
1. 在 [所有服務] 搜尋方塊中，搜尋 **Azure AD B2C**，將滑鼠停留在搜尋結果上方，然後選取工具提示中的星星圖示。 **Azure AD B2C** 現在會出現在 Azure 入口網站的 [我的最愛] 底下。
1. 如果您想要變更新的我的最愛位置，請移至 Azure 入口網站功能表，選取 [Azure AD B2C]，然後將其向上或向下拖曳至所需的位置。

    ![Azure AD B2C, 我的最愛功能表, Microsoft Azure 入口網站](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換至包含您 Azure AD B2C 租使用者的目錄
> * 將 Azure AD B2C 資源新增為 Azure 入口網站中的 [我的最愛]

接下來，了解如何在新的租用戶中註冊 web 應用程式。

> [!div class="nextstepaction"]
> [註冊您的應用程式 >](tutorial-register-applications.md)
