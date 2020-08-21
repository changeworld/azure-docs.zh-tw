---
title: 在 Azure DevTest Labs 的實驗室 Vm 上啟用受控識別
description: 本文會說明實驗室擁有者如何在您的實驗室虛擬機器上啟用使用者指派的受控識別。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717622"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中的實驗室虛擬機器上啟用使用者指派的受控識別
作為實驗室擁有者，您可以在您的實驗室虛擬機器上啟用使用者指派的受控識別， (Vm) Azure DevTest Labs。

受控身分識別可用來向任何支援 Azure Active Directory (AD) 驗證（包括 Key Vault）的服務進行驗證，而不需要在程式碼中傳遞任何認證。 如需受控識別的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。

有了這項功能，實驗室使用者就可以在實驗室環境中共用 Azure 資源（例如 Azure SQL Database）。 對資源的驗證是由身分識別本身負責。 一旦設定之後，將會使用此身分識別來啟用每個現有/新建立的實驗室 VM。 實驗室使用者只要登入他們的電腦，就可以存取資源。

> [!NOTE]
> 您可以新增多個使用者指派的受控識別，以在實驗室 Vm 上啟用。

## <a name="use-azure-portal"></a>使用 Azure 入口網站
若要為實驗室 Vm 新增使用者指派的受控識別，請遵循下列步驟：

1. [在您的訂用帳戶中建立使用者指派的受控識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 流覽至實驗室的 [設定 **與原則** ] 頁面。
1. 在左側功能表上選取 [身分 **識別 (預覽]) ** 。
1. 選取 [ **虛擬機器** ] 索引標籤。
1. 選取 [ **加入** ]，從預先填入的下拉式清單中選取現有的身分識別。 

    > [!div class="mx-imgBorder"]
    > ![[新增身分識別] 按鈕](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. 從下拉式清單中選取現有的 **使用者管理** 的身分識別，然後選取 **[確定]**。 

    > [!div class="mx-imgBorder"]
    > ![新增身分識別](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>使用 API

1.  建立身分識別之後，請記下身分識別的資源識別碼。 它看起來應該如下列範例所示： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. 執行 PUT HTTPS 方法，以將新的 **ServiceRunner** 資源新增至實驗室，如下列範例所示。 

    服務執行器資源是用來管理和控制 DevTest Labs 中受控識別的 proxy 資源。 服務執行器名稱可以是任何有效的名稱，但建議您使用受控識別資源的名稱。

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
若要深入瞭解受控識別，請參閱 [什麼是適用于 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md)。







