---
title: 在 Azure AD 權利管理中自行審核存取套件
description: 瞭解如何在 Azure Active Directory 存取權評論 (預覽) 中，檢查權利管理存取套件的使用者存取權。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798438"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中自行審核存取套件

Azure AD 權利管理可簡化企業管理群組、應用程式和 SharePoint 網站存取的方式。 本文說明使用者如何)  (，自行審核其指派的存取套件。

## <a name="open-the-access-review"></a>開啟存取權審核

若要進行存取權檢查，您必須先開啟存取權審核。 使用下列程式來尋找並開啟存取權審核：

1. 您可能會收到 Microsoft 的電子郵件，要求您複習存取權。 找出電子郵件以開啟存取權審核。 以下是要求審查存取權的電子郵件範例： 
    
    ![存取權審核自助審核電子郵件](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 按一下 [ **審核存取** ] 連結。

1. https://myaccess.microsoft.com如果您未收到電子郵件，您也可以直接前往來尋找您的暫止存取權評論。   (適用于美國政府，請 `https://myaccess.microsoft.us` 改用。 ) 

1. 按一下左側導覽列上的 [ **存取審核** ]，以查看指派給您的暫止存取評論清單。


1.  按一下您想要開始的評論。

## <a name="perform-the-access-review"></a>執行存取權審核

開啟存取權審核之後，您就可以看到您的存取權。 使用下列程式來執行存取權審核：

1.  決定您是否仍需要存取存取套件。 例如，您正在處理的專案尚未完成，因此您仍然需要存取權才能繼續處理專案。

1.  按一下 **[是]** 以保留您的存取權，或按一下 [ **否** ] 移除您的存取權。
    >[!NOTE]
    >如果您指出您不再需要存取權，就不會立即從存取套件中移除。 當審核結束或系統管理員停止審核時，您將會從存取套件中移除。

1.  如果您按一下 **[是]**，可能需要在 [ **原因** ] 方塊中包含理由語句。

1.  按一下 [提交]  。

如果您改變主意，並決定在評論結束之前變更您的回應，您可以返回評論。

## <a name="next-steps"></a>後續步驟

- [檢查存取套件的存取權](entitlement-management-access-reviews-review-access.md) 
