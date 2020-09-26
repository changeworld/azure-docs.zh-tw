---
title: 新增自助式註冊使用者流程 - Azure AD
description: 為您的組織所建立的應用程式建立使用者流程。 然後，流覽該應用程式的使用者就可以使用在使用者流程中設定的選項，來得到來賓帳戶。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb1d5eb756c7bc316a6d8e1de30a95eb1ac5194
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287044"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>將自助式註冊使用者流程新增至應用程式 (預覽)
> [!NOTE]
> 自助式註冊是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以針對由您的組織所建置的應用程式建立使用者流程。 將您的使用者流程與應用程式建立關聯，可讓您啟用該應用程式的註冊。 您可以選擇多個要與使用者流程建立關聯的應用程式。 當您將使用者流程與一或多個應用程式建立關聯後，瀏覽該應用程式的使用者即可透過使用者流程中設定的選項進行註冊並取得來賓帳戶。

> [!NOTE]
> 您可以將由您的組織所建置的應用程式與使用者流程建立關聯。 使用者流程不能用於 Microsoft 應用程式，例如 SharePoint 或 Teams。

## <a name="before-you-begin"></a>開始之前

### <a name="add-social-identity-providers-optional"></a>新增社交識別提供者 (選擇性)

Azure AD 是自助式註冊的預設識別提供者。 這表示使用者依預設可使用 Azure AD 帳戶進行註冊。 社交識別提供者也可以包含在這些註冊流程中，以支援 Google 和 Facebook 帳戶。

- [將 Facebook 新增至您的社交識別提供者清單](facebook-federation.md)
- [將 Google 新增至您的社交識別提供者清單](google-federation.md)

> [!NOTE]
> 在目前的預覽中，如果有自助式註冊使用者流程與應用程式相關聯，且您將該應用程式的邀請傳送給使用者，該使用者將無法使用 Gmail 帳戶來兌換邀請。 因應措施是讓該使用者完成自助式註冊程序。 或者，他們可以透過存取不同的應用程式，或是使用其「我的應用程式」入口網站 (https://myapps.microsoft.com ) 來兌換邀請。

### <a name="define-custom-attributes-optional"></a>定義自訂屬性 (選擇性)

使用者屬性是在自助式註冊期間向使用者收集的值。 Azure AD 隨附內建的屬性集，但您可以建立自訂屬性，以便在使用者流程中使用。 您也可以使用 Microsoft Graph API 來讀取和寫入這些屬性。 請參閱[定義使用者流程的自訂屬性](user-flow-add-custom-attributes.md)。

## <a name="enable-self-service-sign-up-for-your-tenant"></a>為您的租用戶啟用自助式註冊

您必須先為租用戶啟用相關功能，才能將自助式註冊使用者流程新增至應用程式。 該功能啟用後，使用者流程中的控制項就會變成可用，讓您將使用者流程與應用程式建立關聯。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 選取 [使用者設定]，然後在 [外部使用者] 底下，選取 [管理外部共同作業設定]。
4. 設定 [允許來賓透過使用者流程進行自助式註冊 (預覽)] 切換至 [是]。

   ![啟用來賓自助式註冊](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>建立自助式註冊的使用者流程

接著，您將建立自助式註冊的使用者流程，並將其新增至應用程式。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [使用者流程 (預覽)]，然後選取 [新增使用者流程]。

   ![新增使用者流程按鈕](media/self-service-sign-up-user-flow/new-user-flow.png)

5. 在 [建立] 頁面上，輸入使用者流程 的 [名稱]。 請注意，名稱前面會自動加上 **B2X_1_** 。
6. 在 [識別提供者] 清單中，選取您的外部使用者可用來登入應用程式的一或多個識別提供者。 依預設會選取 [Azure Active Directory 註冊]。 (請參閱本文前述的[開始之前](#before-you-begin)，以了解如何新增識別提供者。)
7. 在 [使用者屬性] 底下，選擇您要向使用者收集的屬性。 如需其他屬性，請選取 [顯示更多]。 例如，選取 [顯示更多]，然後選擇 [國家/地區]、[顯示名稱]，及 [郵遞區號] 的屬性和宣告。 選取 [確定]。

   ![建立新的使用者流程頁面](media/self-service-sign-up-user-flow/create-user-flow.png)

8. 選取 [建立]。
9. 新的使用者流程會出現在 [使用者流程 (預覽)] 清單中。 如有需要，請重新整理頁面。

## <a name="select-the-layout-of-the-attribute-collection-form"></a>選取屬性集合表單的配置

您可以選擇在註冊頁面上顯示內容的順序。 

1. 在 [[Azure 入口網站]](https://portal.azure.com) 中，選取 **[Azure Active Directory]** 。
2. 選取 [外部身分識別]，然後選取 [使用者流程 (預覽)]。
3. 從清單中選取自助式註冊使用者流程。
4. 在 [自訂] 下方，選取 [頁面配置]。
5. 您選擇要收集的屬性隨即列出。 若要變更顯示順序，請選取屬性，然後選取 [上移]、[下移]、[移至最上方] 或 [移至底部]。
6. 選取 [儲存]。

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>將應用程式新增至自助式註冊使用者流程

現在，您可以將應用程式與使用者流程建立關聯。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 在 [自助式註冊] 底下，選取 [使用者流程 (預覽)]。
5. 從清單中選取自助式註冊使用者流程。
6. 在左側功能表的 [使用] 底下，選取 [應用程式]。
7. 選取 [新增應用程式] 。

   ![將應用程式指派給使用者流程](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 從清單中選取應用程式。 或者，使用搜尋方塊來尋找應用程式，然後加以選取。
9. 按一下 [選取]。

## <a name="next-steps"></a>後續步驟

- [將 Google 新增至您的社交識別提供者清單](google-federation.md)
- [將 Facebook 新增至您的社交識別提供者清單](facebook-federation.md)
- [使用 API 連接器透過 web Api 自訂和擴充您的使用者流程](api-connectors-overview.md)
- [將自訂核准工作流程新增至您的使用者流程](self-service-sign-up-add-approvals.md)
