---
title: 共用點檔 - QnA 製造商
description: 將安全的 SharePoint 資料來源添加到您的知識庫，以豐富知識庫，其中可以用 Active Directory 保護的問題和答案。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294887"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>將安全的 SharePoint 資料來源添加到知識庫

將安全的基於雲的 SharePoint 資料來源添加到您的知識庫，以豐富知識庫，提供可能通過 Active Directory 保護的問題和答案。

當您將安全的 SharePoint 文檔添加到知識庫時，作為 QnA 製造商管理器，您必須請求 QnA Maker 的活動目錄許可權。 一旦從 Active Directory 管理器授予 QnA Maker 以訪問 SharePoint 的許可權，就不必再次授予它。 如果每個後續文檔添加到知識庫將不需要授權，如果它位於同一 SharePoint 資源中。

如果 QnA Maker 知識庫管理器不是活動目錄管理器，則需要與活動目錄管理器通信才能完成此過程。

## <a name="prerequisites"></a>Prerequisites

* 基於雲的 SharePoint - QnA 製造商使用 Microsoft 圖形獲得許可權。 如果您的 SharePoint 是本地的，您將無法從 SharePoint 中提取，因為 Microsoft 圖形無法確定許可權。
* URL 格式 - QnA Maker 僅支援為共用而生成的 SharePoint URL，該 URL 具有格式`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>將受支援的檔案類型添加到知識庫

您可以將所有 QnA Maker 支援[的檔案類型](../Concepts/content-types.md)從 SharePoint 網站添加到您的知識庫。 如果檔資源是安全的，您可能需要授予[許可權](#permissions)。

1. 從具有 SharePoint 網站的庫中，選擇檔的省略號功能表`...`。
1. 複製檔的 URL。

   ![通過選擇檔的省略號功能表，然後複製 URL，獲取 SharePoint 檔 URL。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. 在"設置 **"** 頁上的 QnA Maker 門戶中，[將 URL 添加到](manage-knowledge-bases.md#edit-knowledge-base)知識庫。

### <a name="images-with-sharepoint-files"></a>具有 SharePoint 檔的映射

如果檔包含圖像，則不會提取圖像。 在將檔提取到 QnA 對後，可以從 QnA Maker 門戶添加圖像。

使用以下標記語法添加圖像：

```markdown
![Explanation or description of image](URL of public image)
```

方括弧中的文本`[]`，解釋圖像。 括弧`()`中的 URL 是指向圖像的直接連結。

在互動式測試面板（在 QnA Maker 門戶中）中測試 QnA 對時，將顯示圖像，而不是標記文本。 這將驗證可以從用戶端應用程式公開檢索映射。

## <a name="permissions"></a>權限

當從 SharePoint 伺服器添加安全檔到知識庫時，將授予許可權。 根據 SharePoint 的設置方式和添加檔的人員的許可權，這可能需要：

* 沒有其他步驟 - 添加檔的人具有所需的擁有權限。
* [知識庫管理器](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)和[活動目錄管理器](#active-directory-manager-grant-file-read-access-to-qna-maker)的步驟。

請參閱下面列出的步驟。

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>知識庫管理器：在 QnA Maker 門戶中添加 SharePoint 資料來源

當**QnA Maker 管理器**將安全的 SharePoint 文檔添加到知識庫時，知識庫管理器將發起活動目錄管理器需要完成的許可權請求。

請求以快顯視窗開頭，以對活動目錄帳戶進行身份驗證。

![驗證使用者帳戶](../media/add-sharepoint-datasources/authenticate-user-account.png)

一旦 QnA Maker 管理器選擇帳戶，Azure 活動目錄管理員將收到一條通知，指出他們需要允許 QnA Maker 應用（而不是 QnA Maker 管理器）訪問 SharePoint 資源。 Azure 活動目錄管理器需要為每個 SharePoint 資源執行此操作，但不是該資源中的每個文檔。

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>活動目錄管理器：授予對 QnA 製造商的檔讀取存取許可權

活動目錄管理器（不是 QnA Maker 管理器）需要通過選擇[此連結](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)授權 QnA Maker 門戶 SharePoint 企業應用具有檔讀取權限，授予訪問 QnA Maker 以訪問 SharePoint 資源的存取權限。

![Azure 活動目錄管理器以對話模式授予許可權](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>從 Azure 活動目錄管理中心授予存取權限

1. 活動目錄管理器登錄到 Azure 門戶並打開**[企業應用程式](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**。

1. 搜索`QnAMakerPortalSharePoint`選擇 QnA 製造商應用。

    [![在企業應用清單中搜索 QnAMakerPortalSharePoint](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. 在 **"安全"下**，轉到**許可權**。 選擇 **"授予組織管理員同意**"。

    [![為活動目錄管理員選擇經過身份驗證的使用者](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. 選擇具有授予活動目錄許可權的登錄帳戶。



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
> [對知識庫進行共同作業](collaborate-knowledge-base.md)
