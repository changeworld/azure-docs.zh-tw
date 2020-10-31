---
title: 在商業市場中建立免費或試用 SaaS 供應專案的登陸頁面
description: 瞭解如何為您的免費或試用版 SaaS 供應專案建立登陸頁面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: dfa5d77077b8827bed1cbd8c7a46a5dbf361f139
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125711"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>在商業市場中建立免費或試用 SaaS 供應專案的登陸頁面

本文將逐步引導您建立免費或試用版 SaaS 應用程式的登陸頁面，該頁面將會在 Microsoft 商業 marketplace 上銷售。

## <a name="overview"></a>概觀

您可以將登陸頁面視為軟體即服務 (SaaS) 供應專案的「大廳」。 客戶選擇取得您的應用程式之後，商業市場會將他們導向至登陸頁面，以啟用並設定其對您 SaaS 應用程式的訂用帳戶。 當您在合作夥伴中心中建立軟體即服務 (SaaS) 供應專案時，您可以選擇是否要 [透過 Microsoft 銷售](plan-saas-offer.md#listing-options)。 如果您只想要在 Microsoft 商業 marketplace 中列出您的供應專案，而不是透過 Microsoft 銷售，可以指定潛在客戶與供應專案互動的方式。 當您啟用 [ **立即取得] (免費)** 或 **免費試用** 清單選項時，您必須指定使用者可以前往的登陸頁面 URL，以存取免費訂用帳戶或試用版。

登陸頁面的目的只是要接收使用者，讓他們可以啟動免費試用或免費訂用帳戶。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph，您將為使用者啟用單一登入 (SSO) ，並取得有關使用者的重要詳細資料，您可用來啟用其免費試用或免費訂用帳戶，包括其名稱、電子郵件地址和組織。

因為啟用訂用帳戶所需的資訊受限於 Azure AD 和 Microsoft Graph，所以不需要要求超過基本同意的資訊。 如果您需要應用程式需要額外同意的使用者詳細資料，您應該在訂用帳戶啟用完成後要求此資訊。 這可讓使用者順暢訂用帳戶啟用，並降低放棄的風險。

登陸頁面通常包含下列資訊和清單選項：

- 提供免費試用或免費訂用帳戶的名稱和詳細資料。 例如，指定試用的使用量限制或持續時間。
- 顯示使用者的帳戶詳細資料，包括名字和姓氏、組織和電子郵件。
- 提示使用者確認或替代不同的帳戶詳細資料。
- 在啟用之後，引導使用者進行後續步驟。 例如，收到歡迎電子郵件、管理訂用帳戶、取得支援或閱讀檔。

本文中的下列各節將引導您完成建立登陸頁面的程式：

1. 為登陸頁面[建立 Azure AD 應用程式註冊](#create-an-azure-ad-app-registration)。
2. [使用程式碼範例作為應用程式的起點](#use-a-code-sample-as-a-starting-point) 。
3. [從識別碼權杖中編碼的宣告讀取資訊](#read-information-from-claims-encoded-in-the-id-token)，這些宣告會在登入後從 Azure AD 接收，並隨要求一起傳送。
4. 視需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)來收集其他資訊。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

商業 marketplace 與 Azure AD 完全整合。 使用者會在使用 [Azure AD 帳戶或 Microsoft 帳戶 (MSA) ](../active-directory/fundamentals/active-directory-whatis.md#terminology)進行驗證的 marketplace。 透過僅限清單供應專案取得免費或免費試用版訂用帳戶之後，該使用者會從商業 marketplace 移至登陸頁面 URL，以啟用並管理其訂用帳戶到您的 SaaS 應用程式。 您必須讓使用者使用 Azure AD SSO 來登入您的應用程式。  (登陸頁面 URL 是在供應專案的 [ [技術](plan-saas-offer.md#technical-information) 設定] 頁面中指定。

使用身分識別的第一個步驟是確定您的登陸頁面已註冊為 Azure AD 應用程式。 註冊應用程式可讓您使用 Azure AD 來驗證使用者，並要求使用者資源的存取權。 您可以將它視為應用程式的定義，讓服務知道如何根據應用程式的設定，對應用程式發出權杖。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

若要開始使用，請遵循 [註冊新應用程式](../active-directory/develop/quickstart-register-app.md)的指示。 若要讓其他公司的使用者流覽應用程式，您必須選擇 **任何組織目錄中的帳戶 (任何 Azure AD 目錄，也就是多租使用者) 和個人 Microsoft 帳戶 (例如 Skype 或 Xbox)** 當系統要求可以使用應用程式時

如果您想要查詢 Microsoft Graph API，請 [設定新的應用程式以存取 Web api](../active-directory/develop/quickstart-configure-app-access-web-apis.md)。 當您選取此應用程式的 [API] 許可權時，[ **使用者** ] 的預設值就足以收集使用者的基本資訊，讓上執行緒序順利和自動完成。 請勿要求任何標記為需要系統 **管理員同意** 的 API 許可權，因為這樣會封鎖所有非系統管理員的使用者造訪登陸頁面。

如果您在上線或布建程式中需要較高的許可權，請考慮使用 Azure AD 的累加 [式同意](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) 功能，以便從 marketplace 傳送的所有使用者一開始都可以與登陸頁面互動。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用程式碼範例做為起點

Microsoft 已提供數個範例應用程式，這些應用程式會使用已啟用 Azure AD 登入來執行簡單的網站。 在 Azure AD 中註冊您的應用程式之後，[ **快速入門** ] 分頁會提供常見的應用程式類型和開發堆疊清單， ([圖 1]) 。 選擇符合您環境的專案，並遵循下載和設定的指示。

**_圖1： Azure 入口網站 _ 中的快速入門 blade_*

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="說明 Azure 入口網站中的快速入門分頁。":::

在您下載程式代碼並設定開發環境之後，請變更應用程式中的設定，以反映您在上一個程式中記錄的應用程式識別碼、租使用者識別碼和用戶端密碼。 請注意，確切的步驟會因您所使用的範例而有所不同。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>從識別碼權杖中編碼的宣告讀取資訊

在 [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) 流程中，Azure AD 將使用者傳送至登陸頁面時，會將 [識別碼權杖](../active-directory/develop/id-tokens.md) 新增至要求中。 此權杖包含多項基本資訊，這些資訊在啟用程式中可能很有用，包括此表格所示的資訊。

| 值 | 描述 |
| ------------ | ------------- |
| aud | 此權杖的目標物件。 在此情況下，它應該符合您的應用程式識別碼並進行驗證。 |
| preferred_username | 造訪使用者的主要使用者名稱。 這可能是電子郵件地址、電話號碼或其他識別碼。 |
| 電子郵件 | 使用者的電子郵件地址。 請注意，此欄位可能是空的。 |
| NAME | 人們可讀取的值，可識別權杖的主體。 在此情況下，它將會是使用者的名稱。 |
| oid | Microsoft 身分識別系統中的識別碼，可跨應用程式唯一識別使用者。 Microsoft Graph 將會傳回此值做為指定使用者帳戶的 ID 屬性。 |
| tid | 代表使用者來自 Azure AD 租使用者的識別碼。 在 MSA 身分識別的案例中，這一律會是 `9188040d-6c67-4c5b-b112-36a304b66dad` 。 如需詳細資訊，請參閱下一節中的附注：使用 Microsoft Graph API。 |
| sub | 唯一識別此特定應用程式中使用者的識別碼。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

識別碼權杖包含用來識別使用者的基本資訊，但您的啟用程式可能需要其他詳細資料（例如使用者的公司）才能完成上架程式。 使用 [MICROSOFT GRAPH API](/graph/use-the-api) 來要求這項資訊，以避免強制使用者再次輸入這些詳細資料。 標準 _ *使用者. 讀取* * 許可權預設包含下列資訊：

| 值 | 描述 |
| ------------ | ------------- |
| displayName | 顯示在使用者通訊錄中的名稱。 |
| givenName | 使用者的名字。 |
| jobTitle | 使用者的職稱。 |
| mail | 使用者的 SMTP 位址。 |
| mobilePhone | 使用者的主要行動電話號碼。 |
| preferredLanguage | 適用于使用者慣用語言的 ISO 639-1 程式碼。 |
| surname | 使用者的姓氏。 |
|||

您可以選取其他屬性（例如使用者的公司名稱或使用者位置 (country) ），以納入要求中。 如需詳細資訊，請參閱 [使用者資源類型的屬性](/graph/api/resources/user?view=graph-rest-1.0#properties)。

大部分向 Azure AD 註冊的應用程式會授與委派的許可權，以讀取其公司 Azure AD 租使用者中的使用者資訊。 針對該資訊 Microsoft Graph 的任何要求都必須伴隨存取權杖作為驗證。 產生存取權杖的特定步驟將取決於您所使用的技術堆疊，但範例程式碼將包含範例。 如需詳細資訊，請參閱 [代表使用者取得存取權](/graph/auth-v2-user)。

> [!NOTE]
> MSA 租使用者 (中具有租使用者識別碼) 的帳戶， `9188040d-6c67-4c5b-b112-36a304b66dad` 將不會傳回超過已使用識別碼權杖收集的資訊。 因此，您可以略過這類帳戶對圖形 API 的呼叫。

## <a name="next-steps"></a>後續步驟
- [如何在商業市場中建立 SaaS 供應專案](create-new-saas-offer.md)