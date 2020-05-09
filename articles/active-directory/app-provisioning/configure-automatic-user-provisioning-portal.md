---
title: Azure AD 中的企業應用程式使用者布建管理
description: 了解如何使用 Azure Active Directory 管理企業應用程式的使用者帳戶佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 02f3198a9c3d35a0031fdd65ceefbb72b0f8bb31
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593806"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 入口網站中管理企業應用程式的使用者帳戶佈建

本文說明管理自動使用者帳戶布建和解除布建的一般步驟，以支援它的應用程式。 *使用者帳戶佈建*是指在應用程式的本機使用者設定檔儲存中建立、更新及/或停用使用者帳戶記錄的動作。 大部分的雲端和 SaaS 應用程式會將使用者角色和許可權儲存在使用者自己的本機使用者設定檔存放區中，而使用者本機存放區中的這類使用者記錄*必須*存在，才能讓單一登入和存取工作。 若要深入瞭解自動使用者帳戶布建，請參閱[使用 Azure Active Directory 自動化 SaaS 應用程式的使用者](user-provisioning.md)布建和解除布建。

> [!IMPORTANT]
> Azure Active Directory （Azure AD）有一個資源庫，其中包含數千個預先整合的應用程式，可透過 Azure AD 啟用自動布建。 您應該從在[如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中找到您的應用程式專屬的布建設定教學課程開始。 您可能會發現設定應用程式和 Azure AD 來建立布建連線的逐步指引。

## <a name="finding-your-apps-in-the-portal"></a>在入口網站中尋找您的應用程式

使用 Azure Active Directory 入口網站來查看和管理在目錄中針對單一登入設定的所有應用程式。 企業應用程式是您組織內部署和使用的應用程式。 請遵循下列步驟來查看及管理您的企業應用程式：

1. 開啟[Azure Active Directory 入口網站](https://aad.portal.azure.com)。
1. 從左窗格中選取 [**企業應用程式**]。 隨即會顯示所有已設定的應用程式清單，包括從資源庫新增的應用程式。
1. 選取任何應用程式以載入其 [資源] 窗格，您可以在其中查看報表及管理應用程式設定。
1. 選取 [布建 **] 以管理**所選應用程式的使用者帳戶布建設定。

   ![用來管理使用者帳戶布建設定的布建畫面](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>佈建模式

[**布**建] 窗格的開頭為 [**模式**] 功能表，其中顯示企業應用程式支援的布建模式，並可讓您進行設定。 可用的選項包括：

* **自動**-如果 Azure AD 支援對此應用程式進行使用者帳戶的自動以 API 為基礎的布建或取消布建，則會顯示此選項。 選取此模式以顯示可協助系統管理員的介面：

  * 設定 Azure AD 以連接到應用程式的使用者管理 API
  * 建立帳戶對應和工作流程，以定義使用者帳戶資料應如何在 Azure AD 與應用程式之間流動
  * 管理 Azure AD 布建服務

* **手動**-如果 Azure AD 不支援將使用者帳戶自動布建到此應用程式，則會顯示此選項。 在此情況下，必須使用外部進程來管理儲存在應用程式中的使用者客戶紀錄，這是根據該應用程式所提供的使用者管理和布建功能（可以包含 SAML 即時布建）。

## <a name="configuring-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

選取 [**自動**] 選項，以指定系統管理員認證、對應、啟動和停止，以及同步處理的設定。

### <a name="admin-credentials"></a>管理員認證

展開 [系統**管理員認證**]，以輸入 Azure AD 連線到應用程式的使用者管理 API 所需的認證。 所需的輸入依應用程式而有所不同。 若要深入了解認證類型，以及針對特定應用程式的需求，請參閱 [針對該特定應用程式的設定教學課程](user-provisioning.md)。

選取 [**測試**連線] 以測試認證，方法是讓 Azure AD 嘗試使用提供的認證來連線到應用程式的布建應用程式。

### <a name="mappings"></a>對應

在布建或更新使用者帳戶時 **，展開 [** 對應] 以查看和編輯在 Azure AD 與目標應用程式之間流動的使用者屬性。

Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間，有一組預先設定的對應。 有些 app 則管理其他類型的物件，例如群組或連絡人。 在資料表中選取對應，以開啟右側的對應編輯器，您可以在其中進行查看和自訂。

![顯示 [屬性對應] 畫面](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支援的自訂項目包含：

* 啟用和停用特定物件的對應，例如 Azure AD 使用者物件對應至 SaaS 應用程式的使用者物件。
* 編輯會從 Azure AD 使用者物件流向應用程式使用者物件的屬性。 如需有關屬性對應的詳細資訊，請參閱 [了解屬性對應類型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 篩選 Azure AD 在目標應用程式上執行的布建動作。 您可以限制執行的動作，而不是讓 Azure AD 完全同步處理物件。

  例如，只有選取 [**更新**] 和 [Azure AD 只會更新應用程式中的現有使用者帳戶，但不會建立新的帳戶。 只有選取 [**建立**] 和 [Azure] 只會建立新的使用者帳戶，但不會更新現有的帳戶。 這項功能可讓系統管理員針對帳戶建立和更新工作流程建立不同的對應。

* 加入新的屬性對應。 選取 [**屬性對應**] 窗格底部的 [**加入新的對應**]。 填寫 [**編輯屬性**] 表單，然後選取 **[確定]** ，將新的對應加入清單中。

### <a name="settings"></a>設定

您可以在 [布建 **] 畫面的 [** **設定**] 區域中，啟動和停止所選應用程式的 Azure AD 布建服務。 您也可以選擇清除布建快取，然後重新開機服務。

如果是初次為應用程式啟用佈建，將 [佈建狀態]**** 變更為 [開啟]**** 即可開啟服務。 這種變更會導致 Azure AD 布建服務執行初始迴圈。 它會讀取 [**使用者和群組**] 區段中指派的使用者、查詢目標應用程式，然後執行在 **[Azure AD 對應**] 區段中定義的布建動作。 在此程式中，布建服務會儲存有關其管理之使用者帳戶的快取資料，因此不在指派範圍內的目標應用程式中的非受控帳戶不會受到解除布建作業的影響。 在初始迴圈之後，布建服務會在40分鐘的間隔自動同步處理使用者和群組物件。

將布建**狀態**變更為 [**關閉**]，以暫停布建服務。 在此狀態下，Azure 不會建立、更新或移除應用程式中的任何使用者或群組物件。 將狀態變更回 [**開啟**]，服務就會從中斷處繼續進行。

**清除目前狀態並重新啟動同步**處理會觸發初始迴圈。 服務接著會再次評估來源系統中的所有使用者，並判斷它們是否在布建範圍內。 當您的應用程式目前在隔離中，或您需要變更屬性對應時，這會很有用。 請注意，由於需要評估的物件數目，初始週期需要比一般累加週期更長的時間才能完成。 您可以在[這裡](application-provisioning-when-will-provisioning-finish-specific-user.md)深入瞭解初始和增量迴圈的效能。 
