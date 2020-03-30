---
title: 商業應用商店中 Azure 應用的載入 API
description: 在 Microsoft 合作夥伴中心的商業市場中，Azure 應用的 API 先決條件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279754"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>用於在合作夥伴中心中載入 Azure 應用的 API

使用*合作夥伴中心提交 API*以程式設計方式查詢、為 Azure 產品/服務創建提交併發布 Azure 產品/服務。  如果您的帳戶管理許多產品/服務，並且希望自動和優化這些優惠的提交過程，則此 API 非常有用。

## <a name="api-prerequisites"></a>API 先決條件

要使用 Azure 產品的合作夥伴中心 API，您需要一些程式設計資源： 

- Azure 活動目錄應用程式。
- Azure 活動目錄 （Azure AD） 訪問權杖。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步驟 1：完成使用合作夥伴中心提交 API 的先決條件

在開始編寫代碼以調用合作夥伴中心提交 API 之前，請確保您已完成以下先決條件。

- 您 (或您的組織) 必須擁有 Azure AD 目錄，而且您必須具備目錄的[全域系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)權限。 如果您已經使用 Office 365 或其他 Microsoft 所提供的商務服務，您就已經擁有 Azure AD 目錄。 否則，您可以在[合作夥伴中心中創建新的 Azure AD，](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)無需支付額外費用。

- 您必須[將 Azure AD 應用程式與合作夥伴中心帳戶相關聯，](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)並獲取租戶 ID、用戶端 ID 和金鑰。 您需要這些值才能取得 Azure AD 存取權杖，您將會在呼叫 Microsoft Store 提交 API 時使用權杖。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>如何將 Azure AD 應用程式與合作夥伴中心帳戶關聯

若要使用 Microsoft 應用商店提交 API，必須將 Azure AD 應用程式與合作夥伴中心帳戶關聯，檢索應用程式的租戶 ID 和用戶端 ID，並生成金鑰。 Azure AD 應用程式表示要從中調用合作夥伴中心提交 API 的應用或服務。 您需要租用戶識別碼、用戶端識別碼和金鑰，才能取得傳遞給 API 的 Azure AD 存取權杖。

>[!Note]
>您只需要執行此任務一次。 有了租用戶識別碼、用戶端識別碼和金鑰，每當您必須建立新的 Azure AD 存取權杖時，就可以重複使用它們。

1. 在合作夥伴中心中，[將組織的合作夥伴中心帳戶與組織的 Azure AD 目錄相關聯](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)。
1. 接下來，從合作夥伴中心"**帳戶設置"** 部分中的 **"使用者"** 頁面[中，添加 Azure AD 應用程式](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account)，該應用程式表示將用於訪問合作夥伴中心帳戶提交的應用或服務。 請確定您指派此應用程式 **[管理員]** 角色。 如果應用程式在 Azure AD 目錄中尚不存在，則可以[在合作夥伴中心中創建新的 Azure AD 應用程式](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)。
1. 返回 **\[使用者\]** 頁面，按一下您 Azure AD 應用程式的名稱來移至應用程式設定，然後複製 **\[租用戶識別碼\]** 和 **\[用戶端識別碼\]** 的值。
1. 按一下 **"添加新鍵**"。 在以下螢幕上，向下複製 **"金鑰"** 值。 您離開這個頁面之後就無法再存取此資訊。 如需詳細資訊，請參閱[管理 Azure AD 應用程式的金鑰](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步驟 2：取得 Azure AD 存取權杖

在調用合作夥伴中心提交 API 中的任何方法之前，必須首先獲取 Azure AD 訪問權杖，該權杖將傳遞給 API 中每個方法**的授權**標頭。 在您取得存取權杖之後，您在權杖到期之前有 60 分鐘的時間可以使用權杖。 權杖過期後，可以刷新權杖，以便在以後調用 API 時繼續使用它。

要獲取訪問權杖，請按照[服務到服務調用中的說明使用用戶端憑據](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)將 發送到`HTTP POST``https://login.microsoftonline.com/<tenant_id>/oauth2/token`終結點。 以下是範例要求：

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

對於 和*client_id* `POST URI`和*client_secret*參數中的*tenant_id*值，請在上一節中指定從合作夥伴中心檢索的應用程式的租戶 ID、用戶端 ID 和金鑰。 對於 *resource* 參數，您必須指定 `https://api.partner.microsoft.com`。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>步驟 3：使用 Microsoft Store 提交 API

獲得 Azure AD 訪問權杖後，可以在合作夥伴中心提交 API 中調用方法。 要創建或更新提交，通常按特定順序在合作夥伴中心提交 API 中調用多個方法。 有關每個方案以及每個方法的語法的資訊，請參閱引入 API 揮霍。

https://apidocs.microsoft.com/services/partneringestion/
