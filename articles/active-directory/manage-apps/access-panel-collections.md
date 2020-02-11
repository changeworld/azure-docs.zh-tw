---
title: 在 Azure Active Directory 中建立我的應用程式入口網站的集合 |Microsoft Docs
description: 使用我的應用程式的集合，為您的終端使用者自訂我的應用程式頁面，以提供更簡單的我的應用程式體驗。 將應用程式組織成具有不同索引標籤的群組。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120092"
---
# <a name="create-collections-on-the-my-apps-portal"></a>在我的應用程式入口網站上建立集合

您的使用者可以使用我的應用程式入口網站來查看和啟動他們可以存取的雲端式應用程式。 根據預設，使用者可以存取的所有應用程式會在單一頁面上一起列出。 若要為您的使用者提供更好的組織此頁面，如果您有 Azure AD Premium P1 或 P2 授權，您可以設定集合。 使用集合時，您可以將相關的應用程式群組在一起（例如，依作業角色、工作或專案），並將它們顯示在不同的索引標籤上。集合基本上會將篩選套用至使用者可以存取的應用程式，因此使用者只會看到集合中已指派給他們的應用程式。

> [!NOTE]
> 本文涵蓋系統管理員如何啟用和建立集合。 如需有關如何使用我的應用程式入口網站和集合的使用者資訊，請參閱[存取和使用集合](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-the-latest-my-apps-features"></a>啟用最新的我的應用程式功能

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，然後以使用者系統管理員或全域管理員身分登入。

2. 移至**Azure Active Directory** > **使用者設定**。

3. 在 [**使用者功能預覽**] 底下，選取 [**管理使用者功能預覽設定**]。

4. 在 [**使用者可以使用預覽功能進行我的應用程式**] 下，選擇下列其中一個選項：
   * 已**選取**-啟用特定群組的功能。 使用 [**選取群組**] 選項，選取您要啟用功能的群組。  
   * **全部**-啟用所有使用者的功能。

> [!NOTE]
> 若要開啟我的應用程式入口網站，使用者可以使用連結 `https://myapps.microsoft.com` 或您組織的自訂連結，例如 `https://myapps.microsoft.com/contoso.com`。 當您啟用新的我的應用程式體驗之後，[已**更新的我的應用程式體驗] 就**會顯示在 [我的應用程式] 頁面頂端，使用者可以選取 [**試試看**] 來查看新的體驗。 若要停止使用新的體驗，使用者可以從頁面頂端的 [**離開新體驗**] 橫幅選取 **[是]** 。

## <a name="create-a-collection"></a>建立集合

若要建立集合，您必須擁有 Azure AD Premium P1 或 P2 授權。

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以具有 Azure AD Premium P1 或 P2 授權的系統管理員身分登入。

2. 移至**Azure Active Directory** > **企業應用程式**。

3. 在 [**管理**] 底下，選取 [**集合**]。

4. 選取 [**新增集合**]。 在 [**新增集合**] 頁面上，輸入集合的**名稱**（我們建議您不要在名稱中使用 "collection"。 然後輸入**描述**。

   ![[新增集合] 頁面](media/acces-panel-collections/new-collection.png)

5. 選取 [**應用程式**] 索引標籤。選取 [ **+ 新增應用程式**]，然後在 [**新增應用**程式] 頁面中，選取您要新增至集合的所有應用程式，或使用 [**搜尋**] 方塊來尋找應用程式。

   ![將應用程式新增至集合](media/acces-panel-collections/add-applications.png)

6. 當您完成新增應用程式時，請選取 [**新增**]。 選取的應用程式清單隨即出現。 您可以使用向上箭號來變更清單中的應用程式順序。 若要將應用程式向下移動或從集合中刪除，請選取 [**更多**] 功能表（ **...** ）。

7. 選取 [**擁有**者] 索引標籤。選取 [ **+ 新增使用者和群組**]，然後在 [**新增使用者和群組**] 頁面中，選取您想要指派擁有權的使用者或群組。 當您完成選取 [使用者和群組] 時，請選擇 [**選取**]。

9. 選取 [**使用者和群組**] 索引標籤。選取 [ **+ 新增使用者和群組**]，然後在 [**新增使用者和群組**] 頁面中，選取您想要指派集合的使用者或群組。 或者，使用 [**搜尋**] 方塊來尋找使用者或群組。 當您完成選取 [使用者和群組] 時，請選擇 [**選取**]。

   ![新增使用者和群組](media/acces-panel-collections/add-users-and-groups.png)

11. 選取 [檢閱 + 建立]。 新集合的屬性隨即出現。


## <a name="view-audit-logs"></a>檢視稽核記錄

「Audit 記錄」會記錄我的應用程式集合作業，包括集合建立的使用者動作。 從我的應用程式產生下列事件：

* 建立集合
* 編輯集合
* 刪除集合
* 啟動應用程式（終端使用者）
* 自助應用程式新增（終端使用者）
* 自助式應用程式刪除（終端使用者）

您可以在 [活動] 區段中選取 [ **Azure Active Directory** > **企業應用程式**] > [**審核記錄**]，以存取[Azure 入口網站](https://portal.azure.com)中的 audit 記錄。 針對 [**服務**]，選取 [**我的應用程式**]。

## <a name="get-support-for-my-account-pages"></a>取得我的帳戶頁面的支援

在 [我的應用程式] 頁面上，使用者可以**選取 [我的帳戶]**  > [**查看我的帳戶**] 來開啟其帳戶設定。 在 [Azure AD**我的帳戶**] 頁面上，使用者可以管理他們的安全性資訊、裝置和密碼等。 他們也可以存取其 Office 帳戶設定。

如果您需要在 Azure AD 帳戶 頁面或 Office 帳戶 頁面上提交問題的支援要求，請遵循下列步驟，以正確地路由傳送您的要求： 

* 如有 Azure AD [**我的帳戶]** 頁面的問題，請從 Azure 入口網站內開啟支援要求。 請移至**Azure 入口網站** > **Azure Active Directory** > **新的支援要求**。

* 對於**Office [我的帳戶]** 頁面的問題，請從 Microsoft 365 系統管理中心內開啟支援要求。 請移至**Microsoft 365 系統管理中心** > **支援**。 

## <a name="next-steps"></a>後續步驟
[Azure Active Directory 中應用程式的使用者體驗](end-user-experiences.md)