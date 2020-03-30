---
title: 更改 Azure AD 中企業應用程式的名稱或徽標
description: 如何更改 Azure 活動目錄中自訂企業應用程式的名稱或徽標
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138496"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>更改 Azure 活動目錄中企業應用程式的名稱或徽標

在 Azure Active Directory (Azure AD) 中，您可以輕鬆變更自訂企業應用程式的名稱或標誌。 您必須具有適當的許可權才能進行這些更改，並且必須是自訂應用程式的建立者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何更改企業應用程式的名稱或徽標？

1. 使用目錄的全域管理員的帳戶登錄到[Azure 活動目錄門戶](https://aad.portal.azure.com/)。 將顯示**Azure 活動目錄管理中心**頁。
2. 在左側窗格中，選取 [企業應用程式]****。 將顯示企業應用程式的清單。
3. 選取應用程式。 將顯示應用程式概述頁。
4. 在"應用程式概述"窗格中，在 **"管理**"標題下，選擇 **"屬性**"。 將顯示 **"屬性"** 頁。
5. 如果要更改名稱，請選擇"**名稱**"框，鍵入新名稱 **，然後按**Enter 。
6. 如果要更改徽標，請查找**徽標**欄位，然後選擇 **"選擇檔**框"旁邊的資料夾圖示，該資料夾圖示位於應用程式當前徽標圖像下方。

   ![選取 [屬性] 命令](./media/change-name-or-logo-portal/change-logo.png)

   否則，如果不更改徽標，則轉到步驟 8。
7. 在檔選取器中，選擇要作為新徽標的檔。 檔的名稱將顯示在當前徽標圖像下方的框中。

   > [!NOTE]
   > Azure 要求徽標映射為 PNG 檔，並應用寬度、高度和檔案大小的限制。 自訂徽標的大小必須正好為 215 &times; 215 圖元，且採用 PNG 格式。 我們建議您使用應用程式徽標中沒有透明度的純色背景，以便對使用者最好顯示。
8. 選取 [儲存]****。 如果選擇了新徽標，**徽標**欄位的圖像將更改以反映新的徽標檔。

## <a name="next-steps"></a>後續步驟

* [快速入門：在 Azure Active Directory 中檢視貴組織的群組和成員](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
