---
title: 在商業市場中建立可交易 SaaS 供應專案的登陸頁面
description: 瞭解如何為您的可交易 SaaS 供應專案建立登陸頁面。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 9db013d13098fc6aa4552459a2189e0ad8fc3ea6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378792"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>在商業市場中建立可交易 SaaS 供應專案的登陸頁面

本文將引導您完成為可交易 SaaS 應用程式建立登陸頁面的程式，此頁面將會在 Microsoft 商業 marketplace 上銷售。

## <a name="overview"></a>概觀

您可以將登陸頁面視為軟體即服務 (SaaS) 供應專案的「大廳」。 買方訂閱供應專案之後，商業市場會將他們導向至登陸頁面，以啟用並設定其對您 SaaS 應用程式的訂用帳戶。 將此視為訂單確認步驟，可讓買方查看他們購買的內容，並確認其帳戶詳細資料。 使用 Azure Active Directory (Azure AD) 和 Microsoft Graph，您將為買方啟用單一登入 (SSO) ，並取得可用來確認及啟用其訂用帳戶的購買者相關重要詳細資料，包括其名稱、電子郵件地址和組織。

因為啟用訂用帳戶所需的資訊受限於 Azure AD 和 Microsoft Graph，所以不需要要求超過基本同意的資訊。 如果您需要應用程式需要額外同意的使用者詳細資料，您應該在訂用帳戶啟用完成後要求此資訊。 這會啟用買方的順暢訂用帳戶啟用，並降低放棄的風險。

登陸頁面通常包含下列各項：

- 提供購買的供應專案和方案名稱，以及帳單條款。
- 顯示買方的帳戶詳細資料，包括名字和名字、組織和電子郵件。
- 提示買方確認或替代不同的帳戶詳細資料。
- 請在啟用後引導買方進行後續步驟。 例如，收到歡迎電子郵件、管理訂用帳戶、取得支援或閱讀檔。

> [!NOTE]
> 買方在啟用之後管理其訂用帳戶時，也會被導向至登陸頁面。 在購買買方的訂用帳戶之後，您必須使用 SSO 來讓使用者登入。 建議將使用者導向至帳戶設定檔或設定頁面。

下列各節將引導您完成建立登陸頁面的程式：

1. 為登陸頁面[建立 Azure AD 應用程式註冊](#create-an-azure-ad-app-registration)。
1. [使用程式碼範例作為應用程式的起點](#use-a-code-sample-as-a-starting-point) 。
1. [使用兩個 Azure AD 的應用程式來改善生產環境中的安全性](#use-two-azure-ad-apps-to-improve-security-in-production)。
1. 解決商業 marketplace 新增至 URL[的 marketplace 購買識別權杖](#resolve-the-marketplace-purchase-identification-token)。
1. [從識別碼權杖中編碼的宣告讀取資訊](#read-information-from-claims-encoded-in-the-id-token)，此權杖是在登入後從 Azure AD 收到，並隨要求一起傳送。
1. 視需要[使用 MICROSOFT GRAPH API](#use-the-microsoft-graph-api)來收集其他資訊。

## <a name="create-an-azure-ad-app-registration"></a>建立 Azure AD 應用程式註冊

商業 marketplace 與 Azure AD 完全整合。 買方會在使用 [Azure AD 帳戶或 Microsoft 帳戶 (MSA) ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)進行驗證的 marketplace 中抵達。 購買之後，買方會從商業 marketplace 移至登陸頁面 URL，以啟用及管理其 SaaS 應用程式的訂用帳戶。 您必須讓買方使用 Azure AD SSO 來登入您的應用程式。  (登陸頁面 URL 是在供應專案的 [ [技術](plan-saas-offer.md#technical-information) 設定] 頁面中指定。

使用身分識別的第一個步驟是確定您的登陸頁面已註冊為 Azure AD 應用程式。 註冊應用程式可讓您使用 Azure AD 來驗證使用者，並要求使用者資源的存取權。 您可以將它視為應用程式的定義，讓服務知道如何根據應用程式的設定，對應用程式發出權杖。

### <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

若要開始使用，請遵循 [註冊新應用程式](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的指示。 若要讓其他公司的使用者流覽應用程式，您必須在要求使用者可以使用應用程式時，選擇其中一個多租使用者選項。

如果您想要查詢 Microsoft Graph API，請 [設定新的應用程式以存取 Web api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis)。 當您選取此應用程式的 [API] 許可權時，[ **使用者** ] 的預設值就足以收集買方的基本資訊，讓上執行緒序順利且自動。 請勿要求任何標記為需要系統 **管理員同意**的 API 許可權，因為這樣會封鎖所有非系統管理員的使用者造訪登陸頁面。

如果您在上線或布建程式中需要較高的許可權，請考慮使用 Azure AD 的累加 [式同意](https://aka.ms/incremental-consent) 功能，以便從 marketplace 傳送的所有買家一開始都能與登陸頁面互動。

## <a name="use-a-code-sample-as-a-starting-point"></a>使用程式碼範例做為起點

我們已提供數個範例應用程式，這些應用程式會執行已啟用 Azure AD 登入的簡易網站。 在 Azure AD 中註冊您的應用程式之後，[ **快速入門** ] 分頁會提供常見的應用程式類型和開發堆疊清單，如圖1所示。 選擇符合您環境的專案，並遵循下載和設定的指示。

***圖1： Azure 入口網站中的快速入門 blade***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="說明 Azure 入口網站中的快速入門分頁。":::

在您下載程式代碼並設定開發環境之後，請變更應用程式中的設定，以反映您在上一個程式中記錄的應用程式識別碼、租使用者識別碼和用戶端密碼。 請注意，確切的步驟會因您所使用的範例而有所不同。

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>使用兩個 Azure AD 的應用程式來改善生產環境中的安全性

本文提供簡化的架構版本，可供您針對商用 marketplace SaaS 供應專案實施登陸頁面。 在生產環境中執行此頁面時，建議您只透過不同的安全應用程式與 SaaS 履行 Api 進行通訊，以提升安全性。 這需要建立兩個新的應用程式：

- 第一個是目前為止所述的多租使用者登陸頁面應用程式，但沒有聯繫 SaaS 履行 Api 的功能。 此功能將會卸載至另一個應用程式，如下所述。
- 第二，應用程式擁有與 SaaS 履行 Api 的通訊。 此應用程式應該是單一租使用者（僅供您的組織使用），而且可以建立存取控制清單，以限制只能從這個應用程式存取 Api。

這可讓解決方案在觀察 [考慮準則分離](https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) 的案例中運作。 例如，登陸頁面會使用第一個註冊的 Azure AD 應用程式來登入使用者。 使用者登入之後，登陸頁面會使用第二個 Azure AD 來要求存取權杖，以呼叫 SaaS 履行 API 並呼叫解析作業。

## <a name="resolve-the-marketplace-purchase-identification-token"></a>解析 marketplace 購買識別權杖

當買方傳送至登陸頁面時，會將權杖新增至 URL 參數。 此權杖不同于 Azure AD 發行的權杖和用於服務對服務驗證的存取權杖，可用來做為 [SaaS 履行 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 的輸入，以解析呼叫以取得訂用帳戶的詳細資料。 如同所有對 SaaS 履行 Api 的呼叫，服務對服務的要求將會使用以應用程式的 Azure AD 應用程式識別碼使用者進行服務對服務驗證的存取權杖進行驗證。

> [!NOTE]
> 在大部分的情況下，最好是從第二個單一租使用者應用程式進行此呼叫。 請參閱本文稍早的在 [生產環境中使用兩個 Azure AD 應用程式來改善安全性](#use-two-azure-ad-apps-to-improve-security-in-production) 。

### <a name="request-an-access-token"></a>要求存取權杖

若要使用 SaaS 履行 Api 來驗證您的應用程式，您需要可透過呼叫 Azure AD OAuth 端點來產生的存取權杖。 請參閱 [如何取得發行者的授權權杖](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

### <a name="call-the-resolve-endpoint"></a>呼叫解析端點

SaaS 履行 Api 會執行 [解析](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 端點，此端點可呼叫以確認 marketplace 權杖的有效性，並傳回訂用帳戶的相關資訊。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>從識別碼權杖中編碼的宣告讀取資訊

在 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 流程中，Azure AD 在買方傳送至登陸頁面時，將 [識別碼權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) 新增至要求中。 此權杖包含多項基本資訊，這些資訊在啟用程式中可能很有用，包括此表格所示的資訊。

| 值 | 描述 |
| ------------ | ------------- |
| aud | 此權杖的目標物件。 在此情況下，它應該符合您的應用程式識別碼並進行驗證。 |
| preferred_username | 造訪使用者的主要使用者名稱。 這可能是電子郵件地址、電話號碼或其他識別碼。 |
| 電子郵件 | 使用者的電子郵件地址。 請注意，此欄位可能是空的。 |
| NAME | 人們可讀取的值，可識別權杖的主體。 在此情況下，它將會是買方的名稱。 |
| oid | Microsoft 身分識別系統中的識別碼，可跨應用程式唯一識別使用者。 Microsoft Graph 將會傳回此值做為指定使用者帳戶的 ID 屬性。 |
| tid | 代表買方來源 Azure AD 租使用者的識別碼。 在 MSA 身分識別的案例中，這一律會是 ``9188040d-6c67-4c5b-b112-36a304b66dad`` 。 如需詳細資訊，請參閱下一節中的附注：使用 Microsoft Graph API。 |
| sub | 唯一識別此特定應用程式中使用者的識別碼。 |
|||

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

識別碼權杖包含識別買方的基本資訊，但您的啟用程式可能需要其他詳細資料（例如買方的公司）才能完成上架程式。 使用 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api) 來要求這項資訊，以避免強制使用者再次輸入這些詳細資料。 標準 **使用者。讀取** 許可權預設包含下列資訊。

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

您可以選取其他屬性（例如使用者的公司名稱或使用者位置 (country) ），以納入要求中。 如需詳細資訊，請參閱 [使用者資源類型的屬性](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) 。

大部分向 Azure AD 註冊的應用程式會授與委派的許可權，以讀取其公司 Azure AD 租使用者中的使用者資訊。 針對該資訊 Microsoft Graph 的任何要求都必須伴隨存取權杖以進行驗證。 產生存取權杖的特定步驟將取決於您所使用的技術堆疊，但範例程式碼將包含範例。 如需詳細資訊，請參閱 [代表使用者取得存取權](https://docs.microsoft.com/graph/auth-v2-user)。

> [!NOTE]
> MSA 租使用者 (中具有租使用者識別碼) 的帳戶， ``9188040d-6c67-4c5b-b112-36a304b66dad`` 將不會傳回超過已使用識別碼權杖收集的資訊。 因此，您可以略過這類帳戶對圖形 API 的呼叫。

## <a name="next-steps"></a>後續步驟

- [如何在商業市場中建立 SaaS 供應專案](create-new-saas-offer.md)
