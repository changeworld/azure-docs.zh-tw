---
title: 在商業 Marketplace 中將 Azure 應用程式的 API 上架
description: 在 Microsoft 合作夥伴中心的商業 marketplace 中，Azure 應用程式的 API 必要條件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279754"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>在合作夥伴中心將 Azure 應用程式上架的 API

使用*合作夥伴中心提交 API*以程式設計方式查詢、建立提交，併發布 Azure 供應專案。  如果您的帳戶管理許多供應專案，而您想要將這些供應專案的提交程式自動化並優化，此 API 會很有用。

## <a name="api-prerequisites"></a>API 必要條件

您需要幾個程式設計的資產，才能使用適用于 Azure 產品的合作夥伴中心 API： 

- Azure Active Directory 的應用程式。
- Azure Active Directory （Azure AD）存取權杖。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步驟1：完成使用合作夥伴中心提交 API 的必要條件

在您開始撰寫程式碼以呼叫合作夥伴中心提交 API 之前，請確定您已完成下列必要條件。

- 您 (或您的組織) 必須擁有 Azure AD 目錄，而且您必須具備目錄的[全域系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)權限。 如果您已經使用 Office 365 或其他 Microsoft 所提供的商務服務，您就已經擁有 Azure AD 目錄。 否則，您可以在[合作夥伴中心建立新的 Azure AD](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) ，而不需要額外付費。

- 您必須[將 Azure AD 應用程式與您的合作夥伴中心帳戶建立關聯](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)，並取得您的租使用者識別碼、用戶端識別碼和金鑰。 您需要這些值才能取得 Azure AD 存取權杖，您將會在呼叫 Microsoft Store 提交 API 時使用權杖。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何將 Azure AD 應用程式與您的合作夥伴中心帳戶產生關聯

若要使用 Microsoft Store 提交 API，您必須將 Azure AD 應用程式與您的合作夥伴中心帳戶建立關聯、抓取應用程式的租使用者識別碼和用戶端識別碼，並產生金鑰。 Azure AD 應用程式代表您要從中呼叫合作夥伴中心提交 API 的應用程式或服務。 您需要租用戶識別碼、用戶端識別碼和金鑰，才能取得傳遞給 API 的 Azure AD 存取權杖。

>[!Note]
>您只需要執行這項工作一次。 有了租用戶識別碼、用戶端識別碼和金鑰，每當您必須建立新的 Azure AD 存取權杖時，就可以重複使用它們。

1. 在合作夥伴中心，[將組織的合作夥伴中心帳戶與組織的 Azure AD 目錄建立關聯](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)。
1. 接下來，從 [合作夥伴中心] 的 [**帳戶設定**] 區段中的 [**使用者**] 頁面，新增代表您將用來存取合作夥伴中心帳戶提交之應用程式或服務的[Azure AD 應用程式](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account)。 請確定您指派此應用程式 **[管理員]** 角色。 如果您的 Azure AD 目錄中尚未存在該應用程式，您可以[在合作夥伴中心建立新的 Azure AD 應用程式](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。
1. 返回 **\[使用者\]** 頁面，按一下您 Azure AD 應用程式的名稱來移至應用程式設定，然後複製 **\[租用戶識別碼\]** 和 **\[用戶端識別碼\]** 的值。
1. 按一下 [**新增金鑰**]。 在下列畫面上，複製索引**鍵值**。 您離開這個頁面之後就無法再存取此資訊。 如需詳細資訊，請參閱[管理 Azure AD 應用程式的金鑰](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步驟 2：取得 Azure AD 存取權杖

在您呼叫合作夥伴中心提交 API 中的任何方法之前，您必須先取得您傳遞給 API 中每個方法之**Authorization**標頭的 Azure AD 存取權杖。 在您取得存取權杖之後，您在權杖到期之前有 60 分鐘的時間可以使用權杖。 權杖到期之後，您可以重新整理權杖，以便在未來的 API 呼叫中繼續使用它。

若要取得存取權杖，請遵循[使用用戶端認證的服務對服務呼叫](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)中的指示`HTTP POST` ，將`https://login.microsoftonline.com/<tenant_id>/oauth2/token`傳送至端點。 以下是範例要求：

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

針對中的*tenant_id*值`POST URI`和*client_id*和*client_secret*參數，指定您在上一節中從合作夥伴中心抓取之應用程式的租使用者識別碼、用戶端識別碼和金鑰。 對於 *resource* 參數，您必須指定 `https://api.partner.microsoft.com`。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>步驟 3：使用 Microsoft Store 提交 API

擁有 Azure AD 的存取權杖之後，您可以在合作夥伴中心提交 API 中呼叫方法。 若要建立或更新提交，您通常會以特定順序在合作夥伴中心提交 API 中呼叫多個方法。 如需每個案例的詳細資訊和每個方法的語法，請參閱內嵌 API swagger。

https://apidocs.microsoft.com/services/partneringestion/
