---
title: 使用 Azure 受控識別在 DevTest Labs 中建立環境 |Microsoft Docs
description: 瞭解如何使用 Azure 中的受控識別，在 Azure DevTest Labs 的實驗室中部署環境。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73931147"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 受控識別在實驗室中部署環境 
身為實驗室擁有者，您可以使用受控識別在實驗室中部署環境。 當環境包含或有 Azure 資源的參考（例如金鑰保存庫、共用映射資源庫，以及環境資源群組外部的網路）時，這項功能會很有説明。 它可讓您建立不限於該環境之資源群組的沙箱環境。

> [!NOTE]
> 目前，每個實驗室都支援單一使用者指派的身分識別。 

## <a name="prerequisites"></a>Prerequisites
- [使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

## <a name="use-azure-portal"></a>使用 Azure 入口網站
在本節中，您身為實驗室擁有者，請使用 Azure 入口網站將使用者管理的身分識別新增至實驗室。 

1. 在 [實驗室] 頁面上，選取 [設定**和原則**]。 
1. 在 [**設定**] 區段中選取 [身分**識別**]。
1. 若要新增使用者指派的身分識別，請選取工具列上的 [**新增**]。 
1. 從預先填入的下拉式清單中選取身分**識別**。
1. 選取 [確定]  。

    ![新增使用者管理的身分識別](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 您會在清單中看到新增的使用者管理的身分識別。 

    ![清單中的使用者管理的身分識別](./media/use-managed-identities-environments/identity-in-list.png)

儲存之後，實驗室將會在部署所有實驗室環境時使用此身分識別。 您也可以從清單中選取身分識別，以存取 Azure 中的身分識別資源。 

部署環境時，實驗室擁有者不需要執行任何特殊動作，只要新增至實驗室的身分識別具有環境需要存取之外部資源的許可權即可。 

若要變更指派給實驗室的使用者受控識別，請先移除附加至實驗室的身分識別，然後再將另一個身分新增至實驗室。 若要移除附加至實驗室的身分識別，請選取 [ **...]（省略號）**，然後按一下 [**移除**]。 

![清單中的使用者管理的身分識別](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>使用 API

1. 建立身分識別之後，請記下此身分識別的資源識別碼。 看起來應該如下列範例所示： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 執行 PUT Https 方法，將新`ServiceRunner`資源新增至實驗室，如下列範例所示。 服務執行器資源是用來管理和控制 DevTest Labs 中受控識別的 proxy 資源。 服務執行器名稱可以是任何有效的名稱，但建議您使用受控識別資源的名稱。 
 
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
 
一旦將使用者指派的身分識別新增至實驗室之後，Azure DevTest Labs 服務就會在部署 Azure Resource Manager 環境時使用它。 例如，如果您需要 Resource Manager 範本來存取外部共用映射庫映射，請確定您新增至實驗室的身分識別具有共用映射庫資源的最低必要許可權。 
