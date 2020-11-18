---
title: 如何在 Azure Active Directory 中移除使用者對應用程式的存取權
description: 瞭解如何在 Azure Active Directory 中移除使用者對應用程式的存取權
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: 28b31d98f283dc957927ab2a35f0ab95bf066473
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654111"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>如何移除使用者的應用程式存取

本文將協助您了解如何移除使用者的應用程式存取。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我想要移除特定使用者或群組的應用程式指派

若要移除使用者或群組的應用程式指派，請依照[在 Azure Active Directory 中從企業應用程式移除使用者或群組指派](./assign-user-or-group-access-portal.md)文件列出的步驟執行。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>我想要讓每個使用者都無法存取應用程式

若要讓所有使用者都無法登入應用程式，請依照[在 Azure Active Directory 中停用使用者登入企業應用程式](./disable-user-sign-in-portal.md)文件列出的步驟執行。

## <a name="i-want-to-delete-an-application-entirely"></a>我想要完全刪除應用程式

[應用程式管理的快速入門系列](delete-application-portal.md)包含從 Azure Active Directory 租使用者刪除應用程式的指引。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我想要停用任何應用程式的所有未來使用者同意作業

停用整個目錄的使用者同意會阻止使用者同意任何應用程式。 系統管理員仍然可以代表使用者同意。 若要進一步了解應用程式同意，以及您想要或不想這樣做的原因，請參閱[了解使用者和系統管理員同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。 另請參閱[權限及同意](../develop/v2-permissions-and-consent.md)。

若要 **停用整個目錄中的所有未來使用者同意作業**，請遵循下列指示：

1.  開啟 [Azure 入口網站](https://portal.azure.com/)，以 **全域管理員** 身分登入。

2.  開啟 [Azure Active Directory 擴充功能] 

3.  按一下瀏覽功能表中的 [企業應用程式]。

5.  按一下 [ **使用者設定**]。

6.  將 [使用者可以允許應用程式代替他們存取資料] 切換開關設為 [否]，並按一下 [儲存] 按鈕。


## <a name="next-steps"></a>後續步驟

[管理應用程式的存取](what-is-access-management.md)