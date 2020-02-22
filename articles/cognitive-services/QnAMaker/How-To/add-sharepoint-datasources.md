---
title: SharePoint 檔案-QnA Maker
description: 將受保護的 SharePoint 資料來源新增至您的知識庫，以使用 Active Directory 保護的問題和答案來擴充知識庫。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: ec77d3ac217991ff191d80249bc86805e7c8ba35
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544159"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>將受保護的 SharePoint 資料來源新增至您的知識庫

將安全的以雲端為基礎的 SharePoint 資料來源新增至您的知識庫，以使用 Active Directory 可保護的問題和答案來擴充知識庫。

當您將受保護的 SharePoint 檔加入至您的知識庫時，身為 QnA Maker 管理員，您必須要求 QnA Maker 的 Active Directory 許可權。 一旦從 Active Directory 管理員授與此許可權，以 QnA Maker 存取 SharePoint，就不需要再次指定。 如果知識庫的每個後續檔都在同一個 SharePoint 資源中，則不需要授權。

如果 QnA Maker 知識庫管理員不是 Active Directory 管理員，您就必須與 Active Directory 管理員通訊，才能完成此程式。

## <a name="prerequisites"></a>必要條件

QnA Maker 使用 Microsoft Graph 的許可權。 如果您的 SharePoint 是內部部署，您將無法從 SharePoint 解壓縮，因為 Microsoft Graph 無法判斷許可權。

## <a name="add-supported-file-types-to-knowledge-base"></a>將支援的檔案類型新增至知識庫

您可以從 SharePoint 網站將所有 QnA Maker 支援的[檔案類型](../Concepts/content-types.md)加入至您的知識庫。 如果檔案資源受到保護，您可能必須授與[許可權](#permissions)。

1. 從程式庫與 SharePoint 網站，選取檔案的省略號功能表，`...`。
1. 複製檔案的 URL。

   ![選取檔案的省略號功能表，然後複製 URL，以取得 SharePoint 檔案 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. 在 QnA Maker 入口網站的 [**設定**] 頁面上，[將 URL 加入](edit-knowledge-base.md#add-datasource)至知識庫。

### <a name="images-with-sharepoint-files"></a>含有 SharePoint 檔案的影像

如果檔案包含影像，則不會解壓縮這些檔案。 將檔案解壓縮至 QnA 組之後，您可以從 QnA Maker 入口網站新增映射。

使用下列 markdown 語法來新增映射：

```markdown
![Explanation or description of image](URL of public image)
```

`[]`方括弧中的文字會說明影像。 括弧中的 URL `()`，是影像的直接連結。

當您在互動式測試面板中測試 QnA 組時，在 QnA Maker 入口網站中，會顯示影像，而不是 markdown 文字。 這會驗證可以從您的用戶端應用程式公開抓取映射。

## <a name="permissions"></a>權限

當 SharePoint 伺服器的安全檔案加入至知識庫時，就會授與許可權。 根據 SharePoint 的設定方式，以及新增檔案的人員許可權而定，這可能需要：

* 沒有其他步驟-新增檔案的人員會擁有所需的擁有權限。
* 由[知識庫管理員](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)和[Active Directory 管理員](#active-directory-manager-grant-file-read-access-to-qna-maker)進行的步驟。

請參閱下面所列的步驟。

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知識庫管理員：在 QnA Maker 入口網站中新增 SharePoint 資料來源

當**QnA Maker 管理員**將受保護的 SharePoint 檔加入至知識庫時，知識庫管理員就會起始 Active Directory 管理員需要完成的許可權要求。

要求會以快顯的方式開始向 Active Directory 帳戶進行驗證。

![驗證使用者帳戶](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker 管理員選取帳戶，Azure Active Directory 系統管理員就會收到通知，指出他們需要允許 QnA Maker 應用程式（而不是 QnA Maker 管理員）存取 SharePoint 資源。 Azure Active Directory 管理員必須針對每個 SharePoint 資源（但不是該資源中的每個檔）執行此動作。

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory 管理員：將檔案讀取權限授與 QnA Maker

Active Directory 管理員（而不是 QnA Maker 管理員）必須藉由選取[此連結](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2F www.qnamaker.ai%3A%2FCreate&state=68)來授權 QnA Maker 入口網站 SharePoint 企業應用程式擁有檔案讀取權限，以授與存取 SharePoint 資源 QnA Maker 的許可權。

![Azure Active Directory manager 以互動方式授與許可權](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>授與 Azure Active Directory 系統管理中心的存取權

1. Active Directory 管理員會登入 Azure 入口網站並開啟 **[企業應用程式](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** 。

1. 搜尋 `QnAMakerPortalSharePoint` 選取 [QnA Maker] 應用程式。

    [![搜尋企業應用程式清單中的 QnAMakerPortalSharePoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 在 [**安全性**] 底下，移至 [**許可權**]。 選取 **[為組織授與系統管理員同意**]。

    [![為 Active Directory 管理員選取 [已驗證的使用者]](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 選取具有許可權的登入帳戶，以授與 Active Directory 的許可權。



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

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

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
> [在您的知識庫上共同作業](collaborate-knowledge-base.md)
