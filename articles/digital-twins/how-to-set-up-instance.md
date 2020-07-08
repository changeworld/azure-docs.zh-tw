---
title: 建立 Azure Digital Twins 執行個體
titleSuffix: Azure Digital Twins
description: 請參閱如何設定 Azure 數位 Twins 服務的實例。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057478"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>設定 Azure Digital Twins 執行個體

本文將逐步引導您完成設定新的 Azure 數位 Twins 實例的基本步驟。 這包括建立實例，並將[Azure Active Directory （AAD）](../active-directory/fundamentals/active-directory-whatis.md)許可權指派給您自己的實例。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>設定 Azure Digital Twins 執行個體

接下來，您將建立新的 Azure 資源群組，以用於此操作說明。 然後，您可以在該資源群組內**建立新的 Azure 數位 Twins 實例**。 

您也需要提供實例的名稱，然後選擇部署的區域。 若要查看哪些區域支援 Azure 數位 Twins，請造訪[依區域提供的 azure 產品](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。

>[!NOTE]
> 新實例的名稱在區域內必須是唯一的（也就是說，如果該區域中的另一個 Azure 數位 Twins 實例已經使用您選擇的名稱，您就必須挑選不同的名稱）。

使用下列命令建立資源群組和實例：

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

這些命令的結果看起來像這樣，輸出您所建立之資源的相關資訊：

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="成功建立資源群組和 Azure 數位 Twins 實例的命令視窗":::

請記下輸出中的 Azure 數位 Twins 實例的*主機名稱*、*名稱*和*resourceGroup* 。 當您繼續使用 Azure 數位 Twins 實例時，這些都是您可能需要的所有重要值，以設定驗證和相關的 Azure 資源。

> [!TIP]
> 您可以隨時執行，查看這些屬性，以及實例的所有屬性 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

### <a name="assign-azure-active-directory-permissions"></a>指派 Azure Active Directory 許可權

Azure 數位 Twins 會使用[Azure Active Directory （AAD）](../active-directory/fundamentals/active-directory-whatis.md)來進行角色型存取控制（RBAC）。 這表示您必須先為自己指派具有這些許可權的角色，才可以對 Azure 數位 Twins 實例進行資料平面呼叫。

若要搭配用戶端應用程式使用 Azure 數位 Twins，您也必須確定您的用戶端應用程式可以針對 Azure 數位 Twins 進行驗證。 這是藉由設定 Azure Active Directory （AAD）應用程式註冊來完成，您可以在[如何：驗證用戶端應用程式](how-to-authenticate-client.md)中閱讀相關資訊。

#### <a name="assign-yourself-a-role"></a>為自己指派角色

使用您的 Azure 訂用帳戶上與 AAD 租使用者相關聯的電子郵件，在 Azure 數位 Twins 實例中建立自己的角色指派。 

若要這麼做，您必須將其分類為 Azure 訂用帳戶中的擁有者。 您可以執行命令來進行檢查 `az role assignment list --assignee <your-Azure-email>` ，並在輸出中驗證*roleDefinitionName*值是否為*Owner*。 如果您發現值是「*參與者*」或「*擁有*者」以外的專案，請洽詢您的訂用帳戶管理員，以授與您訂用帳戶中的許可權，以便提升您的角色。

身為訂用帳戶的擁有者，您可以使用下列命令，將您的使用者指派給 Azure 數位 Twins 實例的擁有者角色：

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

此命令的結果是您所建立角色指派的輸出資訊。

> [!TIP]
> 如果您改為取得*400： BadRequest*錯誤，請執行下列命令來取得使用者的*ObjectID* ：
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> 然後，使用您的使用者*物件識別碼*來取代您的電子郵件，以重複 [角色指派] 命令。

您現在已有 Azure 數位 Twins 實例可以開始使用。

## <a name="next-steps"></a>後續步驟

請參閱如何設定和驗證用戶端應用程式以使用您的實例：
* [如何：驗證用戶端應用程式](how-to-authenticate-client.md)
