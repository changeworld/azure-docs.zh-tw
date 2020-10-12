---
title: 在 Azure Active Directory 中建立我的應用程式入口網站的集合 |Microsoft Docs
description: 使用我的應用程式集合為您的終端使用者自訂我的應用程式頁面，以提供更簡單的我的應用程式體驗。 使用不同的索引標籤將應用程式組織成群組。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f520141d36726e94dc8d49d7e5aa95bb35d5484
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85956231"
---
# <a name="create-collections-on-the-my-apps-portal"></a>在我的應用程式入口網站上建立集合

您的使用者可以使用我的應用程式入口網站來查看並啟動他們有權存取的雲端式應用程式。 依預設，使用者可以存取的所有應用程式都會在單一頁面上一起列出。 若要為您的使用者更妥善地組織此頁面，如果您有 Azure AD Premium P1 或 P2 授權，您可以設定集合。 您可以使用集合將 (相關的應用程式群組在一起，例如，依工作角色、工作或專案) ，並將它們顯示在不同的索引標籤上。集合基本上會將篩選套用至使用者已可存取的應用程式，因此使用者只會看到集合中已指派給他們的應用程式。

> [!NOTE]
> 本文涵蓋系統管理員如何啟用和建立集合。 如需有關如何使用我的應用程式入口網站和集合的終端使用者資訊，請參閱 [存取和使用集合](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-the-latest-my-apps-features"></a>啟用最新的我的應用程式功能

1. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以使用者系統管理員或全域管理員身分登入。

2. 移至**Azure Active Directory**  >  **使用者設定**]。

3. 在 [ **使用者功能預覽**] 下，選取 [ **管理使用者功能預覽設定**]。

4. 在 [ **使用者可以使用預覽功能進行我的應用程式**] 下，選擇下列其中一個選項：
   * 已**選取**-啟用特定群組的功能。 使用 [ **選取群組** ] 選項，即可選取您要啟用功能的群組。  
   * **全部** -啟用所有使用者的功能。

> [!NOTE]
> 若要開啟我的應用程式入口網站，使用者可以 `https://myapps.microsoft.com` 針對您的組織使用連結或自訂連結，例如 `https://myapps.microsoft.com/contoso.com` 。 當您啟用新的我的應用程式體驗之後，[已 **更新的我的應用程式] 體驗就** 會顯示在 [我的應用程式] 頁面頂端，使用者可以選取 [ **試用** ] 以查看新的體驗。 若要停止使用新的體驗，使用者可以從頁面頂端的 [**離開新體驗**] 橫幅中選取 **[是]** 。

## <a name="create-a-collection"></a>建立集合

若要建立集合，您必須有 Azure AD Premium P1 或 P2 授權。

1. 開啟 [**Azure 入口網站**](https://portal.azure.com/) ，然後以具有 Azure AD Premium P1 或 P2 授權的系統管理員身分登入。

2. 移至**Azure Active Directory**  >  **企業應用程式**。

3. 在 [ **管理**] 底下，選取 [ **集合**]。

4. 選取 [ **新增集合**]。 在 [ **新增集合** ] 頁面中，輸入集合的 **名稱** (我們建議不要在名稱中使用 "collection"。 然後輸入 **描述**。

   ![[新增集合] 頁面](media/acces-panel-collections/new-collection.png)

5. 選取 [ **應用程式** ] 索引標籤。選取 [ **+ 新增應用程式**]，然後在 [ **新增應用程式** ] 頁面中，選取您要新增至集合的所有應用程式，或使用 [ **搜尋** ] 方塊來尋找應用程式。

   ![將應用程式新增至集合](media/acces-panel-collections/add-applications.png)

6. 當您完成新增應用程式時，請選取 [ **新增**]。 選取的應用程式清單隨即出現。 您可以使用向上箭號來變更清單中的應用程式順序。 若要向下移動應用程式或將它從集合中刪除，請選取 [ **更多** ] 功能表 (**...**) 。

7. 選取 [ **擁有** 者] 索引標籤。選取 [ **+ 新增使用者和群組**]，然後在 [ **新增使用者和群組** ] 頁面中，選取您要指派擁有權的使用者或群組。 當您完成選取使用者和群組時，請選擇 [ **選取**]。

9. 選取 [ **使用者和群組** ] 索引標籤。選取 [ **+ 新增使用者和群組**]，然後在 [ **新增使用者和群組** ] 頁面中，選取您要指派集合的使用者或群組。 或使用 [ **搜尋** ] 方塊來尋找使用者或群組。 當您完成選取使用者和群組時，請選擇 [ **選取**]。

   ![新增使用者和群組](media/acces-panel-collections/add-users-and-groups.png)

11. 選取 [檢閱 + 建立]  。 新集合的屬性隨即出現。


## <a name="view-audit-logs"></a>檢視稽核記錄

Audit 記錄會記錄我的應用程式的集合作業，包括集合建立終端使用者動作。 以下是從我的應用程式產生的事件：

* 建立集合
* 編輯集合
* 刪除集合
*  (終端使用者啟動應用程式) 
* 新增 (終端使用者) 的自助應用程式
*  (終端使用者) 的自助式應用程式刪除

您可以在[Azure portal](https://portal.azure.com) **Azure Active Directory**  >  [活動] 區段中選取 [**企業應用程式**  >  **審核記錄**] Azure Active Directory，以存取 Azure 入口網站中的 audit 記錄檔。 針對 [ **服務**]，選取 [ **我的應用程式**]。

## <a name="get-support-for-my-account-pages"></a>取得我的帳戶頁面的支援

在 [我的應用程式] 頁面中，使用者可以選取 [**我的帳戶**]  >  **View 我的帳戶**來開啟其帳戶設定。 在 [Azure AD **我的帳戶** ] 頁面上，使用者可以管理其安全性資訊、裝置、密碼等等。 他們也可以存取其 Office 帳戶設定。

如果您需要在 Azure AD 帳戶] 頁面或 [Office 帳戶] 頁面上提交問題的支援要求，請遵循下列步驟，以正確地路由傳送您的要求： 

* 針對 Azure AD 的 [ **我的帳戶]** 頁面的問題，請從 Azure 入口網站內開啟支援要求。 前往**Azure 入口網站**  >  **Azure Active Directory**  >  **新的支援要求**。

* 針對 **Office [我的帳戶]** 頁面的問題，請在 Microsoft 365 系統管理中心中開啟支援要求。 移至**Microsoft 365 系統管理中心**  >  **支援服務**。 

## <a name="next-steps"></a>接下來的步驟
[Azure Active Directory 中的應用程式使用者體驗](end-user-experiences.md)