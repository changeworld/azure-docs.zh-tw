---
title: Azure AD 應用的連結登錄 - 微軟標識平臺
description: 在 Microsoft 標識平臺 （Azure AD） 中將連結的單一登入 （SSO） 配置到 Azure AD 企業應用程式
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063538"
---
# <a name="configure-linked-sign-on"></a>設定連結的登入

添加庫或非庫 Web 應用程式時，可供您使用的單一登入選項之一是[連結登錄](what-is-single-sign-on.md)。 選擇此選項可向組織的 Azure AD 訪問面板或 Office 365 門戶中的應用程式添加連結。 可以使用此方法向當前使用 Active 目錄聯合服務（或其他識別身分同盟服務）的自訂 Web 應用程式添加連結，而不是 Azure AD 進行身份驗證。 或者，您可以新增特定 SharePoint 網頁或其他只要出現在使用者存取面板上的網頁的深層連結。

## <a name="before-you-begin"></a>開始之前

如果尚未將應用程式新增至您的 Azure AD 租用戶，請參閱[新增資源庫應用程式](add-gallery-app.md)或[新增非資源庫應用程式](add-non-gallery-app.md)。

### <a name="open-the-app-and-select-linked-sign-on"></a>打開應用並選擇連結登錄

1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 導航到**Azure 活動目錄** > **企業應用程式**。 Azure AD 租用戶中應用程式的隨機樣本隨即出現。 

1. 在 [應用程式類型]**** 功能表中，選取 [所有應用程式]****，然後選取 [套用]****。

1. 在搜尋方塊中輸入應用程式的名稱，然後從結果中選取應用程式。

1. 在 [管理]**** 區段中，選取 [單一登入]****。 

1. 選擇**連結**。

1. 輸入要連結到的應用程式的 URL。 鍵入 URL 並選擇 **"保存**"。 
 
1. 您可以將使用者和組分配給應用程式，這將導致應用程式顯示在[Office 365 應用啟動器](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)或針對這些使用者的[Azure AD 訪問面板](end-user-experiences.md)中。

1. 選取 [儲存]****。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [配置自動使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
