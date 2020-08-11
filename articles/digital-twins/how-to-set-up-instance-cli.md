---
title: 設定執行個體和驗證 (CLI)
titleSuffix: Azure Digital Twins
description: 請參閱如何使用 CLI 設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3c7e4887610f30113b81421396500416d04c5e5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078473"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>設定 Azure 數位 Twins 實例和 (CLI 的驗證) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋**設定新的 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將擁有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會使用 CLI 手動逐一執行這些步驟。
* 若要使用 Azure 入口網站手動執行這些步驟，請參閱本文的入口網站版本：[*如何：設定實例和驗證 (入口網站) *](how-to-set-up-instance-portal.md)。
* 若要使用部署腳本範例執行自動安裝，請參閱本文的腳本版本：[*如何：設定實例和驗證 (腳本) *](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>建立 Azure 數位 Twins 實例

在本節中，您將使用 Cloud Shell 命令來**建立新的 Azure 數位 Twins 實例**。 您必須提供：
* 要在其中部署的資源群組。 如果您還沒有考慮到現有的資源群組，可以使用此命令立即建立一個：
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 部署的區域。 若要查看哪些區域支援 Azure 數位 Twins，請造訪[*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 實例的名稱。 新實例的名稱在您的 (訂用帳戶區域內必須是唯一的，這表示如果您的訂用帳戶在已使用所選名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱) 。

在下列命令中使用這些值來建立實例：

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>確認成功並收集重要的值

如果成功建立實例，Cloud Shell 中的結果會看起來像這樣，輸出您所建立之資源的相關資訊：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="成功建立資源群組和 Azure 數位 Twins 實例的命令視窗":::

請記下輸出中的 Azure 數位 Twins 實例的*主機名稱*、*名稱*和*resourceGroup* 。 當您繼續使用 Azure 數位 Twins 實例時，這些都是您可能需要的重要值，以設定驗證和相關的 Azure 資源。 如果其他使用者會針對該實例進行程式設計，您應該與他們共用這些值。

> [!TIP]
> 您可以隨時執行，查看這些屬性，以及實例的所有屬性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

您現在已有 Azure 數位 Twins 實例可以開始使用。 接下來，您會提供適當的 Azure 使用者許可權來進行管理。

## <a name="set-up-user-access-permissions"></a>設定使用者存取權限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

使用下列命令來指派角色 (必須由 Azure 訂用帳戶) 中具有[足夠許可權](#prerequisites-permission-requirements)的使用者執行。 此命令會要求您針對應指派角色的使用者，傳入 Azure AD 帳戶的*使用者主體名稱*。 在大部分情況下，這會比對使用者在 Azure AD 帳戶上的電子郵件。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的結果會輸出已建立之角色指派的相關資訊。

> [!NOTE]
> 如果此命令傳回錯誤，指出 CLI**在圖形資料庫中找不到使用者或服務主體**：
>
> 請改為使用使用者的*物件識別碼*來指派角色。 個人[Microsoft 帳戶 (msa) ](https://account.microsoft.com/account)的使用者可能會發生這種情況。 
>
> 使用[Azure Active Directory 使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)的 [Azure 入口網站] 頁面來選取使用者帳戶，並開啟其詳細資料。 複製使用者的*ObjectID*：
>
> :::image type="content" source="media/includes/user-id.png" alt-text="[物件識別碼] 欄位中顯示 GUID 的 Azure 入口網站中的使用者網頁檢視" lightbox="media/includes/user-id.png":::
>
> 然後，使用上述參數的使用者*物件識別碼*，重複 [角色指派清單] 命令 `assignee` 。

### <a name="verify-success"></a>確認是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

您現在已有 Azure 數位 Twins 實例可開始使用，並已指派許可權來管理它。 接下來，您將設定用戶端應用程式存取它的許可權。

## <a name="set-up-access-permissions-for-client-applications"></a>設定用戶端應用程式的存取權限

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

若要建立應用程式註冊，您必須提供 Azure 數位 Twins Api 的資源識別碼，以及 API 的基準許可權。

在您的工作目錄中，建立新的檔案，並輸入下列 JSON 程式碼片段來設定這些詳細資料： 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

將此檔案儲存為_**manifest.js開啟**_。

> [!NOTE] 
> 有一些地方可以使用「易記」、人類可讀的字串，做 `https://digitaltwins.azure.net` 為 Azure 數位 Twins 資源應用程式識別碼，而不是 GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 。 比方說，這整篇檔集中的許多範例都是使用驗證搭配 MSAL 程式庫，而易記的字串則可以用於該程式。 不過，在建立應用程式註冊的這個步驟中，需要識別碼的 GUID 形式，如上所示。 

接下來，您會將此檔案上傳至 Cloud Shell。 在 Cloud Shell 視窗中，按一下 [上傳/下載檔案] 圖示，然後選擇 [上傳]。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="顯示上傳選項選取範圍的 Cloud Shell 視窗":::
流覽至您剛建立的*manifest.js* ，然後按 [開啟]。

接下來，執行下列命令來建立應用程式註冊 (視需要取代預留位置) ：

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

以下是此命令的輸出摘要，其中顯示您已建立之註冊的相關資訊：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Cloud Shell 新 Azure AD 應用程式註冊的輸出":::

### <a name="verify-success"></a>確認是否成功

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，請確認已在註冊上正確設定*上傳manifest.js*的設定。 若要這麼做，請從功能表列選取 [*資訊清單*]，以查看應用程式註冊的資訊清單代碼。 在程式碼視窗的底部，尋找您的*manifest.js*中的欄位（位於）底下 `requiredResourceAccess` ：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>收集重要值

接下來，從功能表列選取 *[總覽*]，以查看應用程式註冊的詳細資料：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="應用程式註冊重要值的入口網站視圖":::

請記下**您**頁面上顯示的*應用程式 (用戶端) id*和*目錄 (租使用者) 識別碼*。 稍後會需要這些值，以對[Azure 數位 Twins api 驗證用戶端應用程式](how-to-authenticate-client.md)。 如果您不是要為這類應用程式撰寫程式碼的人員，您將需要與即將擁有的人員共用這些值。

### <a name="other-possible-steps-for-your-organization"></a>貴組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

請參閱如何撰寫用戶端應用程式的驗證碼，將您的用戶端應用程式連接到您的實例：
* [*如何：撰寫應用程式驗證碼*](how-to-authenticate-client.md)