---
title: 快速入門`:` 使用受控識別來存取 Azure Resource Manager - Azure AD
description: 本快速入門會逐步引導您使用 Linux VM 系統指派的受控識別，以存取 Azure Resource Manager。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653159c2e40d3375a422f0da14274f57130de1fe
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359675"
---
# <a name="use-a-linux-vm-system-assigned-managed-identity-to-access-azure-resource-manager"></a>使用 Linux VM 系統指派的受控識別來存取 Azure Resource Manager

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本快速入門說明如何將系統指派的身分識別用於 Linux 虛擬機器 (VM)，以存取 Azure Resource Manager API。 適用於 Azure 資源的受控識別會自動由 Azure 管理，可讓您向支援 Azure AD 驗證的服務進行驗證，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Azure Resource Manager 中將您的 VM 存取權授與資源群組 
> * 使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager 

## <a name="prerequisites"></a>Prerequisites

- 了解受控識別。 如果您不熟悉適用於 Azure 資源的受控識別功能，請參閱此[概觀](overview.md)。 
- Azure 帳戶，[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 您也需要已啟用系統指派受控識別的 Linux 虛擬機器。
  - 如果您需要為本教學課程建立虛擬機器，您可以遵循標題為[使用 Azure 入口網站建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)的文章

## <a name="grant-access"></a>授與存取權

您的程式碼可以使用適用於 Azure 資源的受控識別來取得存取權杖，以向支援 Azure AD 驗證的資源進行驗證。 Azure Resource Manager API 支援 Azure AD 驗證。 首先，我們需要在 Azure Resource Manager 中將這個 VM 的身分識別存取權授與資源，此情況下就是包含 VM 的資源群組。  

1. 瀏覽至 **資源群組** 的索引標籤。
2. 選取您用於虛擬機器的特定 **資源群組**。
3. 前往左側面板的 [存取控制 (IAM)]  。
4. 按一下以 [新增]  VM 的新角色指派。 選擇 [角色]  為 [讀取者]  。
5. 在下一個下拉式清單中，將 **存取權指派給** 資源 [虛擬機器]  。
6. 接下來，確認適當的訂用帳戶已列在 [訂用帳戶]  下拉式清單中。 針對 [資源群組]  ，請選取 [所有資源群組]  。
7. 最後，在 [選取]  中選擇下拉式清單中您的 Linux 虛擬機器，然後按一下 [儲存]  。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-resource-manager"></a>使用 VM 系統指派的受控識別取得存取權杖，以用來呼叫 Resource Manager

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](/windows/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](../../virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](../../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在入口網站中，瀏覽至 [Linux VM]，並在 [概觀]  中按一下 [連線]  。  
2. 使用您所選擇的 SSH 用戶端來 **連線** 到 VM。 
3. 在終端機視窗中，使用 `curl` 向 Azure 資源端點的本機受控識別提出要求，以取得 Azure Resource Manager 的存取權杖。  
 
    存取權杖的 `curl` 要求如下。  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -H Metadata:true   
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。    當使用 Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。 
    
    此回應包含您存取 Azure Resource Manager 所需的存取權杖。 
    
    回應：  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    您可以使用此存取權杖來存取 Azure Resource Manager，例如讀取您先前授與此 VM 存取的資源群組詳細資料。 使用您稍早建立的值來取代 \<SUBSCRIPTION ID\>、\<RESOURCE GROUP\>和 \<ACCESS TOKEN\>。 
    
    > [!NOTE]
    > URL 區分大小寫，因此請確定您使用的是稍早在命名資源群組時所使用的相同大小寫，而且 “resourceGroup” 中的 “G” 為大寫。  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    傳回的回應含有特定的資源群組資訊： 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用系統指派的受控識別，來存取 Azure Resource Manager API。  若要深入了解 Azure Resource Manager，請參閱：

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
>[使用 Azure PowerShell 來建立、列出及刪除使用者指派的受控識別](how-to-manage-ua-identity-powershell.md)