---
title: 設定執行個體和驗證 (CLI)
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 CLI 設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8e82f8974b53224b3e471d1628a1ca5819ce2955
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044470"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>設定 Azure 數位 Twins 實例和驗證 (CLI) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋 **設定新 Azure 數位 Twins 實例** 的步驟，包括建立實例和設定驗證。 完成本文之後，您將會有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會使用 CLI 手動逐一完成這些步驟。
* 若要使用 Azure 入口網站手動完成這些步驟，請參閱本文的入口網站版本： [*如何：設定實例和驗證 (入口網站)*](how-to-set-up-instance-portal.md)。
* 若要使用部署腳本範例來執行自動安裝，請參閱本文的腳本版本： [*如何：設定實例和驗證 (腳本)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>設定 Cloud Shell 工作階段
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>建立 Azure 數位 Twins 實例

在本節中，您將使用 Cloud Shell 命令 **建立 Azure 數位 Twins 的新實例** 。 您將需要提供：
* 將部署實例的資源群組。 如果您還沒有現有的資源群組，您可以使用下列命令立即建立一個：
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 部署的區域。 若要查看哪些區域支援 Azure 數位 Twins，請造訪 [*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 實例的名稱。 如果您的訂用帳戶在已使用指定名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱。

在下列命令中使用這些值來建立實例：

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>驗證成功並收集重要值

如果成功建立實例，Cloud Shell 的結果看起來像這樣，輸出您所建立資源的相關資訊：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="成功建立資源群組和 Azure 數位 Twins 實例的命令視窗":::

請記下輸出中的 Azure 數位 Twins 實例的 **主機名稱**、 **名稱** 和 **resourceGroup** 。 當您繼續使用 Azure 數位 Twins 實例時，這些都是您可能需要的重要值，以設定驗證和相關的 Azure 資源。 如果其他使用者將針對該實例進行程式設計，您應該與這些使用者共用這些值。

> [!TIP]
> 您可以隨時執行，查看這些屬性，以及實例的所有屬性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

您現在已準備好開始使用 Azure 數位 Twins 實例。 接下來，您將授與適當的 Azure 使用者權限來管理它。

## <a name="set-up-user-access-permissions"></a>設定使用者存取權限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

使用下列命令來指派角色 (必須由具有 Azure 訂用帳戶) 中 [足夠許可權](#prerequisites-permission-requirements) 的使用者執行。 此命令會要求您在應指派角色的使用者 Azure AD 帳戶上傳入 *使用者主體名稱* 。 在大部分的情況下，這會比對使用者在 Azure AD 帳戶的電子郵件。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

此命令的結果會輸出已建立之角色指派的相關資訊。

> [!NOTE]
> 如果此命令傳回錯誤，指出 CLI **在圖形資料庫中找不到使用者或服務主體**：
>
> 請改為使用使用者的 *物件識別碼* 來指派角色。 個人 Microsoft 帳戶的使用者可能會發生這種情況 [ (msa) ](https://account.microsoft.com/account)。 
>
> 使用 [Azure Active Directory 使用者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 的 [Azure 入口網站] 頁面，即可選取使用者帳戶並開啟其詳細資料。 複製使用者的 *ObjectID*：
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Azure 入口網站反白顯示 [物件識別碼] 欄位中的 GUID 之使用者頁面的視圖" lightbox="media/includes/user-id.png":::
>
> 然後，針對上面的參數使用使用者的 *物件識別碼* ，重複執行角色指派清單命令 `assignee` 。

### <a name="verify-success"></a>確認是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

您現在已準備好開始使用 Azure 數位 Twins 實例，並已獲指派管理該實例的許可權。

## <a name="next-steps"></a>下一步

使用 Azure 數位 Twins CLI 命令，在您的實例上測試個別 REST API 呼叫： 
* [az dt 參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [操作說明：*使用 Azure Digital Twins CLI*](how-to-use-cli.md)

或者，請參閱如何使用驗證碼將用戶端應用程式連接到您的實例：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)