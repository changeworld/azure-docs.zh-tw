---
title: 如何移除使用者的應用程式存取 | Microsoft Docs
description: 了解如何移除使用者的應用程式存取
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9626c256755e2fce81b593d95b8680f4bb55ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763154"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>如何移除使用者的應用程式存取

本文將協助您了解如何移除使用者的應用程式存取。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我想要移除特定使用者或群組的應用程式指派

若要移除使用者或群組的應用程式指派，請依照[在 Azure Active Directory 中從企業應用程式移除使用者或群組指派](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)文件列出的步驟執行。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>我想要讓每個使用者都無法存取應用程式

若要讓所有使用者都無法登入應用程式，請依照[在 Azure Active Directory 中停用使用者登入企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)文件列出的步驟執行。

## <a name="i-want-to-delete-an-application-entirely"></a>我想要完全刪除應用程式

若要**刪除應用程式**，請遵循下列指示：

1. 開啟[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**或共同管理員身分登入 **。**

2. 按一下左側主導覽功能表底部的 [所有服務]，以開啟 [Azure Active Directory 延伸模組]。

3. 在篩選搜尋方塊中輸入 **“Azure Active Directory**”，然後選取 [Azure Active Directory] 項目。

4. 按一下 Azure Active Directory 左側導覽功能表中的 [**企業應用程式**]。

5. 按一下 [**所有應用程式**] 以查看所有應用程式的清單。

   * 若在這裡沒看到您要顯示的應用程式，請使用 [所有應用程式清單] 頂端的 [篩選] 控制項，並將 [顯示] 選項設定為 [所有應用程式]。

6. 選取您要刪除的應用程式。

7. 應用程式載入後，在頂端應用程式的 [概觀]**** 窗格按一下 [刪除]**** 圖示。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我想要停用任何應用程式的所有未來使用者同意作業

停用整個目錄的使用者同意會阻止使用者同意任何應用程式。 系統管理員仍然可以代表使用者同意。 若要進一步了解應用程式同意，以及您想要或不想這樣做的原因，請參閱[了解使用者和系統管理員同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。 另請參閱[權限及同意](../develop/v2-permissions-and-consent.md)。

若要**停用整個目錄中的所有未來使用者同意作業**，請遵循下列指示：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以**全域管理員**身分登入。

2.  開啟 [Azure Active Directory 擴充功能]**** 

3.  按一下瀏覽功能表中的 [企業應用程式]****。

5.  按一下 [**使用者設定**]。

6.  將 [使用者可以允許應用程式代替他們存取資料]**** 切換開關設為 [否]****，並按一下 [儲存] 按鈕。


## <a name="next-steps"></a>後續步驟

[管理應用程式的存取](what-is-access-management.md)
