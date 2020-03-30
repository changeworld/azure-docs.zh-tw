---
title: 如何在 Azure AD Connect 中自訂同步化規則 | Microsoft Docs
description: 本主題提供對安裝 Azure AD Connect 的問題進行疑難排解的步驟。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351035"
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

## <a name="recommended-documents"></a>**推薦檔**
* [Azure AD 連接同步：技術概念](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect 同步處理：了解架構](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect 同步處理：了解宣告式佈建](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD 連接同步：瞭解聲明預配運算式](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect 同步處理：了解預設組態](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect 同步處理：了解使用者、群組和連絡人](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD 連接同步：陰影屬性](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什麼是混合式身分識別？](whatis-hybrid-identity.md)。
