---
title: 在 Azure 開發人員測試實驗室中的實驗室 VM 上啟用託管標識
description: 本文介紹實驗室擁有者如何在實驗室虛擬機器上啟用使用者分配的託管標識。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692663"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 開發人員測試實驗室中的實驗室虛擬機器上啟用使用者分配的託管標識
作為實驗室擁有者，您可以在 Azure DevTest Labs 中的實驗室虛擬機器 （VM） 上啟用使用者分配的託管標識。

託管標識可用於對支援 Azure 活動目錄 （AD） 身份驗證（包括金鑰保存庫）的任何服務進行身份驗證，而無需傳遞代碼中的任何憑據。 有關託管標識的詳細資訊，請參閱 Azure[資源的託管標識是什麼？](../active-directory/managed-identities-azure-resources/overview.md)

使用此功能，實驗室使用者可以在實驗室的上下文中共用 Azure 資源，如 Azure SQL 資料庫。 對資源的身份驗證由標識本身處理。 配置後，將使用此標識啟用每個現有/新創建的實驗室 VM。 實驗室使用者可以在登錄到其電腦後訪問資源。

> [!NOTE]
> 您可以添加多個分配給使用者分配的託管標識，以在實驗室 VM 上啟用。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
要為實驗室 VM 添加分配給的託管標識的使用者，請按照以下步驟操作：

1. [在訂閱中創建使用者分配的託管標識](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 導航到實驗室的 **"配置和策略"** 頁面。
2. 選擇左側功能表上的**標識（預覽）。**
3. 選擇 **"虛擬機器**"選項卡。
4. 選擇 **"添加"** 可從預填充的下拉清單中選擇現有標識。 

    ![添加標識按鈕](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. 從下落清單中選擇現有**使用者託管標識**，然後選擇 **"確定**"。 

    ![添加標識](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>使用 API

1.  創建標識後，請注意標識的資源識別碼。 它應類似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. 運行 PUT HTTPS 方法以向實驗室添加新**的 ServiceRunner**資源，如以下示例所示。 

    服務運行者資源是管理和控制 DevTest 實驗室中的託管標識的代理資源。 服務運行者名稱可以是任何有效名稱，但我們建議您使用託管標識資源的名稱。

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>後續步驟
要瞭解有關託管標識的更多內容，請參閱[Azure 資源的託管標識是什麼？](../active-directory/managed-identities-azure-resources/overview.md)







