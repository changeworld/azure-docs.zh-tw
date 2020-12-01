---
title: SharePoint 檔案-QnA Maker
description: 將受保護的 SharePoint 資料來源新增至您的知識庫，以使用可能受到 Active Directory 保護的問題和答案來擴充知識庫。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c231ac95841043e5576f064e683dd86d9695b108
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353182"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>將受保護的 SharePoint 資料來源新增至您的知識庫

將安全的雲端式 SharePoint 資料來源新增至您的知識庫，以使用可能會受到 Active Directory 保護的問題和答案來擴充知識庫。

當您將受保護的 SharePoint 檔加入至知識庫時，您必須為 QnA Maker 管理員要求 QnA Maker 的 Active Directory 許可權。 一旦將此許可權提供給 Active Directory 管理員來 QnA Maker，才能存取 SharePoint，就不必再指定一次。 如果您在相同的 SharePoint 資源中加入知識庫，每個後續的檔都不需要授權。

如果 QnA Maker 的知識庫管理員不是 Active Directory manager，您必須與 Active Directory 管理員通訊，才能完成此程式。

## <a name="prerequisites"></a>必要條件

* 以雲端為基礎的 SharePoint QnA Maker 使用 Microsoft Graph 來取得許可權。 如果您的 SharePoint 是內部部署，您將無法從 SharePoint 進行解壓縮，因為 Microsoft Graph 無法判斷許可權。
* URL 格式-QnA Maker 僅支援為了共用而產生的 SharePoint url，且格式為 `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>將支援的檔案類型新增至知識庫

您可以從 SharePoint 網站將所有 QnA Maker 支援的 [檔案類型](../index.yml) 加入至知識庫。 如果檔案資源受到保護，您可能必須授與 [許可權](#permissions) 。

1. 從具有 SharePoint 網站的文件庫中，選取檔案的省略號功能表 `...` 。
1. 複製檔案的 URL。

   ![選取檔案的省略號功能表然後複製 URL，以取得 SharePoint 檔案 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. 在 QnA Maker 入口網站的 [ **設定** ] 頁面上，將 URL 新增至知識庫。

### <a name="images-with-sharepoint-files"></a>具有 SharePoint 檔案的影像

如果檔案包含影像，則不會解壓縮這些檔案。 將檔案解壓縮至 QnA 組之後，您可以從 QnA Maker 入口網站新增映射。

使用下列 markdown 語法新增映射：

```markdown
![Explanation or description of image](URL of public image)
```

方括弧中的文字會 `[]` 說明影像。 括弧中的 URL `()` 是影像的直接連結。

當您在互動式測試面板中測試 QnA 組時，會在 QnA Maker 入口網站中顯示影像，而不是 markdown 文字。 這會驗證是否可從您的用戶端應用程式公開取出映射。

## <a name="permissions"></a>權限

從 SharePoint 伺服器將安全的檔案新增至知識庫時，即會授與許可權。 根據 SharePoint 的設定方式，以及加入檔案的人員的許可權，這可能需要：

* 沒有其他步驟-新增檔案的人員具有所需的擁有權限。
* [知識庫管理員](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)和[Active Directory 管理員](#active-directory-manager-grant-file-read-access-to-qna-maker)的步驟。

請參閱下列步驟。

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知識基底管理員：在 QnA Maker 入口網站中加入 SharePoint 資料來源

當 **QnA Maker 管理員** 將安全的 SharePoint 檔加入至知識庫時，知識庫管理員就會起始 Active Directory 管理員需要完成的許可權要求。

要求會從快顯視窗開始驗證 Active Directory 帳戶。

![驗證使用者帳戶](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker 管理員選取帳戶，Azure Active Directory 系統管理員將會收到通知，指出他們必須允許 QnA Maker 應用程式 (QnA Maker 管理員) 存取 SharePoint 資源。 Azure Active Directory 管理員必須為每個 SharePoint 資源（但不是該資源中的每個檔）進行此動作。

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 管理員：將檔案讀取權限授與 QnA Maker

Active Directory manager (不是 QnA Maker 管理員) 需要將存取權授與 QnA Maker，以存取 SharePoint 資源，方法是選取 [此連結](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) ，以授權 QnA Maker Portal SharePoint enterprise 應用程式擁有檔案讀取權限。

![Azure Active Directory 管理員以互動方式授與許可權](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>從 Azure Active Directory 系統管理中心授與存取權

1. Active Directory 管理員登入 Azure 入口網站並開啟 **[企業應用程式](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。

1. 搜尋 `QnAMakerPortalSharePoint` 選取 QnA Maker 應用程式。

    [![搜尋企業應用程式清單中的 QnAMakerPortalSharePoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 在 [ **安全性**] 底下，移至 [ **許可權**]。 選取 **[授與組織的管理員同意**]。

    [![為 Active Directory 系統管理員選取已驗證的使用者](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 選取具有授與 Active Directory 許可權的 Sign-On 帳戶。



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [對知識庫進行共同作業](../index.yml)