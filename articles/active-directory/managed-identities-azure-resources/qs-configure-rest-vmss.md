---
title: 使用 REST Azure AD 在 Azure 虛擬機器擴展集上設定受控識別
description: 使用捲曲在 Azure 虛擬機器擴展集上設定系統和使用者指派的受控識別，以進行 REST API 呼叫的逐步指示。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06bce15dfbd2ccd3ac97f6a4f1e4efb5a24db85d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609091"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>使用 REST API 呼叫在虛擬機器擴展集上設定 Azure 資源受控識別

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控系統識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，使用 CURL 來呼叫 Azure Resource Manager REST 端點，即可了解如何在虛擬機器擴展集上執行下列 Azure 資源受控識別作業：

- 在 Azure 虛擬機器擴展集上啟用和停用系統指派的受控識別
- 在 Azure 虛擬機器擴展集上新增和移除使用者指派的受控識別

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列 Azure 角色型存取控制指派：

    > [!NOTE]
    > 不需要其他 Azure AD 目錄角色指派。

    - [虛擬機器參與者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可建立虛擬機器擴展集，並從虛擬機器擴展集啟用和移除系統和/或使用者指派的受控識別。
    - [受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可建立使用者指派的受控識別。
    - [受控識別操作員](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可為虛擬機器擴展集指派和移除使用者指派的識別。
- 如果您使用的是 Windows，請安裝[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)，或使用 Azure 入口網站中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果您使用的是[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)或 [Linux 散發作業系統](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，請[安裝 Azure CLI 本機主控台](/cli/azure/install-azure-cli)。
- 如果您使用的是 Azure CLI 本機主控台，請登入 Azure ，登入時請使用與您想要用於管理系統或使用者指派受控識別的 Azure 訂用帳戶相關聯的帳戶，搭配使用 `az login` 登入。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在虛擬機器擴展集上啟用和停用系統指派的受控識別。

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>在虛擬機器擴展集建立期間啟用系統指派的受控識別

若要啟用系統指派的受控識別來建立虛擬機器擴展集，您需要建立虛擬機器擴展集，並擷取存取權杖，才能使用 CURL 以系統指派的受控識別類型值呼叫 Resource Manager 端點。

1. 使用 [az group create](/cli/azure/group/#az-group-create)，為您的虛擬機器擴展集和其相關資源建立[資源群組](../../azure-resource-manager/management/overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 為虛擬機器擴展集建立[網路介面](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器擴展集。 下列範例會使用系統指派的受控識別 (如同在要求本文中由 `"identity":{"type":"SystemAssigned"}` 值所識別)，在 *myResourceGroup* 中建立名為 *myVMSS* 的虛擬機器擴展集。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>在現有虛擬機器擴展集上啟用系統指派的受控識別

若要在現有的虛擬機器擴展集上啟用系統指派的受控識別，您需要取得存取權杖，然後使用 CURL 呼叫 Resource Manager REST 端點來更新識別類型。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，以便在您的虛擬機器擴展集上啟用系統指派的受控識別 (如同在要求本文中由 myVMSS** 虛擬機器擴展集的 `{"identity":{"type":"SystemAssigned"}` 值所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
   
   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器擴展集指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的受控識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如有任何使用者指派的受控識別是指派給回應中 `identity` 值識別的虛擬機器擴展集，請跳至步驟 3，此步驟將說明如何保留使用者指派的受控識別，同時在虛擬機器擴展集上啟用系統指派的受控識別。

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. 若要在有現有使用者指派受控識別的虛擬機器擴展集上啟用系統指派的受控識別，您需要將 `SystemAssigned` 新增至 `type` 值。  
   
   舉例而言，若您的虛擬機器擴展集有使用者指派的受控識別 `ID1` 和 `ID2`，而且您想要將系統指派的受控識別新增至虛擬機器擴展集，請使用下列 CURL 呼叫。 以適用於您的環境的值取代 `<ACCESS TOKEN>` 和 `<SUBSCRIPTION ID>`。

   API 版本 `2018-06-01` 會將使用者指派的受控身分識別儲存在 `userAssignedIdentities` 值內 (採用字典格式)，而不是儲存在 `identityIds` 值內 (採用 API 版本 `2017-12-01` 中所使用的陣列格式)。
   
   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 |
 
   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>從虛擬機器擴展集停用系統指派的受控識別

若要在現有的虛擬機器擴展集上停用系統指派的識別，您需要取得存取權杖，然後使用 CURL 呼叫 Resource Manager REST 端點，將識別類型更新為 `None`。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用 CURL 呼叫 Azure Resource Manager REST 端點來停用系統指派的受控識別，以便更新虛擬機器擴展集。  下列範例會停用系統指派的受控識別 (如同在要求本文中由 myVMSS** 虛擬機器擴展集中的 `{"identity":{"type":"None"}}` 值所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器擴展集指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的受控識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如有任何使用者指派的受控識別是指派給虛擬機器擴展集，請跳至步驟 3，此步驟將說明如何保留使用者指派的受控識別，同時從虛擬機器擴展集移除系統指派的受控識別。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   若要從具有使用者指派受控識別的虛擬機器擴展集移除系統指派的受控識別，請從 `{"identity":{"type:" "}}` 值移除 `SystemAssigned`，但同時保留 `UserAssigned` 值和 `userAssignedIdentities` 字典值 (如果您使用 **API 版本 2018-06-01**)。 如果您使用 **API 版本 2017-12-01** 或先前版本，則請保留 `identityIds` 陣列。

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在虛擬機器擴展集上新增和移除使用者指派的受控識別。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>在虛擬機器擴展集建立期間指派使用者指派的受控識別

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 為虛擬機器擴展集建立[網路介面](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用以下找到的指示建立使用者指派的受控識別：[建立使用者指派的受控](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)識別。

5. 使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器擴展集。 下列範例會使用使用者指派的受控識別 `ID1` (如同在要求本文中由 `"identity":{"type":"UserAssigned"}` 值所識別)，在資源群組 myResourceGroup** 中建立名為 myVMSS** 的虛擬機器擴展集。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
 
   **API 版本 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API 版本 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 |
 
   **要求本文**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>將使用者指派的受控識別指派給現有的 Azure 虛擬機器擴展集

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  使用以下找到的指示建立使用者指派的受控識別：[建立使用者指派的受控識別](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

3. 若要確保您不會刪除指派給虛擬機器擴展集的任何現有使用者或系統指派識別，需要使用下列 CURL 命令來列出指派給虛擬機器擴展集的識別類型。 若有指派給虛擬機器擴展集的受控識別，則會列在 `identity` 值內。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。 |   
 

4. 如果您沒有任何使用者或系統指派的受控識別指派給您的虛擬機器擴展集，請使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，將第一個使用者指派的受控識別指派給虛擬機器擴展集。  如有使用者或系統指派的受控識別是指派給虛擬機器擴展集，請跳至步驟 5，此步驟將說明如何將多個使用者指派的受控識別新增到虛擬機器擴展集，同時維持系統指派的受控識別。

   下列範例會將使用者指派的受控識別 `ID1` 指派給資源群組 *myResourceGroup* 中名為 *myVMSS* 的虛擬機器擴展集。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. 如果您將現有的使用者指派或系統指派受控識別指派給虛擬機器擴展集：
   
   **API 版本 2018-06-01**

   將使用者指派的受控識別新增至 `userAssignedIdentities` 字典值。

   例如，如果您目前將系統指派的受控識別和使用者指派的受控識別 `ID1` 指派給虛擬機器擴展集，而且想要在其中新增使用者指派的受控識別 `ID2`：

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   保留您想要留在 `identityIds` 陣列值內的使用者指派受控識別，同時新增使用者指派的受控識別。

   例如，如果您目前將系統指派的受控識別和使用者指派的受控識別 `ID1` 指派給虛擬機器擴展集，而且想要在其中新增使用者指派的受控識別 `ID2`：

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>從虛擬機器擴展集移除使用者指派的受控識別

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器擴展集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 若要確保您不會刪除您要維持指派給虛擬機器擴展集的任何現有使用者指派受控識別，或者要移除系統指派的受控識別，需要使用下列 CURL 命令來列出受控識別：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。 |
   
   若您有指派給虛擬機器的受控識別，則會在 `identity` 值的回應中列出。 
    
   例如，如果您將使用者指派的受控識別 `ID1` 和 `ID2` 指派給虛擬機器擴展集，而且只想要繼續指派 `ID1`，並保留系統指派的受控識別：

   **API 版本 2018-06-01**

   將 `null` 新增至您想要移除的使用者指派受控識別：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   只保留您想要留在 `identityIds` 陣列中的使用者指派受控識別：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

若您的虛擬機器擴展集同時具有系統指派和使用者指派的受控識別，只要使用下列命令切換為僅使用系統指派的受控識別，即可移除所有使用者指派的受控識別：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**要求標頭**

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

**要求本文**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
若您的虛擬機器擴展集只有使用者指派的受控識別，而且您想要全部移除，請使用下列命令：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**要求標頭**

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*驗證*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

**要求本文**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>後續步驟

如需如何使用 REST 建立、列出或刪除使用者指派的受控識別相關資訊，請參閱：

- [使用 REST API 呼叫建立、列出和刪除使用者指派的受控識別](how-to-manage-ua-identity-rest.md)
