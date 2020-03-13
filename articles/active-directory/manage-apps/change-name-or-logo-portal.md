---
title: 在 Azure AD 中變更企業應用程式的名稱或標誌
description: 如何在 Azure Active Directory 中變更自訂企業應用程式的名稱或標誌
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138496"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>在 Azure Active Directory 中變更企業應用程式的名稱或標誌

在 Azure Active Directory (Azure AD) 中，您可以輕鬆變更自訂企業應用程式的名稱或標誌。 您必須具有適當的許可權，才能進行這些變更，而且您必須是自訂應用程式的建立者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何? 變更企業應用程式的名稱或標誌嗎？

1. 使用具有目錄全域管理員身分的帳戶來登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [ **Azure Active Directory 系統管理中心**] 頁面隨即出現。
2. 在左側窗格中，選取 [企業應用程式]。 您的企業應用程式清單隨即出現。
3. 選取應用程式。 [應用程式總覽] 頁面隨即出現。
4. 在 [應用程式總覽] 窗格的 [**管理**] 標題底下，選取 [**屬性**]。 [**屬性**] 頁面隨即出現。
5. 如果您想要變更名稱，請選取 [**名稱**] 方塊，輸入新名稱，然後按**enter**。
6. 如果您想要變更標誌，請尋找 [**標誌**] 欄位，然後選取 [**選取**檔案] 方塊旁的資料夾圖示，此方塊位於應用程式目前的標誌影像下方。

   ![選取 [屬性] 命令](./media/change-name-or-logo-portal/change-logo.png)

   否則，如果您沒有變更標誌，請移至步驟8。
7. 在檔案選擇器中，選取您想要作為新標誌的檔案。 檔案的名稱會出現在目前標誌影像下方的方塊中。

   > [!NOTE]
   > Azure 要求標誌影像必須是 PNG 檔案，而且會套用寬度、高度和檔案大小的限制。 自訂標誌的大小必須剛好為 215 &times; 215 圖元，且格式為 PNG。 我們建議您在應用程式標誌中使用純色背景，而不會出現透明度，使其最適合使用者。
8. 選取 [儲存]。 如果您選擇新的標誌，**標誌**欄位的影像會變更，以反映新的標誌檔案。

## <a name="next-steps"></a>後續步驟

* [快速入門：在 Azure Active Directory 中查看貴組織的群組和成員](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
