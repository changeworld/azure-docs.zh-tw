---
title: 將屬性同步處理至 Azure AD 以進行對應
description: 瞭解如何將內部部署 Active Directory 的屬性同步處理至 Azure AD。 設定 SaaS 應用程式的使用者布建時，請使用目錄擴充功能來新增預設未同步處理的來源屬性。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 00c4dec329456409bc8d5b77dca72f25daf9f5c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781068"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>將內部部署 Active Directory 的屬性同步處理至 Azure AD 以布建至應用程式

自訂使用者布建的屬性對應時，您可能會發現您想要對應的屬性不會出現在 [**來源屬性**] 清單中。 本文說明如何藉由從內部部署 Active Directory （AD）同步處理至 Azure Active Directory （Azure AD），來新增遺漏的屬性。

從 Azure AD 將使用者帳戶布建到 SaaS 應用程式時，Azure AD 必須包含建立使用者設定檔所需的所有資料。 在某些情況下，若要讓資料可供使用，您可能需要將內部部署 AD 的屬性同步處理至 Azure AD。 Azure AD Connect 會自動將特定屬性同步處理至 Azure AD，但不是所有屬性。 此外，某些預設會同步處理的屬性（例如 SAMAccountName）可能不會使用 Microsoft Graph API 來公開。 在這些情況下，您可以使用 Azure AD Connect 目錄擴充功能，將屬性同步處理至 Azure AD。 如此一來，Microsoft Graph API 和 Azure AD 布建服務就會看到此屬性。

如果您需要布建的資料在 Active Directory 中，但因為上述原因而無法提供布建，請遵循下列步驟。
 
## <a name="sync-an-attribute"></a>同步處理屬性 

1. 開啟 [Azure AD Connect wizard]，選擇 [工作]，然後選擇 [**自訂同步處理選項**]。

   ![Azure Active Directory Connect wizard 其他工作] 頁面](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全域管理員身分登入。 

3. 在 [**選用功能**] 頁面上，選取 [**目錄擴充屬性同步**處理]。
 
   ![Azure Active Directory Connect wizard 選用功能頁面](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 選取您想要擴充以 Azure AD 的屬性。
   > [!NOTE]
   > [**可用屬性**] 下的搜尋會區分大小寫。

   ![Azure Active Directory Connect wizard 目錄延伸選取頁面](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD Connect wizard，並允許執行完整的同步處理迴圈。 當迴圈完成時，會擴充架構，並在您的內部部署 AD 與 Azure AD 之間同步處理新的值。
 
6. 在 Azure 入口網站中，當您要[編輯使用者屬性](customize-application-attributes.md)對應時，[**來源屬性**] 清單現在會包含以格式加入的屬性 `<attributename> (extension_<appID>_<attributename>)` 。 選取屬性，並將其對應至目標應用程式以進行布建。

   ![Azure Active Directory Connect wizard 目錄延伸選取頁面](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 目前不支援從內部部署 AD （例如**managedby**或**DN/DistinguishedName**）布建參考屬性的功能。 您可以在[User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)上要求這項功能。 

## <a name="next-steps"></a>後續步驟

* [定義布建範圍中的人員](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
