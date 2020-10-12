---
title: Azure AD 中的企業應用程式使用者佈建管理
description: 了解如何使用 Azure Active Directory 管理企業應用程式的使用者帳戶佈建
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9c42a83b4f7f3c6b5ff501525a04ebd96c2a692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88234834"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 入口網站中管理企業應用程式的使用者帳戶佈建

本文描述針對受支援應用程式管理自動使用者帳戶佈建和解除佈建的一般步驟。 *使用者帳戶佈建*是指在應用程式的本機使用者設定檔儲存中建立、更新及/或停用使用者帳戶記錄的動作。 大部分雲端和 SaaS 應用程式都會在使用者的本機使用者設定檔儲存中儲存使用者角色和權限，而這類使用者記錄「必須」存在於使用者本機存放區中，才能讓單一登入和存取運作。 如需深入了解自動使用者佈建，請參閱[使用 Azure Active Directory 對 SaaS 應用程式的自動使用者佈建和解除佈建](user-provisioning.md)。

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 資源庫包含數千個預先整合的應用程式，且能夠搭配 Azure AD 使用自動佈建。 建議先在[如何整合 SaaS 應用程式與 Azure Active Directory 教學課程清單](../saas-apps/tutorial-list.md)中尋找應用程式特定的佈建設定教學課程。 您或許可找到設定應用程式和 Azure AD 來建立佈建連線的逐步指引。

## <a name="finding-your-apps-in-the-portal"></a>在入口網站中尋找您的應用程式

使用 Azure Active Directory 入口網站來檢視和管理針對單一登入目錄設定的所有應用程式。 企業應用程式是您組織內部署和使用的應用程式。 請遵循下列步驟來檢視和管理企業應用程式：

1. 開啟 [Azure Active Directory 入口網站](https://aad.portal.azure.com)。
1. 在左側窗格中，選取 [企業應用程式]。 此時會顯示所有已設定的應用程式清單，包括已從資源庫新增的應用程式。
1. 選取任意應用程式以載入其資源窗格，您可在其中檢視報表及管理應用程式設定。
1. 選取 [佈建]，以管理所選應用程式的使用者帳戶佈建設定。

   ![管理使用者帳戶佈建設定的佈建畫面](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>佈建模式

[佈建] 窗格的最前方是 [模式] 功能表，其會顯示企業應用程式所支援的佈建模式，且能夠加以設定。 可用的選項包括：

* **自動** - 如果 Azure AD 支援對此應用程式進行 API 型自動佈建或解除佈建使用者帳戶，就會出現此選項。 選取此模式以顯示可協助系統管理員的介面：

  * 設定 Azure AD 以連線至應用程式的使用者管理 API
  * 建立帳戶對應和工作流程，其定義使用者帳戶資料應如何在 Azure AD 與應用程式之間流動
  * 管理 Azure AD 佈建服務

* **手動** - 如果 Azure AD 不支援對此應用程式自動佈建使用者帳戶，就會出現此選項。 在此情況下，必須根據該應用程式提供的使用者管理和佈建功能 (可包括 SAML Just-In-Time 佈建)，使用外部程序來管理儲存在應用程式中的使用者帳戶記錄。

## <a name="configuring-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

選取 [自動] 選項，以指定管理員認證、對應、啟動和停止以及同步等的設定。

### <a name="admin-credentials"></a>管理員認證

展開 [管理員認證] 以輸入 Azure AD 連線到應用程式使用者管理 API 所需的認證。 所需的輸入依應用程式而有所不同。 若要深入了解認證類型，以及針對特定應用程式的需求，請參閱 [針對該特定應用程式的設定教學課程](user-provisioning.md)。

選取 [測試連線] ，讓 Azure AD 使用所提供認證來嘗試連線到應用程式的佈建應用程式，以此測試認證。

### <a name="mappings"></a>對應

在佈建或更新使用者帳戶時，展開 [對應] 以檢視和編輯在 Azure AD 與目標應用程式之間流動的使用者屬性。

在 Azure AD 使用者物件與每個 SaaS 應用程式的使用者物件之間，會有一組預先設定的對應。 有些 app 則管理其他類型的物件，例如群組或連絡人。 在資料表中選取對應，以開啟右側的對應編輯器，您可在其中進行檢視和自訂。

![顯示 [屬性對應] 畫面](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支援的自訂項目包含：

* 啟用和停用特定物件的對應，例如 Azure AD 使用者物件對應至 SaaS 應用程式的使用者物件。
* 編輯會從 Azure AD 使用者物件流向應用程式使用者物件的屬性。 如需有關屬性對應的詳細資訊，請參閱 [了解屬性對應類型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 篩選 Azure AD 在目標應用程式上執行的佈建動作。 您可限制執行的動作，而無需讓 Azure AD 完全同步物件。

  例如，若只選取 [更新]，則 Azure AD 只會更新應用程式中的現有使用者帳戶，而不會建立新帳戶。 若只選取 [建立]，則 Azure 只會建立新的使用者帳戶，但不會更新現有帳戶。 這項功能可讓管理員為帳戶建立和更新工作流程建立不同的對應。

* 新增新的屬性對應。 選取 [屬性對應] 窗格底部的 [新增對應]。 填寫 [編輯屬性] 表單，然後選取 [確定] 將對應新增至清單中。

### <a name="settings"></a>設定

您可在 [佈建] 畫面的 [設定] 區域中，啟動和停止所選應用程式的 Azure AD 佈建服務。 您也可以選擇清除佈建快取，然後重新啟動服務。

如果是初次為應用程式啟用佈建，將 [佈建狀態] 變更為 [開啟] 即可開啟服務。 此變更會導致 Azure AD 佈建服務執行初始週期。 此程序會讀取 [使用者和群組] 區段中指派的使用者，查詢其目標應用程式，並接著執行在 Azure AD [對應] 區段中定義的佈建動作。 在此程序中，佈建服務會儲存快取的資料 (其有關正在管理哪些使用者帳戶)，所以不曾在指派範圍中目標應用程式內的非受控帳戶將不會受到解除佈建作業的影響。 在初始週期之後，每隔 40 分鐘佈建服務會自動同步使用者和群組物件。

將 [佈建狀態] 變更為 [關閉] 以暫停佈建服務。 在此狀態下，Azure 不會建立、更新或移除應用程式中的任何使用者或群組物件。 將狀態變更回 [開啟]，服務就會從中斷處繼續進行。

[清除目前狀態並重新啟動同步] 會觸發初始週期。 服務接著會再次評估來源系統中的所有使用者，並判斷這些使用者是否在佈建範圍內。 當應用程式目前在隔離中，或您需要變更屬性對應時，這會很有幫助。 請注意，由於需要評估的物件數目，初始週期需要比一般累加週期花費更長的時間才能完成。 您可在[這裡](application-provisioning-when-will-provisioning-finish-specific-user.md)深入了解初始和累加週期的效能。