---
title: 將屬性同步到 Azure AD 進行映射 |微軟文檔
description: 瞭解如何將屬性從本地活動目錄同步到 Azure AD。 將使用者預配配置為 SaaS 應用時，請使用目錄擴展功能添加預設情況下未同步的源屬性。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522266"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>將屬性從本地活動目錄同步到 Azure AD 以預配到應用程式

為使用者預配自訂屬性對應時，您可能會發現要映射的屬性不會出現在 **"源"屬性**清單中。 本文介紹如何通過將缺少的屬性從本地活動目錄 （AD） 同步到 Azure 活動目錄 （Azure AD） 來添加它。

在將使用者帳戶從 Azure AD 預配到 SaaS 應用時，Azure AD 必須包含創建使用者設定檔所需的所有資料。 在某些情況下，要使資料可用，可能需要將屬性從本地 AD 同步到 Azure AD。 Azure AD Connect 會自動將某些屬性同步到 Azure AD，但不是所有屬性。 此外，預設情況下同步的某些屬性（如 SAMAccountName）可能不會使用 Microsoft 圖形 API 公開。 在這些情況下，可以使用 Azure AD 連接目錄擴展功能將屬性同步到 Azure AD。 這樣，該屬性將對 Microsoft 圖形 API 和 Azure AD 預配服務可見。

如果需要預配的資料位於 Active Directory 中，但由於上述原因無法進行預配，請按照以下步驟操作。
 
## <a name="sync-an-attribute"></a>同步屬性 

1. 打開 Azure AD 連接嚮導，選擇任務，然後選擇 **"自訂同步"選項**。

   ![Azure 活動目錄連接嚮導 其他任務頁](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全域管理員身份登錄。 

3. 在 **"可選功能"** 頁上，選擇**目錄擴充屬性同步**。
 
   ![Azure 活動目錄連接嚮導 可選功能頁](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 選擇要擴展到 Azure AD 的屬性。
   > [!NOTE]
   > **"可用屬性**"下的搜索區分大小寫。

   ![Azure 活動目錄連接嚮導目錄延伸選取頁](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD 連接嚮導並允許運行完整的同步週期。 週期完成後，架構將擴展，並在本地 AD 和 Azure AD 之間同步新值。
 
6. 在 Azure 門戶中，在[編輯使用者屬性對應](customize-application-attributes.md)時，**源屬性**清單現在將包含格式`<attributename> (extension_<appID>_<attributename>)`中添加的屬性。 選擇該屬性並將其映射到目標應用程式進行預配。

   ![Azure 活動目錄連接嚮導目錄延伸選取頁](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 目前不支援從本地 AD（如**託管資料**或**DN/分辨名稱**）預配引用屬性的功能。 您可以在[使用者語音](https://feedback.azure.com/forums/169401-azure-active-directory)上請求此功能。 

## <a name="next-steps"></a>後續步驟

* [定義誰在預配範圍內](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
