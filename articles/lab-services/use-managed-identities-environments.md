---
title: 使用 Azure 託管標識在開發人員測試實驗室中創建環境 |微軟文檔
description: 瞭解如何在 Azure 開發人員測試實驗室中使用 Azure 中的託管標識在實驗室中部署環境。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931147"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 託管標識在實驗室中部署環境 
作為實驗室擁有者，可以使用託管標識在實驗室中部署環境。 此功能在環境包含或具有對 Azure 資源的引用（如金鑰保存庫、共用映射庫和環境資源組外部的網路）的方案中非常有用。 它支援創建不局限于該環境的資源組的沙箱環境。

> [!NOTE]
> 目前，每個實驗室都支援單個使用者分配的標識。 

## <a name="prerequisites"></a>Prerequisites
- [使用 Azure 門戶創建、列出、刪除或將角色指派給使用者分配的託管標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
在本節中，作為實驗室擁有者，您可以使用 Azure 門戶向實驗室添加使用者託管標識。 

1. 在實驗室頁面上，選擇 **"配置"和"策略**"。 
1. 在 **"設置"** 部分中選擇 **"標識**"。
1. 要添加使用者分配的標識，請在工具列上選擇 **"添加**"。 
1. 從預填充的下拉清單中選擇**標識**。
1. 選取 [確定]****。

    ![添加使用者託管標識](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 在清單中可以看到添加的使用者託管標識。 

    ![清單中的使用者託管標識](./media/use-managed-identities-environments/identity-in-list.png)

保存後，實驗室將在部署所有實驗室環境時使用此標識。 還可以通過從清單中選擇標識來訪問 Azure 中的標識資源。 

只要添加到實驗室的標識具有環境需要訪問的外部資源的許可權，實驗室擁有者在部署環境時不需要執行任何特殊操作。 

要更改分配給實驗室的使用者託管標識，請先刪除附加到實驗室的標識，然後向實驗室添加另一個標識。 要刪除附加到實驗室的標識，請選擇 **...（省略號），** 然後按一下 **"刪除**"。 

![清單中的使用者託管標識](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>使用 API

1. 創建標識後，請注意此標識的資源識別碼。 它應類似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 執行 PUT Https 方法向實驗室`ServiceRunner`添加新資源，類似于以下示例。 服務運行者資源是管理和控制 DevTest 實驗室中的託管標識的代理資源。 服務運行者名稱可以是任何有效名稱，但我們建議您使用託管標識資源的名稱。 
 
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
 
    以下是範例： 

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
 
將使用者分配的標識添加到實驗室後，Azure DevTest Labs 服務將在部署 Azure 資源管理器環境時使用它。 例如，如果需要資源管理器範本來訪問外部共用圖像庫圖像，請確保添加到實驗室的標識具有共用映射庫資源所需的最低許可權。 
