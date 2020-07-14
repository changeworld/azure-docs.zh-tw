---
title: 快速入門：檢視使用您 Azure Active Directory (Azure AD) 租用戶進行身分識別管理的應用程式清單
description: 在本快速入門中，您將使用 Azure 入口網站，檢視已註冊為使用您 Azure Active Directory (Azure AD) 租用戶進行身分識別管理的應用程式清單。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249cdbef6214eb8c66da841d9eae817bcf794996
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055387"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>快速入門：檢視使用您 Azure Active Directory (Azure AD) 租用戶進行身分識別管理的應用程式清單

開始使用 Azure AD 作為您組織所用應用程式的身分識別和存取管理 (IAM) 系統。 在本快速入門中，您將會檢視已設定為使用 Azure AD 租用戶作為其身分識別提供者的應用程式 (也稱為 APP)。

## <a name="prerequisites"></a>必要條件

若要檢視已在 Azure AD 租用戶中註冊的應用程式，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。

## <a name="find-the-list-of-applications-in-your-tenant"></a>尋找您租用戶中的應用程式清單

向您 Azure AD 租用戶註冊的應用程式會顯示在 Azure 入口網站的 [企業應用程式] 區段中。

若要檢視在您租用戶中註冊的應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左瀏覽窗格中，選取 [Azure Active Directory]。
3. 在 [Azure Active Directory] 視格中，選取 [企業應用程式]。
4. 從 [應用程式類型] 下拉式清單中，選取 [所有應用程式]，然後選擇 [套用]。 此時會出現租用戶應用程式的隨機樣本。
5. 若要檢視更多應用程式，請選取清單底部的 [載入更多]。 如果您租用戶中有大量應用程式，比起捲動清單進行搜尋，搜尋特定應用程式可能是較輕鬆的方式。 本快速入門稍後會討論特定應用程式的搜尋方式。

## <a name="select-viewing-options"></a>選取檢視選項

根據您要尋找的內容來選取選項。

1. 您可以依照 [應用程式類型]、[應用程式狀態] 和 [應用程式可見度] 檢視應用程式。
2. 在 [應用程式類型] 下方，選擇其中一個選項：
    - [企業應用程式] 會顯示非 Microsoft 應用程式。
    - [Microsoft 應用程式] 會顯示 Microsoft 應用程式。
    - [所有應用程式] 會顯示非 Microsoft 與 Microsoft 應用程式。
3. 在 [應用程式狀態] 下方，選擇 [任何]、[已停用] 或 [已啟用]。 [任何] 選項會包含已停用和已啟用的應用程式。
4. 在 [應用程式可見度] 下方，選擇 [任何]或 [已隱藏]。 [已隱藏] 選項會顯示存在租用戶中，但不會讓使用者看見的應用程式。
5. 選擇您需要的選項之後，請選取 [套用]。

## <a name="search-for-an-application"></a>搜尋應用程式

若要搜尋特定應用程式：

1. 在 [應用程式類型] 功能表中，選取 [所有應用程式]，然後選擇 [套用]。
2. 輸入您要尋找的應用程式名稱。 如果此應用程式已新增至您的 Azure AD 租用戶，搜尋結果中就會出現此應用程式。 此範例顯示 GitHub 尚未新增至租用戶應用程式。
    ![顯示應用程式尚未新增至租用戶的範例](media/view-applications-portal/search-for-tenant-application.png)
3. 嘗試輸入應用程式名稱的前幾個字母。 此範例顯示以 **Sales** 開頭的所有應用程式。
    ![顯示所有應用程式皆以 Sales 開頭的範例](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何檢視您 Azure AD 租用戶中的應用程式。 您已了解如何依據應用程式類型、狀態及可見性來篩選應用程式清單。 您也已了解如何搜尋特定應用程式。

- [新增應用程式](add-application-portal.md)
- [設定應用程式](add-application-portal-configure.md)
- [設定單一登入](add-application-portal-setup-sso.md)
- [刪除應用程式](delete-application-portal.md)
