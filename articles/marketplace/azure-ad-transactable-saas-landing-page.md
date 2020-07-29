---
title: 在商業 marketplace 中建立 transactable SaaS 供應專案的登陸頁面
description: 瞭解如何為您的 transactable SaaS 供應專案建立登陸頁面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 1ff366e24adb82a0d7d4660d4afaffa0bbca0b3c
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328210"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>在商業 marketplace 中建立 transactable SaaS 供應專案的登陸頁面

本文會逐步引導您建立 transactable SaaS 應用程式的登陸頁面，並將于 Microsoft 商業 marketplace 銷售。

## <a name="overview"></a>概觀

您可以將登陸頁面視為「軟體即服務」（SaaS）供應專案的「會議廳」。 購買者訂閱供應專案之後，商業 marketplace 會將其導向至登陸頁面，以啟用並設定其訂用帳戶給您的 SaaS 應用程式。 請將此視為訂單確認步驟，讓購買者能夠查看他們已購買的內容，並確認其帳戶詳細資料。 您將使用 Azure Active Directory （Azure AD）和 Microsoft Graph，為購買者啟用單一登入（SSO），並取得可用於確認及啟用其訂用帳戶之買方的重要詳細資料，包括其名稱、電子郵件地址和組織。

由於啟動訂用帳戶所需的資訊受到 Azure AD 和 Microsoft Graph 的限制，因此不需要要求需要超過基本同意的資訊。 如果您需要需要額外同意應用程式的使用者詳細資料，您應該在訂用帳戶啟用完成後要求這則資訊。 這可讓買方的順暢訂用帳戶啟用，並降低放棄的風險。

登陸頁面通常包含下列各項：

- 提供購買的供應專案和方案的名稱，以及計費條款。
- 顯示買方的帳戶詳細資料，包括名字和姓氏、組織和電子郵件。
- 提示買方確認或替代不同的帳戶詳細資料。
- 在啟用後，引導買方進行後續步驟。 例如，接收歡迎電子郵件、管理訂用帳戶、取得支援或閱讀檔。

> [!NOTE]
> 購買者在啟用後管理其訂用帳戶時，也會將買方導向登陸頁面。 在購買者的訂用帳戶啟用之後，您必須使用 SSO 讓使用者能夠登入。 建議將使用者引導至帳戶設定檔或設定頁面。

下列各節將引導您完成建立登陸頁面的程式：

1. 建立登陸頁面的[Azure AD 應用程式註冊](#create-an-azure-ad-app-registration)。
2. [使用程式碼範例做](#use-a-code-sample-as-a-starting-point)為您應用程式的起點。
3. 解析商業 marketplace 新增至 URL 的[marketplace 購買識別權杖](#resolve-the-marketplace-purchase-identification-token)。
4. [從識別碼權杖中編碼的宣告中讀取資訊](#read-information-from-claims-encoded-in-the-id-token)，該 token 是從登入後的 Azure AD 收到，且是由要求所傳送。
5. 視需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)來收集其他資訊。
6. [使用兩個 Azure AD 應用程式來改善生產環境中的安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

商用 marketplace 完全與 Azure AD 整合。 買家抵達 marketplace 以[Azure AD 帳戶或 Microsoft 帳戶（MSA）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)驗證。 購買後，買方會從商業 marketplace 進入您的登陸頁面 URL，以啟用並管理其 SaaS 應用程式的訂用帳戶。 您必須讓購買者使用 Azure AD SSO 登入您的應用程式。 （登陸頁面 URL 是在供應專案的 [[技術](partner-center-portal/offer-creation-checklist.md#technical-configuration-page)設定] 頁面中指定。

使用身分識別的第一個步驟是確認您的登陸頁面已註冊為 Azure AD 應用程式。 註冊應用程式可讓您使用 Azure AD 來驗證使用者，並要求使用者資源的存取權。 它可以視為應用程式的定義，讓服務知道如何根據應用程式的設定向應用程式發出權杖。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

若要開始使用，請遵循[註冊新應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的指示。 若要讓其他公司的使用者造訪應用程式，您必須在要求可以使用應用程式的情況下，選擇其中一個多租使用者選項。

如果您想要查詢 Microsoft Graph API，請[設定新的應用程式來存取 Web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)。 當您選取此應用程式的 API 許可權時，**使用者**的預設值就足以收集買方的相關基本資訊，讓上執行緒序順暢且自動。 請不要要求任何標示為**需要系統管理員同意**的 API 許可權，因為這會封鎖所有非系統管理員使用者造訪您的登陸頁面。

如果您需要更高的許可權做為上線或布建程式的一部分，請考慮使用 Azure AD 的[增量同意](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)功能，讓從 marketplace 傳送的所有買家一開始就能夠與登陸頁面互動。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用程式碼範例做為起點

我們提供了數個範例應用程式，可在啟用 Azure AD 登入的情況下，執行簡單的網站。 在 Azure AD 中註冊應用程式之後，[**快速入門**] 分頁會提供通用應用程式類型和開發堆疊的清單，如 [圖 1] 所示。 選擇符合您環境的專案，並遵循下載和設定的指示。

***圖1： Azure 入口網站中的快速入門分頁***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="說明 Azure 入口網站中的 [快速入門] 分頁。":::

在您下載程式代碼並設定開發環境之後，請變更應用程式中的設定，以反映您在上一個程式中記錄的應用程式識別碼、租使用者識別碼及用戶端密碼。 請注意，確切的步驟會根據您使用的範例而有所不同。

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>使用兩個 Azure AD 應用程式來改善生產環境中的安全性

本文提供簡化的架構版本，可用於為您的商業 marketplace SaaS 供應專案執行登陸頁面。 在生產環境中執行頁面時，我們建議您只透過不同的安全應用程式來與 SaaS 履行 Api 通訊，藉此改善安全性。 這需要建立兩個新的應用程式：

- 首先，多租使用者登陸頁面應用程式會在此時間點說明，但不含可聯繫 SaaS 履行 Api 的功能。 此功能將會卸載到另一個應用程式，如下所述。
- 第二，應用程式會擁有與 SaaS 履行 Api 的通訊。 此應用程式應該是單一租使用者，僅供您的組織使用，而且可以建立存取控制清單，以限制只能從這個應用程式存取 Api。

這可讓解決方案在觀察[關注點分離](https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns)原則的案例中工作。 例如，登陸頁面會使用第一個註冊的 Azure AD 應用程式來登入使用者。 使用者登入之後，登陸頁面會使用第二個 Azure AD 來要求存取權杖，以呼叫 SaaS 履行 API，並呼叫解析作業。

## <a name="resolve-the-marketplace-purchase-identification-token"></a>解析 marketplace 購買識別權杖

當買方傳送至您的登陸頁面時，會將權杖新增至 URL 參數。 此權杖不同于用來進行服務對服務驗證的 Azure AD 發行的權杖和存取權杖，而且會做為[SaaS 履行 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)的輸入，以解析呼叫以取得訂用帳戶的詳細資料。 就像所有對 SaaS 履行 Api 的呼叫一樣，您的服務對服務要求將會以應用程式的存取權杖來進行驗證，而其 Azure AD 應用程式識別碼使用者進行服務對服務驗證。

> [!NOTE]
> 在大部分情況下，最好是從第二個單一租使用者應用程式進行此呼叫。 請參閱本文稍後的[使用兩個 Azure AD 應用程式來改善生產環境中的安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。

### <a name="request-an-access-token"></a>要求存取權杖

若要使用 SaaS 履行 Api 來驗證您的應用程式，您需要存取權杖，其可透過呼叫 Azure AD OAuth 端點來產生。 請參閱[如何取得發行者的授權權杖](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

### <a name="call-the-resolve-endpoint"></a>呼叫解析端點

SaaS 履行 Api 會執行[解析](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)端點，以呼叫以確認 marketplace 權杖的有效性，並傳回訂用帳戶的相關資訊，包括此表中顯示的值。

| 值 | 說明 |
| ------------ | ------------- |
| Id | 此訂用帳戶的唯一識別碼（GUID）。 未來呼叫 SaaS 履行 Api 時，您將需要此值。 |
| subscriptionName | 訂用帳戶的名稱，這是在供應專案新增至合作夥伴中心時所設定。 |
| offerId | 特定供應專案的識別碼（在新增供應專案時設定）。 |
| planId | 供應專案之特定方案的識別碼（在新增供應專案時設定）。 |
| 數量 | 買方在購買期間的數量輸入。 |
|||

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>從識別碼權杖中編碼的宣告讀取資訊

作為[OpenID connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)流程的一部分，Azure AD 在購買者傳送至登陸頁面時，將[識別碼權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)新增至要求。 此權杖包含多項基本資訊，在啟用程式中可能會很有用，包括此表中顯示的資訊。

| 值 | 說明 |
| ------------ | ------------- |
| aud | 此權杖的目標物件。 在此情況下，它應該符合您的應用程式識別碼並進行驗證。 |
| preferred_username | 造訪使用者的主要使用者名稱。 這可能是電子郵件地址、電話號碼或其他識別碼。 |
| 電子郵件 | 使用者的電子郵件地址。 請注意，此欄位可能是空的。 |
| NAME | 人們可讀取的值，識別權杖的主體。 在此情況下，它會是購買者的名稱。 |
| oid | Microsoft 身分識別系統中的識別碼，可在應用程式之間唯一識別使用者。 Microsoft Graph 會傳回此值做為指定使用者帳戶的 ID 屬性。 |
| tid | 代表買方來源之 Azure AD 租使用者的識別碼。 在 MSA 身分識別的案例中，這一律會是 ``9188040d-6c67-4c5b-b112-36a304b66dad`` 。 如需詳細資訊，請參閱下一節中的附注：使用 Microsoft Graph API。 |
| sub | 唯一識別此特定應用程式中使用者的識別碼。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

識別碼權杖包含可識別購買者的基本資訊，但您的啟用程式可能需要其他詳細資料（例如買方的公司）才能完成上執行緒序。 使用[MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api)來要求這項資訊，以避免強制使用者再次輸入這些詳細資料。 標準使用者： [**讀取**] 許可權預設包含下列資訊。

| 值 | 說明 |
| ------------ | ------------- |
| displayName | 使用者的通訊錄中顯示的名稱。 |
| givenName | 使用者的名字。 |
| jobTitle | 使用者的職稱。 |
| mail | 使用者的 SMTP 位址。 |
| mobilePhone | 使用者的主要行動電話號碼。 |
| preferredLanguage | 使用者慣用語言的 ISO 639-1 程式碼。 |
| surname | 使用者的姓氏。 |
|||

您可以選取其他屬性（例如使用者的公司名稱或使用者的位置（國家/地區）），以納入要求中。 如需詳細資訊，請參閱[使用者資源類型的屬性](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties)。

大部分向 Azure AD 註冊的應用程式都會授與委派的許可權，以從其公司的 Azure AD 租使用者讀取使用者的資訊。 針對該資訊 Microsoft Graph 的任何要求，都必須伴隨存取權杖以進行驗證。 產生存取權杖的特定步驟將取決於您所使用的技術堆疊，但範例程式碼會包含範例。 如需詳細資訊，請參閱[代表使用者取得存取權](https://docs.microsoft.com/graph/auth-v2-user)。

> [!NOTE]
> MSA 租使用者中的帳戶（具有租使用者識別碼 ``9188040d-6c67-4c5b-b112-36a304b66dad`` ）將不會傳回超過已經使用識別碼權杖收集的資訊。 因此，您可以略過這項對這些帳戶的圖形 API 呼叫。

## <a name="next-steps"></a>後續步驟

- [在商業 marketplace 中建立 SaaS 供應專案](./partner-center-portal/create-new-saas-offer.md)
