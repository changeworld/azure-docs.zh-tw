---
title: Azure AD 應用程式的連結登入-Microsoft 身分識別平臺
description: 在 Microsoft 身分識別平臺（Azure AD）中，為您的 Azure AD 企業應用程式設定已連結的單一登入（SSO）
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763477"
---
# <a name="configure-linked-sign-on"></a>設定連結的登入

當您新增資源庫或非資源庫 web 應用程式時，您可以使用的其中一個單一登入選項為 [已連結的登[入](what-is-single-sign-on.md)]。 選取此選項可將連結新增至您組織的 Azure AD 存取面板或 Office 365 入口網站中的應用程式。 您可以使用這個方法，將連結新增至目前使用 Active Directory 同盟服務（或其他同盟服務）的自訂 web 應用程式，而不是 Azure AD 進行驗證。 或者，您可以新增特定 SharePoint 網頁或其他只要出現在使用者存取面板上的網頁的深層連結。

## <a name="before-you-begin"></a>開始之前

如果尚未將應用程式新增至您的 Azure AD 租用戶，請參閱[新增資源庫應用程式](add-gallery-app.md)或[新增非資源庫應用程式](add-non-gallery-app.md)。

### <a name="open-the-app-and-select-linked-sign-on"></a>開啟應用程式，然後選取 [連結的登入]

1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 流覽至**Azure Active Directory**  >  **企業應用程式**。 Azure AD 租用戶中應用程式的隨機樣本隨即出現。 

1. 在 [應用程式類型]**** 功能表中，選取 [所有應用程式]****，然後選取 [套用]****。

1. 在搜尋方塊中輸入應用程式的名稱，然後從結果中選取應用程式。

1. 在 [管理] 區段中，選取 [單一登入]。 

1. 選取 [**連結**]。

1. 輸入要連結之應用程式的 URL。 輸入 URL，然後選取 [**儲存**]。 
 
1. 您可以將使用者和群組指派給應用程式，這會導致應用程式出現在[Office 365 應用程式啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或適用于這些使用者的[Azure AD 存取面板](end-user-experiences.md)中。

1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
