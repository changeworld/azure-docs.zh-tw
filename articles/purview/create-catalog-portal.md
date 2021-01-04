---
title: 快速入門：在 Azure 入口網站中建立 Azure Purview 帳戶 (預覽)
description: 本快速入門說明如何建立 Azure Purview 帳戶，並設定權限以開始使用。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: fe83425230a157be245e3aa1ad088c32596ea5ed
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693367"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Azure Purview 帳戶

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未發行的版本) 的其他法律條款。

在本快速入門中，您會建立 Azure Purview 帳戶。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您自己的 [Azure Active Directory 租用戶](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)。

* 您的帳戶必須擁有在訂用帳戶中建立資源的權限

* 如果您有封鎖所有應用程式建立 **儲存體帳戶** 和 **EventHub 命名空間** 的 **Azure 原則**，則需要使用標籤來建立原則例外狀況，您可在建立 Purview 帳戶的過程中輸入。 主要的原因是每個建立的 Purview 帳戶都必須建立受控資源群組；且此資源群組中必須擁有儲存體帳戶和 EventHub 命名空間。

    > [!important]
    > 如果您沒有 Azure 原則或現有的 Azure 原則不會封鎖 **儲存體帳戶** 和 **EventHub 命名空間** 的建立，則不需要遵循此步驟。

    1. 瀏覽至 Azure 入口網站並搜尋 **原則**
    1. 遵循[建立自訂原則定義](https://docs.microsoft.com/azure/governance/policy/tutorials/create-custom-policy-definition)或修改現有的原則，以新增兩個具有 `not` 運算子和 `resourceBypass` 標籤的例外狀況：

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > 標籤位於 `resourceBypass` 旁且可以是任何內容，只要原則可以偵測到標籤，您就可以在後續建立 Purview 的步驟時定義值。

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="螢幕擷取畫面，顯示如何建立原則定義。":::

    1. 使用所建立的自訂原則[建立原則指派](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)。

        [ ![螢幕擷取畫面，顯示如何建立原則指派](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="configure-your-subscription"></a>設定您的訂用帳戶

如有必要，請遵循下列步驟來設定您的訂用帳戶，讓 Azure Purview 在您的訂用帳戶中執行：

   1. 在 Azure 入口網站中，搜尋並選取 [訂用帳戶]。

   1. 從訂用帳戶的清單中，選取您要使用的訂用帳戶。 需要訂用帳戶的系統管理存取權限。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="顯示如何在 Azure 入口網站中選取訂用帳戶的螢幕擷取畫面。":::

   1. 對於您的訂用帳戶，選取 **資源提供者**。 在 **資源提供者** 窗格中，搜尋並註冊下列三個資源提供者： 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      如果這些資源提供者未註冊，請選取 [註冊] 以註冊。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="顯示如何在 Azure 入口網站中註冊 Microsoft.Purview 資源提供者的螢幕擷取畫面。":::

## <a name="create-an-azure-purview-account-instance"></a>建立 Azure Purview 帳戶執行個體

1. 移至 Azure 入口網站中的 **Purview 帳戶** 頁面，然後選取 [新增] 以建立新的 Azure Purview 帳戶。 或者，您可以移至市集搜尋  **帳戶**，然後選取 [建立]。 請注意，您一次只能新增一個 Azure Purview 帳戶。

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="螢幕擷取畫面，顯示如何在 Azure 入口網站中建立 Azure Purview 帳戶執行個體。":::

1. 在 **基本** 索引標籤中，執行下列動作：
    1. 選取 [資源群組]  。
    1. 針對您的目錄輸入 **Purview 帳戶名稱**。 不允許空格和符號。
    1. 選取 [位置]，然後選取 [下一步:組態]。
1. 在 **組態** 索引標籤上，選取所需的 **平台大小** - 允許的值為 4 個容量單位 (CU) 和 16 CU。 完成時，選取 [下一步:標籤]。
1. 在 **標籤** 索引標籤上，您可以選擇性地新增一或多個標籤。 這些標籤僅供 Azure 入口網站使用， Azure Purview 無法使用。 

    > [!Note] 
    > 如果您有 **Azure 原則**，而且需要在 **必要條件** 中新增例外狀況，則必須新增正確的標籤。 例如，您可以新增 `resourceBypass` 標籤：:::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="將標籤新增至 Purview 帳戶。":::

1. 選取 [檢閱與建立]，然後選取 [建立]。 需要數分鐘才能建立完成。 新建立的 Azure Purview 帳戶執行個體會出現在 **Purview 帳戶** 頁面上的清單中。
1. 當新的帳戶佈建完成時，選取 [移至資源]。

    > [!Note]
    > 如果佈建因 `Conflict` 狀態而失敗，表示有 Azure 原則不讓 Purview 建立 **儲存體帳戶** 和 **EventHub 命名空間**。 您需要執行所有 **必要條件** 步驟來新增例外狀況。
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Purview衝突錯誤訊息":::

1. 選取 [啟動 Purview 帳戶]。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="啟動 Azure Purview 帳戶目錄的選取項目螢幕擷取畫面。":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>將安全性主體新增至資料平面角色

在您或您的小組開始使用 Azure Purview 之前，必須將一或多個安全性主體新增至其中一個預先定義的資料平面角色中：**Purview 資料讀取器**、**Purview 資料保存者** 或 **Purview 資料來源管理員**。 如需 Azure Purview 資料目錄權限的詳細資訊，請參閱[目錄權限](catalog-permissions.md)。

若要將安全性主體新增至 Azure Purview 帳戶中的 **Purview 資料保存者** 資料平面角色：

1. 移至 Azure 入口網站中的 [**Purview 帳戶**](https://aka.ms/purviewportal)頁面。

1. 選取要修改的 Azure Purview 帳戶。

1. 在 **Purview 帳戶** 頁面上，選取 [存取控制 (IAM)] 索引標籤

1. 按一下 [+ 新增]

如果按一下 [新增]，您會看到兩個顯示已標記 (已停用) 的選項，這表示您沒有適當的權限可將任何人新增至 Azure Purview 帳戶上的資料平面角色。 您必須在您的 Azure Purview 帳戶上尋找擁有者、使用者存取管理員或具有角色指派授權的其他人。 您可以選取 **角色指派** 索引標籤，然後向下捲動以尋找擁有者或使用者存取管理員，以尋找並連絡適當的人員。

1. 選取 [新增角色指派]。

1. 針對 **Purview 資料保存者角色** 或 **Purview 資料來源管理員角色** 中的角色類型，請依服務主體的用途而定 (如需詳細資訊，請參閱 [目錄權限](catalog-permissions.md)和 [ Azure Active Directory 中的應用程式和服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals))。

1. 保留 [存取權指派對象] 中的預設值 **使用者、群組或服務主體**。

1. 針對 **選取** 輸入要指派的使用者、Azure Active Directory 群組或服務主體，然後在結果窗格中按一下其名稱。

1. 按一下 [ **儲存**]。

## <a name="clean-up-resources"></a>清除資源

如果不再需要此 Azure Purview 帳戶，請使用下列步驟刪除：

1. 移至 Azure 入口網站中的 **Purview 帳戶** 頁面。

2. 選取您在本快速入門開始時所建立的 Azure Purview 帳戶。 選取 [刪除]，輸入帳戶名稱，再選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Purview 帳戶。

前往下一篇文章，了解如何允許使用者存取您的 Azure Purview 帳戶。 

> [!div class="nextstepaction"]
> [將使用者新增至您的 Azure Purview 帳戶](catalog-permissions.md)
