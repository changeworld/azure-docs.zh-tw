---
title: 在商業 marketplace 中建立免費或試用版 SaaS 供應專案的登陸頁面
description: 瞭解如何為您的免費或試用版 SaaS 供應專案建立登陸頁面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 96e23c22568229ec5f5ba2365747e261b7e471ad
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921379"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>在商業 marketplace 中建立免費或試用版 SaaS 供應專案的登陸頁面

這篇文章會引導您完成建立免費或試用版 SaaS 應用程式的登陸頁面，並將于 Microsoft 商業 marketplace 銷售。

## <a name="overview"></a>概觀

您可以將登陸頁面視為軟體即服務的「會議廳」， (SaaS) 供應專案。 在客戶選擇取得您的應用程式之後，商業 marketplace 會將其導向至登陸頁面，以啟用並設定其訂用帳戶給您的 SaaS 應用程式。 當您在合作夥伴中心建立軟體即服務 (SaaS) 供應專案時，您可以選擇是否要[透過 Microsoft 銷售](partner-center-portal/create-new-saas-offer.md)。 如果您只想要在 Microsoft 商業 marketplace 中列出您的供應專案，而不是透過 Microsoft 銷售，您可以指定潛在客戶與供應專案互動的方式。 當您啟用 [**立即取得] ([免費) ** ] 或 [**免費試用**] 清單選項時，您必須指定使用者可以前往的登陸頁面 URL，以存取免費的訂閱或試用版。

登陸頁面的目的只是要接收使用者，讓他們可以啟動免費試用版或免費訂閱。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph 時，您將會啟用使用者的單一登入 (SSO) ，並取得可用於啟用免費試用或免費訂閱之使用者的重要詳細資料，包括其名稱、電子郵件地址和組織。

由於啟動訂用帳戶所需的資訊受到 Azure AD 和 Microsoft Graph 的限制，因此不需要要求需要超過基本同意的資訊。 如果您需要需要額外同意應用程式的使用者詳細資料，您應該在訂用帳戶啟用完成後要求這則資訊。 這可讓使用者順暢訂用帳戶啟用，並降低放棄的風險。

登陸頁面通常包含下列資訊和動作的呼叫：

- 提供免費試用或免費訂用帳戶的名稱和詳細資料。 例如，指定試用的使用限制或持續時間。
- 顯示使用者的帳戶詳細資料，包括名字和姓氏、組織和電子郵件。
- 提示使用者確認或替代不同的帳戶詳細資料。
- 在啟用後引導使用者進行後續步驟。 例如，接收歡迎電子郵件、管理訂用帳戶、取得支援或閱讀檔。

本文中的下列各節將引導您完成建立登陸頁面的程式：

1. 建立登陸頁面的[Azure AD 應用程式註冊](#create-an-azure-ad-app-registration)。
2. [使用程式碼範例做](#use-a-code-sample-as-a-starting-point)為您應用程式的起點。
3. [從識別碼權杖中編碼的宣告讀取資訊](#read-information-from-claims-encoded-in-the-id-token)，並在登入後從 Azure AD 收到，並與要求一起傳送。
4. 視需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)來收集其他資訊。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

商用 marketplace 完全與 Azure AD 整合。 使用者抵達 marketplace 以 Azure AD 帳戶進行驗證，[或 Microsoft 帳戶 (MSA) ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)。 透過僅限清單的供應專案取得免費或免費試用訂用帳戶之後，使用者會從商業 marketplace 進入您的登陸頁面 URL，以啟用並管理其 SaaS 應用程式的訂用帳戶。 您必須讓使用者使用 Azure AD SSO 登入您的應用程式。  (登陸頁面 URL 是在供應專案的 [技術設定][頁面](partner-center-portal/offer-creation-checklist.md#technical-configuration-page)中指定) 。

使用身分識別的第一個步驟是確認您的登陸頁面已註冊為 Azure AD 應用程式。 註冊應用程式可讓您使用 Azure AD 來驗證使用者，並要求使用者資源的存取權。 它可以視為應用程式的定義，讓服務知道如何根據應用程式的設定向應用程式發出權杖。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

若要開始使用，請遵循[註冊新應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的指示。 若要讓其他公司的使用者造訪此應用程式，您必須選擇任何組織目錄中的帳戶， (任何 Azure AD 目錄，也就是當系統要求使用者可以使用該應用程式時， **) 和個人 Microsoft 帳戶 (例如 Skype 或 Xbox) ** 。

如果您想要查詢 Microsoft Graph API，請[設定新的應用程式來存取 Web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)。 當您選取此應用程式的 API 許可權時，**使用者**的預設值就足以收集使用者的基本資訊，讓上執行緒序順暢且自動。 請不要要求任何標示為**需要系統管理員同意**的 API 許可權，因為這會封鎖所有非系統管理員使用者造訪您的登陸頁面。

如果您在上線或布建程式中需要更高的許可權，請考慮使用 Azure AD 的累加[式同意](https://aka.ms/incremental-consent)功能，讓從 marketplace 傳送的所有使用者一開始就能夠與登陸頁面互動。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用程式碼範例做為起點

Microsoft 提供了數個範例應用程式，可在啟用 Azure AD 登入的情況下，執行簡單的網站。 在 Azure AD 中註冊您的應用程式之後，[**快速入門**] 分頁會提供常見的應用程式類型和開發堆疊清單， ([圖 1]) 。 選擇符合您環境的專案，並遵循下載和設定的指示。

***圖1： Azure 入口網站中的快速入門分頁***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="說明 Azure 入口網站中的 [快速入門] 分頁。":::

在您下載程式代碼並設定開發環境之後，請變更應用程式中的設定，以反映您在上一個程式中記錄的應用程式識別碼、租使用者識別碼及用戶端密碼。 請注意，確切的步驟會根據您使用的範例而有所不同。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>從識別碼權杖中編碼的宣告讀取資訊

作為[OpenID connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)流程的一部分，Azure AD 在使用者傳送至登陸頁面時，將[識別碼權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)新增至要求。 此權杖包含多項基本資訊，在啟用程式中可能會很有用，包括此表中顯示的資訊。

| 值 | 描述 |
| ------------ | ------------- |
| aud | 此權杖的目標物件。 在此情況下，它應該符合您的應用程式識別碼並進行驗證。 |
| preferred_username | 造訪使用者的主要使用者名稱。 這可能是電子郵件地址、電話號碼或其他識別碼。 |
| 電子郵件 | 使用者的電子郵件地址。 請注意，此欄位可能是空的。 |
| NAME | 人們可讀取的值，識別權杖的主體。 在此情況下，它會是使用者的名稱。 |
| oid | Microsoft 身分識別系統中的識別碼，可在應用程式之間唯一識別使用者。 Microsoft Graph 會傳回此值做為指定使用者帳戶的 ID 屬性。 |
| tid | 表示使用者所來自之 Azure AD 租使用者的識別碼。 在 MSA 身分識別的案例中，這一律會是 `9188040d-6c67-4c5b-b112-36a304b66dad` 。 如需詳細資訊，請參閱下一節：使用 Microsoft Graph API 中的附注。 |
| sub | 唯一識別此特定應用程式中使用者的識別碼。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

識別碼權杖包含用來識別使用者的基本資訊，但您的啟用程式可能需要其他詳細資料（例如使用者的公司）才能完成上架程式。 使用[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api)來要求這項資訊，以避免強制使用者再次輸入這些詳細資料。 標準**使用者。 [讀取**] 許可權預設包含下列資訊：

| 值 | 描述 |
| ------------ | ------------- |
| displayName | 使用者的通訊錄中顯示的名稱。 |
| givenName | 使用者的名字。 |
| jobTitle | 使用者的職稱。 |
| mail | 使用者的 SMTP 位址。 |
| mobilePhone | 使用者的主要行動電話號碼。 |
| preferredLanguage | 使用者慣用語言的 ISO 639-1 程式碼。 |
| surname | 使用者的姓氏。 |
|||

您可以選取其他屬性（例如使用者的公司名稱或使用者的位置 (country) ），以納入要求中。 如需詳細資訊，請參閱[使用者資源類型的屬性](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties)。

大部分向 Azure AD 註冊的應用程式都會授與委派的許可權，以從其公司的 Azure AD 租使用者讀取使用者的資訊。 針對該資訊 Microsoft Graph 的任何要求，都必須附有存取權杖做為驗證。 產生存取權杖的特定步驟將取決於您所使用的技術堆疊，但範例程式碼會包含範例。 如需詳細資訊，請參閱[代表使用者取得存取權](https://docs.microsoft.com/graph/auth-v2-user)。

> [!NOTE]
> MSA 租使用者中 (具有租使用者識別碼) 的帳戶， `9188040d-6c67-4c5b-b112-36a304b66dad` 將不會傳回超過已經使用識別碼權杖收集的資訊。 因此，您可以略過這項對這些帳戶的圖形 API 呼叫。

## <a name="next-steps"></a>後續步驟
- [在商業 marketplace 中建立 SaaS 供應專案](./partner-center-portal/create-new-saas-offer.md)
