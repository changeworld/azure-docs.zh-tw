---
title: 使用 REST 在 Azure VM 上設定受控識別 - Azure AD
description: 逐步說明如何 CURL 進行 REST API 呼叫，在 Azure VM 上設定系統和使用者指派的受控識別。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587514"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>使用 REST API 呼叫在 Azure VM 上設定 Azure 資源受控識別

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控系統識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，使用 CURL 來呼叫 Azure Resource Manager REST 端點，即可了解如何在 Azure VM 上執行下列 Azure 資源受控識別作業：

- 在 Azure VM 上啟用和停用系統指派受控識別
- 在 Azure VM 上新增和移除使用者指派受控識別

如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="prerequisites"></a>Prerequisites

- 如果不熟悉 Azure 資源的受控識別，請參閱 [什麼是 Azure 資源受控識別？](overview.md)。 若要了解系統指派和使用者指派的受控識別類型，請參閱[受控識別類型](overview.md#managed-identity-types)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在 Azure VM 上啟用和停用系統指派的受控識別。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時，啟用系統指派的受控識別

若要建立已啟用系統指派受控識別的 Azure VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

1. 使用 [az group create](/cli/azure/group/#az-group-create)，為您的 VM 和其相關資源建立[資源群組](../../azure-resource-manager/management/overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 建立虛擬機器的[網路介面](/cli/azure/network/nic#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用 Azure Cloud Shell，並使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器。 下列範例會建立具有系統指派的受控識別 (如同在要求本文中由 `"identity":{"type":"SystemAssigned"}` 值所識別) 且名為 myVM 的虛擬機器。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **要求標頭**
   
   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 
   
   **要求本文**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在現有 Azure VM 上啟用系統指派的身分識別

若要在原先佈建的 VM 上啟用系統指派的受控識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，以便在您的虛擬機器上啟用系統指派的受控識別 (如同在要求本文中由 myVMSS 虛擬機器的 `{"identity":{"type":"SystemAssigned"}` 值所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
   
   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的受控識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如有任何使用者指派的受控識別是指派給回應中 `identity` 值識別的虛擬機器，請跳至步驟 3，此步驟將說明如何保留使用者指派的受控識別，同時在虛擬機器上啟用系統指派的受控識別。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 
   
   **要求本文**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. 若要在有現有使用者指派受控識別的虛擬機器上啟用系統指派的受控識別，您需要將 `SystemAssigned` 新增至 `type` 值。  
   
   舉例而言，若您的虛擬機器有使用者指派的受控識別 `ID1` 和 `ID2`，而且您想要將系統指派的受控識別新增至虛擬機器，請使用下列 CURL 呼叫。 以適用於您的環境的值取代 `<ACCESS TOKEN>` 和 `<SUBSCRIPTION ID>`。

   API 版本 `2018-06-01` 會將使用者指派的受控身分識別儲存在 `userAssignedIdentities` 值內 (採用字典格式)，而不是儲存在 `identityIds` 值內 (採用 API 版本 `2017-12-01` 中所使用的陣列格式)。
   
   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>停用來自 Azure VM 的系統指派受控識別

若要在 VM 上停用系統指派的受控識別，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。  不需要其他 Azure AD 目錄角色指派。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用 CURL 呼叫 Azure Resource Manager REST 端點來停用系統指派的受控識別，以便更新虛擬機器。  下列範例會停用系統指派受控識別 (如同在要求本文中由 myVM 虛擬機器中的 `{"identity":{"type":"None"}}` 值所識別)。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   > [!IMPORTANT]
   > 若要確保您不會刪除虛擬機器指派的任何現有使用者指派識別，需要使用下列 CURL 命令來列出使用者指派的受控識別：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如有任何使用者指派的受控識別是指派給回應中 `identity` 值識別的虛擬機器，請跳至步驟 3，此步驟將說明如何保留使用者指派的受控識別，同時在虛擬機器上停用系統指派的受控識別。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   若要從具有使用者指派受控識別的虛擬機器移除系統指派的受控識別，請從 `{"identity":{"type:" "}}` 值移除 `SystemAssigned`，但同時保留 `UserAssigned` 值和 `userAssignedIdentities` 字典值 (如果您使用 **API 版本 2018-06-01**)。 如果您使用 **API 版本 2017-12-01** 或先前版本，則請保留 `identityIds` 陣列。

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

在本節中，您將了解如何使用 CURL 呼叫 Azure Resource Manager REST 端點，在 Azure VM 上新增和移除使用者指派的受控識別。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>在建立 Azure VM 時，指派使用者指派的受控識別

若要將使用者指派的身分識別指派給 VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色指派。 不需要其他 Azure AD 目錄角色指派。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 建立虛擬機器的[網路介面](/cli/azure/network/nic#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 使用以下找到的指示建立使用者指派的受控識別：[建立使用者指派的受控識別](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

5. 使用 CURL 呼叫 Azure Resource Manager REST 端點來建立虛擬機器。 下列範例會使用使用者指派的受控識別 `ID1` (如同在要求本文中由 `"identity":{"type":"UserAssigned"}` 值所識別)，在資源群組 myResourceGroup 中建立名為 myVM 的虛擬機器。 將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。
 
   **API 版本 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API 版本 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>將使用者指派的受控識別指派至現有 Azure VM

若要將使用者指派的身分識別指派給 VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[受控識別操作者](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色指派。 不需要其他 Azure AD 目錄角色指派。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  使用以下找到的指示建立使用者指派的受控識別：[建立使用者指派的受控識別](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

3. 若要確保您不會刪除指派給虛擬機器的任何現有使用者或系統指派識別，需要使用下列 CURL 命令來列出指派給虛擬機器的識別類型。 若有指派給虛擬機器擴展集的受控識別，則會列在 `identity` 值之下。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。

    如有任何使用者或系統指派的受控識別是指派給回應中 `identity` 值識別的虛擬機器，請跳至步驟 5，此步驟將說明如何在 VM 上新增使用者指派的受控識別時，同時保留系統指派的受控識別。

4. 若沒有任何使用者指派的受控識別指派給您的虛擬機器，請使用下列 CURL 命令呼叫 Azure Resource Manager REST 端點，將第一個使用者指派的受控識別指派給虛擬機器。

   下列範例會將使用者指派的受控識別 `ID1` 指派給資源群組myResourceGroup  中名為 myVM 的虛擬機器。  將上一個步驟中要求持有人存取權杖時所收到的值用以取代 `<ACCESS TOKEN>`值，`<SUBSCRIPTION ID>` 的值則為適用於您的環境的值。

   **API 版本 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        |
 
   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API 版本 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

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

5. 如果您將現有的使用者指派或系統指派受控識別指派給 VM：
   
   **API 版本 2018-06-01**

   將使用者指派的受控識別新增至 `userAssignedIdentities` 字典值。
    
   例如，如果您目前將系統指派的受控識別和使用者指派的受控識別 `ID1` 指派給 VM，而且想要在其中新增使用者指派的受控識別 `ID2`：

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

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

   例如，如果您目前將系統指派的受控識別和使用者指派的受控識別 `ID1` 指派給 VM，而且想要在其中新增使用者指派的受控識別 `ID2`： 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>從 Azure VM 移除使用者指派的受控識別

若要將使用者指派的身分識別移除至 VM，您的帳戶需要[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色指派。

1. 擷取持有人存取權杖，在下一個步驟中，會使用此權杖搭配搭配系統指派的受控識別，在授權標頭中建立虛擬機器。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 若要確保您不會刪除您要維持指派給虛擬機器的任何現有使用者指派受控識別，或者要移除系統指派的受控識別，需要使用下列 CURL 命令來列出受控識別： 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。
 
   若您有指派給虛擬機器的受控識別，則會在 `identity` 值的回應中列出。

   例如，如果您將使用者指派的受控識別 `ID1` 和 `ID2` 指派給 VM，而且只想要繼續指派 `ID1`，並保留系統指派的身分識別：
   
   **API 版本 2018-06-01**

   將 `null` 新增至您想要移除的使用者指派受控識別：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **要求標頭**

   |要求標頭  |描述  |
   |---------|---------|
   |*Content-Type*     | 必要。 設定為 `application/json`。        |
   |*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。        | 

   **要求本文**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

如果您的虛擬機器同時具有系統指派和使用者指派的受控識別，您可藉由切換為僅使用系統指派的受控識別，移除所有使用者指派的受控識別：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**要求標頭**

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。 | 

**要求本文**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
如果您的虛擬機器只有使用者指派的受控識別，而且您想要全部移除，請使用下列命令：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**要求標頭**

|要求標頭  |描述  |
|---------|---------|
|*Content-Type*     | 必要。 設定為 `application/json`。        |
|*授權*     | 必要。 設定為有效的 `Bearer` 存取權杖。| 

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
