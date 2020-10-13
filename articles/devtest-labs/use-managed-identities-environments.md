---
title: 使用 Azure 受控識別在 DevTest Labs 中建立環境 |Microsoft Docs
description: 瞭解如何在 Azure 中使用受控識別來部署實驗室中的環境 Azure DevTest Labs。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718966"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 受控識別來部署實驗室中的環境 

作為實驗室擁有者，您可以使用受控識別來部署實驗室中的環境。 當環境包含或參考 Azure 資源（例如金鑰保存庫、共用映射資源庫，以及環境資源群組的外部網路）的情況時，這項功能會很有説明。 它可讓您建立不限於該環境之資源群組的沙箱環境。

> [!NOTE]
> 目前，每個實驗室都支援單一使用者指派的身分識別。 

## <a name="prerequisites"></a>Prerequisites

- [使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 
    
    請確定您的受控識別是在與您實驗室相同的區域和訂用帳戶中建立的。 受控識別不需要位於相同的資源群組中。

## <a name="use-azure-portal"></a>使用 Azure 入口網站

在本節中，您會以實驗室擁有者身分，使用 Azure 入口網站將使用者管理的身分識別新增至實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 **DevTest Labs**。
1. 從實驗室清單中，選取您要的實驗室。
1. 選取**Configuration 和原則**身分  ->  **識別 (預覽) **。 
1. 若要新增使用者指派的身分識別，請選取 [ **使用者指派** ] 索引標籤。
1. 按下 [ **新增** ]。
1. 從下拉式清單中選取您已建立及/或具有存取權的現有使用者。
 
    ![新增使用者管理的身分識別](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. 在頁面頂端按下 [ **儲存** ]。

    儲存之後，實驗室會在部署所有實驗室環境時使用此身分識別。 您也可以從清單中選取身分識別，以存取 Azure 中的身分識別資源。 

只要新增至實驗室的身分識別具有環境需要存取的外部資源許可權，實驗室擁有者就不需要在部署環境時進行任何特殊動作。 

若要變更指派給實驗室的使用者受控識別，請先移除附加至實驗室的身分識別，然後將另一個身分識別新增至實驗室。 若要移除附加至實驗室的身分識別，請選取 [ **...] (省略號) **，然後按一下 [ **移除**]。 

## <a name="use-api"></a>使用 API

1. 建立身分識別之後，請記下此身分識別的資源識別碼。 它看起來應該如下列範例所示： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 執行 PUT Https 方法，以將新的 `ServiceRunner` 資源新增至實驗室，類似于下列範例。 服務執行器資源是用來管理和控制 DevTest Labs 中受控識別的 proxy 資源。 服務執行器名稱可以是任何有效的名稱，但建議您使用受控識別資源的名稱。 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    以下為範例： 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
一旦將使用者指派的身分識別新增至實驗室，Azure DevTest Labs 服務就會在部署 Azure Resource Manager 環境時使用它。 例如，如果您需要 Resource Manager 範本來存取外部共用映射庫映射，請確定您新增至實驗室的身分識別具有共用映射庫資源的最低必要許可權。 
