---
title: 如何在 Azure AD Connect 中自訂同步化規則 | Microsoft Docs
description: 瞭解如何使用同步處理規則編輯器來編輯或建立新的同步處理規則。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530483"
---
# <a name="how-to-customize-a-synchronization-rule"></a>如何自訂同步規則

## <a name="recommended-steps"></a>**建議的步驟**

您可以使用同步化規則編輯器，來編輯或建立新的同步化規則。 您必須是進階使用者，才能變更同步化規則。 任何錯誤的變更都可能導致從您的目標目錄中刪除物件。 請參閱[建議的文件](#recommended-documents)，以獲得有關同步化規則的專業知識。 若要修改同步化規則，請執行下列步驟：

* 從桌面的應用程式功能表中啟動同步化編輯器，如下所示：

    ![同步化規則編輯器功能表](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* 若要自訂預設的同步化規則，在同步化規則編輯器中按一下 [編輯] 按鈕來複製現有的規則，其將會建立標準預設規則的複本，並停用它。 儲存優先順序小於 100 的複製規則。  如果發生屬性流程衝突，優先順序會決定哪個規則在衝突解決中勝出 (較小數值)。

    ![同步處理規則編輯器](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* 修改特定屬性時，在理想情況下，您應該只在複製的規則中保留修改中屬性。  然後啟用預設規則，如此一來，修改的屬性就會來自複製的規則，並從預設標準規則中挑選其他屬性。 

* 請注意，如果複製規則中已修改屬性的計算值為 NULL，而且在預設的標準規則中不為 NULL，則非 NULL 值將會勝出且將取代 NULL 值。 如果您不想使用非 NULL 值來取代 NULL 值，則可在複製的規則中指派 AuthoritativeNull。

* 若要修改**輸出**規則，請在同步化規則編輯器中變更篩選條件。

## <a name="recommended-documents"></a>**建議的文件**
* [Azure AD Connect 同步：技術概念](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect 同步處理：了解架構](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect 同步處理：了解宣告式佈建](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect 同步：瞭解宣告式布建運算式](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect 同步處理：了解預設組態](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect 同步處理：了解使用者、群組和連絡人](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect 同步：陰影屬性](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什麼是混合式身分識別？](whatis-hybrid-identity.md)。