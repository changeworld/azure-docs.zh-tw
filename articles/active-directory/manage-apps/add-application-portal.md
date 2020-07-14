---
title: 快速入門：將應用程式新增到您的 Azure Active Directory (Azure AD) 租用戶
description: 本快速入門會使用 Azure 入口網站，將資源庫應用程式新增至 Azure Active Directory (Azure AD) 租用戶。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956095"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>快速入門：將應用程式新增到您的 Azure Active Directory (Azure AD) 租用戶

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的許多應用程式可能已存在資源庫中。 本快速入門會使用 Azure 入口網站，將資源庫應用程式新增至 Azure AD 租用戶。 本快速入門著重於新增已是資源庫一部份的應用程式。 未包含在資源庫中的應用程式也可以與 Azure AD 整合，但本快速入門並未涵蓋此部分。 

將應用程式新增至 Azure AD 租用戶之後，您可以：

- 設定應用程式的屬性。
- 使用條件式存取原則來管理應用程式的使用者存取。
- 設定單一登入，讓使用者可以使用其 Azure AD 認證來登入應用程式。

## <a name="prerequisites"></a>必要條件

若要將應用程式新增至 Azure AD 租用戶，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- (選擇性：完成[檢視您的應用程式](view-applications-portal.md))。

>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。

## <a name="add-an-app-to-your-azure-ad-tenant"></a>將應用程式新增至您的 Azure AD 租用戶

將資源庫應用程式新增至 Azure AD 租用戶：

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]。
2. 在 [Azure Active Directory] 視格中，選取 [企業應用程式]。 [所有應用程式] 窗格會隨即開啟，並顯示您 Azure AD 租用戶中應用程式的隨機樣本。
3. 在 [企業應用程式] 窗格上，選取 [新增應用程式]。 
    ![選取 [新增應用程式] 以將資源庫應用程式新增至您的租用戶](media/add-application-portal/new-application.png)
4. 切換至新的資源庫預覽體驗：在 [新增應用程式] 頁面頂端的橫幅中，選取顯示為 [按一下這裡試用改良過的新版應用程式資源庫] 的連結。
5. [瀏覽 Azure AD 資源庫 (預覽)] 窗格隨即開啟，並顯示雲端平台、內部部署應用程式和精選應用程式的圖格。 [精選應用程式] 區段中所列的應用程式，會有圖示指出其是否支援同盟單一登入 (SSO) 和佈建。
    ![依名稱或類別搜尋應用程式](media/add-application-portal/browse-gallery.png)
6. 您可以瀏覽您要新增之應用程式的資源庫，或在搜尋方塊中輸入應用程式的名稱加以搜尋。 然後，從結果中選取應用程式。 在表單中，您可以編輯應用程式名稱，以符合您的組織需求。 在此範例中，我們已將名稱變更為 **GitHub-test**。
    ![說明如何從資源庫新增應用程式](media/add-application-portal/create-application.png)
7. 選取 [建立]。 [開始使用] 頁面會隨即顯示，其中包含可為您組織設定應用程式的選項。

您已完成新增應用程式。 下一個快速入門將示範如何為應用程式變更標誌和編輯其他屬性。

## <a name="next-steps"></a>後續步驟

- [設定應用程式](add-application-portal-configure.md)
- [設定單一登入](add-application-portal-setup-sso.md)
- [刪除應用程式](delete-application-portal.md)