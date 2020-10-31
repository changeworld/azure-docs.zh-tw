---
title: 用來在 Microsoft 商業市集中將 Azure 應用程式上架的合作夥伴中心提交 API
description: 了解在 Microsoft 合作夥伴中心上的商業市集中，針對 Azure 應用程式使用合作夥伴中心提交 API 的先決條件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 87f27f316914f3efce5a750f50471c65dceca84e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127836"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>用來在合作夥伴中心中將 Azure 應用程式上架的合作夥伴中心提交 API

使用 *合作夥伴中心提交 API* 來以程式設計方式針對 Azure 供應項目進行查詢、建立提交及進行發佈。  在您的帳戶會管理許多供應項目，且您想要將這些供應項目的提交程序自動化及最佳化的情況下，此 API 會相當有用。

## <a name="api-prerequisites"></a>API 必要條件

若要針對 Azure 產品使用合作夥伴中心提交 API，您需要幾個程式設計資產： 

- Azure Active Directory 應用程式。
- Azure Active Directory (Azure AD) 存取權杖。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步驟 1:完成使用合作夥伴中心提交 API 的先決條件

在您開始撰寫程式碼以呼叫合作夥伴中心提交 API 之前，請確定您已完成下列先決條件。

- 您 (或您的組織) 必須具有 Azure AD 目錄，且您必須具備該目錄的[全域管理員](../../active-directory/roles/permissions-reference.md) \(部分機器翻譯\) 權限。 如果您已經使用 Microsoft 365 或 Microsoft 的其他商務服務，則您已經有 Azure AD 目錄。 否則，您可以免費地[在合作夥伴中心中建立新的 Azure AD](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) \(部分機器翻譯\)。

- 您必須[將 Azure AD 應用程式與您的合作夥伴中心帳戶建立關聯](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) \(部分機器翻譯\)，並取得您的租用戶識別碼、用戶端識別碼及金鑰。 您需要這些值才能取得 Azure AD 存取權杖，而您將會在呼叫 Microsoft Store 提交 API 時使用該權杖。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何將 Azure AD 應用程式與您的合作夥伴中心帳戶建立關聯

若要使用 Microsoft Store 提交 API，您必須將 Azure AD 應用程式與您的合作夥伴中心帳戶建立關聯，擷取該應用程式的租用戶識別碼和用戶端識別碼，並產生金鑰。 Azure AD 應用程式代表您想要從中呼叫合作夥伴中心提交 API 的應用程式或服務。 您需要租用戶識別碼、用戶端識別碼及金鑰，以取得您會傳遞給 API 的 Azure AD 存取權杖。

>[!Note]
>您只需要執行此工作一次。 在您取得租用戶識別碼、用戶端識別碼及金鑰之後，您便可以在未來需要建立新的 Azure AD 存取權杖時重複加以使用。

1. 在合作夥伴中心中，[將您組織的合作夥伴中心帳戶與您組織的 Azure AD 目錄建立關聯](/windows/uwp/publish/associate-azure-ad-with-partner-center) \(部分機器翻譯\)。
1. 接下來，在合作夥伴中心 [帳戶設定] 區段中的 [使用者] 頁面上[新增 Azure AD 應用程式](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) \(部分機器翻譯\)，此應用程式代表您將用來存取您合作夥伴中心帳戶之提交的應用程式或服務。 確定您將此應用程式指派為 [管理員] 角色。 如果該應用程式尚未存在於您的 Azure AD 目錄，您可以[在合作夥伴中心中建立新的 Azure AD 應用程式](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account) \(部分機器翻譯\)。
1. 返回 [使用者] 頁面，按一下您 Azure AD 應用程式的名稱以移至應用程式設定，然後複製 [租用戶識別碼] 和 [用戶端識別碼] 值。
1. 按一下 [新增金鑰]。 在後續畫面上，複製 [金鑰] 值。 在您離開此頁面之後，便無法再次存取此資訊。 如需詳細資訊，請參閱[管理 Azure AD 應用程式的金鑰](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys) \(部分機器翻譯\)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步驟 2:取得 Azure AD 存取權杖

在您呼叫合作夥伴中心提交 API 中的任何方法之前，您必須先取得 Azure AD 存取權杖，以將其傳遞至 API 中每個方法的 **Authorization** 標頭。 在您取得存取權杖之後，您有 60 分鐘的使用時間，之後其便會到期。 在權杖到期之後，您可以重新整理權杖以繼續在未來呼叫 API 時加以使用。

若要取得存取權杖，請遵循[使用用戶端認證的服務對服務呼叫](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) \(部分機器翻譯\) 中的指示，將 `HTTP POST` 傳送到 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 端點。 以下是範例要求：

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

針對 `POST URI` 中的 *tenant_id* 值，以及 *client_id* 和 *client_secret* 參數，請指定您在上一節中從合作夥伴中心加以擷取的應用程式租用戶識別碼、用戶端識別碼及金鑰。 針對 *resource* 參數，您必須指定 `https://api.partner.microsoft.com`。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>步驟 3：使用 Microsoft Store 提交 API

在您擁有 Azure AD 存取權杖之後，便可以呼叫合作夥伴中心提交 API 中的方法。 若要建立或更新提交，您通常會以特定順序呼叫合作夥伴中心提交 API 中的多個方法。 如需每個案例及每個方法之語法的相關資訊，請參閱擷取 API Swagger。

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>後續步驟

* 了解如何[建立 Azure VM 技術資產](create-azure-container-technical-assets.md)
* 了解如何[建立 Azure 容器供應項目](create-azure-container-offer.md)