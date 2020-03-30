---
title: Azure AD 中企業應用的使用者預配管理
description: 了解如何使用 Azure Active Directory 管理企業應用程式的使用者帳戶佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264124"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 入口網站中管理企業應用程式的使用者帳戶佈建

本文介紹了管理支援自動使用者帳戶預配和取消預配的一般步驟。 *使用者帳戶佈建*是指在應用程式的本機使用者設定檔儲存中建立、更新及/或停用使用者帳戶記錄的動作。 大多數雲和 SaaS 應用程式在使用者自己的本機使用者設定檔存儲中存儲使用者角色和許可權，並且單次登錄和訪問工作*需要*在使用者的本機存放區中存在此類使用者記錄。 要瞭解有關自動使用者帳戶預配的更多資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配到 SaaS 應用程式](user-provisioning.md)。

> [!IMPORTANT]
> Azure 活動目錄 （Azure AD） 具有包含數千個預集成應用程式，這些應用程式已啟用以自動預配 Azure AD。 您應該首先在[有關如何將 SaaS 應用與 Azure 活動目錄集成的教程清單中](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)找到特定于應用程式的預配設置教程。 您可能會找到用於配置應用和 Azure AD 以創建預配連接的分步指南。

## <a name="finding-your-apps-in-the-portal"></a>在入口網站中尋找您的應用程式

使用 Azure 活動目錄門戶查看和管理為目錄中的單一登入配置的所有應用程式。 企業應用程式是您組織內部署和使用的應用程式。 按照以下步驟查看和管理企業應用程式：

1. 打開[Azure 活動目錄門戶](https://aad.portal.azure.com)。
1. 從左側窗格中選擇**企業應用程式**。 將顯示所有已配置應用的清單，包括從庫中添加的應用。
1. 選擇任何應用以載入其資源窗格，您可以在其中查看報表和管理應用設置。
1. 選擇 **"預配**"以管理所選應用的使用者帳戶預配設置。

   ![預配螢幕以管理使用者帳戶預配設置](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>佈建模式

**預配**窗格從 **"模式"** 功能表開始，該功能表顯示企業應用程式支援的預配模式，並允許您對其進行配置。 可用的選項包括：

* **自動**- 如果 Azure AD 支援自動基於 API 的預配或取消向此應用程式的使用者帳戶預配，則顯示此選項。 選擇此模式可顯示可説明管理員的介面：

  * 將 Azure AD 配置為連接到應用程式的使用者管理 API
  * 創建帳戶映射和工作流，以定義使用者帳戶資料應在 Azure AD 和應用之間流動的方式
  * 管理 Azure AD 預配服務

* **手動**- 如果 Azure AD 不支援自動預配到此應用程式的使用者帳戶，則顯示此選項。 在這種情況下，應用程式中存儲的使用者客戶紀錄必須使用外部進程進行管理，具體取決於該應用程式提供的使用者管理和預配功能（其中可能包括 SAML 即時預配）。

## <a name="configuring-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

選擇 **"自動"** 選項可指定管理員憑據、映射、啟動和停止以及同步的設置。

### <a name="admin-credentials"></a>管理員認證

展開**管理認證**以輸入 Azure AD 連接到應用程式的使用者管理 API 所需的憑據。 所需的輸入依應用程式而有所不同。 若要深入了解認證類型，以及針對特定應用程式的需求，請參閱 [針對該特定應用程式的設定教學課程](user-provisioning.md)。

選擇 **"測試連接**"可讓 Azure AD 嘗試使用提供的憑據連接到應用的預配應用，以測試憑據。

### <a name="mappings"></a>對應

展開**映射**，以查看和編輯在預配或更新使用者帳戶時在 Azure AD 和目標應用程式之間流動的使用者屬性。

Azure AD 使用者物件和每個 SaaS 應用的使用者物件之間有一組預先配置的映射。 有些 app 則管理其他類型的物件，例如群組或連絡人。 在表中選擇映射以向右打開映射編輯器，您可以在其中查看和自訂它們。

![顯示內容映射螢幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支援的自訂項目包含：

* 啟用和停用特定物件的對應，例如 Azure AD 使用者物件對應至 SaaS 應用程式的使用者物件。
* 編輯會從 Azure AD 使用者物件流向應用程式使用者物件的屬性。 如需有關屬性對應的詳細資訊，請參閱 [了解屬性對應類型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 篩選 Azure AD 在目標應用程式上運行的預配操作。 您可以限制運行操作，而不是讓 Azure AD 完全同步物件。

  例如，僅選擇 **"更新**"和"Azure AD"僅更新應用程式中的現有使用者帳戶，但不會創建新使用者帳戶。 僅選擇 **"創建**"和"Azure"僅創建新使用者帳戶，但不更新現有使用者帳戶。 此功能允許管理員為帳戶創建和更新工作流創建不同的映射。

* 添加新屬性對應。 在**屬性對應**窗格的底部選擇 **"添加新映射**"。 填寫 **"編輯屬性"** 表單並選擇 **"確定"** 以將新映射添加到清單中。

### <a name="settings"></a>設定

您可以在**預配**螢幕的 **"設置"** 區域中啟動和停止所選應用程式的 Azure AD 預配服務。 您還可以選擇清除預配緩存並重新啟動服務。

如果是初次為應用程式啟用佈建，將 [佈建狀態]**** 變更為 [開啟]**** 即可開啟服務。 此更改會導致 Azure AD 預配服務運行初始週期。 它讀取**在"使用者和組**"部分中分配的使用者，為其查詢目標應用程式，然後運行 Azure AD**映射**部分中定義的預配操作。 在此過程中，預配服務存儲有關其管理的使用者帳戶的緩存資料，因此目標應用程式內從未處於分配範圍的非託管帳戶不受取消預配操作的影響。 初始週期後，預配服務會在 40 分鐘間隔內自動同步使用者和組物件。

將**預配狀態**更改為 **"關閉**"以暫停預配服務。 在此狀態下，Azure 不會創建、更新或刪除應用中的任何使用者或組物件。 將狀態更改回**On，** 服務從關閉的位置開始。

**清除目前狀態並重新啟動同步**將觸發初始週期。 然後，該服務將再次評估源系統中的所有使用者，並確定他們是否在預配範圍內。 當應用程式當前處於隔離狀態或需要更改屬性對應時，這非常有用。 請注意，由於需要計算的物件數，初始週期需要比典型的增量週期更長的時間才能完成。 您可以在此處瞭解有關初始週期和增量週期性能的更多[詳細資訊](application-provisioning-when-will-provisioning-finish-specific-user.md)。 
