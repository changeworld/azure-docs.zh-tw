---
title: 針對 Azure AD Connect 中不會同步處理屬性的問題進行疑難排解 | Microsoft Docs
description: 本主題提供如何使用疑難排解工作，針對屬性同步處理問題進行疑難排解的步驟。
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
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455910"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>針對 Azure AD Connect 中不會同步處理屬性的問題進行疑難排解

## <a name="recommended-steps"></a>**建議的步驟**

調查屬性同步處理問題之前，讓我們先了解 **Azure AD Connect** 同步處理程序：

  ![Azure AD Connect 同步處理程序](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**術語**

* **CS：** 連接器空間，資料庫中的表。
* **MV：** Metaverse，資料庫中的一個表。
* **廣告：** 活動目錄
* **AAD：** Azure 活動目錄

### <a name="synchronization-steps"></a>**同步步驟**

* 從 AD 導入：活動目錄物件將帶入 AD CS。

* 從 AAD 導入：Azure 活動目錄物件將引入 AAD CS。

* 同步：**入站同步規則**和**出站同步規則**按優先順序數從低到高的順序運行。 若要檢視同步化規則，您可以從桌面應用程式移至**同步化規則編輯器**。 **輸入同步化規則**會從 CS 將資料帶入到 MV。 **輸出同步化規則**會從 MV 將資料帶入到 CS。

* 匯出到 AD：運行同步後，物件將從 AD CS 匯出到**活動目錄**。

* 匯出到 AAD：運行同步後，物件將從 AAD CS 匯出到**Azure 活動目錄**。

### <a name="step-by-step-investigation"></a>**逐步進行調查**

* 我們一開始將搜尋 **Metaverse**，並查看從來源到目標的屬性對應。

* 從桌面應用程式啟動 [Synchronization Service Manager]****，如下所示：

  ![啟動 Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* 在 [Synchronization Service Manager]**** 上，依序選取 [Metaverse 搜尋]**** 和 [依物件類型的範圍]****、使用屬性選取物件，然後按一下 [搜尋]**** 按鈕。

  ![Metaverse 搜尋](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* 按兩下在 **Metaverse** 搜尋中找到的物件，以檢視其所有屬性。 您可以按一下 [連接器]**** 索引標籤，以查看所有**連接器空間**中的對應物件。

  ![Metaverse 物件連接器](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* 按兩下 **Active Directory 連接器**以檢視**連接器空間**屬性。 按一下 [預覽]**** 按鈕，在下列對話方塊上，按一下 [產生預覽]**** 按鈕。

  ![連接器空間屬性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* 現在，按一下 [匯入屬性流程]****，這會顯示從 **Active Directory 連接器空間**到 **Metaverse** 的屬性流程。 [同步化規則]**** 資料行會顯示已將哪些**同步化規則**提供給該屬性。 [資料來源]**** 資料行會顯示來自**連接器空間**的屬性。 [Metaverse 屬性]**** 資料行會顯示 **Metaverse** 中的屬性。 您可以在此處查看未同步處理的屬性。 如果您在此處找不到屬性，則這不會對應，而您必須建立新的自訂**同步化規則**來對應屬性。

  ![連接器空間屬性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* 按一下左窗格中的 [匯出屬性流程]****，以使用**輸出同步化規則**來檢視從 **Metaverse** 回到 **Active Directory 連接器空間**的屬性流程。

  ![連接器空間屬性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* 同樣地，您可以檢視 **Azure Active Directory 連接器空間**物件，並且可以產生**預覽**來檢視從 **Metaverse** 到**連接器空間**的屬性流程，反之亦然，如此一來，您就能調查屬性不會同步處理的原因。

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
