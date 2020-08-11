---
title: 使用 PowerShell 建立共用的自我裝載整合執行階段
description: 了解如何在 Azure Data Factory 中建立共用的自我裝載整合執行階段，讓多個資料處理站都能存取該整合執行階段。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 03e906d448790be8c7494e8172d02b3fc0603673
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067948"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立共用的自我裝載整合執行階段

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本指南說明如何在 Azure Data Factory 中建立共用的自我裝載整合執行階段。 然後您可以在其他資料處理站中，使用該共用的自我裝載整合執行階段。

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>使用 Azure Data Factory UI 建立共用的自我裝載 IR

若要使用 Azure Data Factory UI 建立共用的自我裝載 IR，您可採取下列步驟：

1. 在要共用的自我裝載 IR 中，選取 [**授與許可權給另一個 Data factory** ]，然後在 [整合執行時間設定] 頁面中，選取您要在其中建立連結 IR 的 Data factory。
      
    ![在 [共用] 索引標籤上的權限授與按鈕](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. 請注意，並複製自我裝載 IR 的上述「資源識別碼」以供共用。
         
3. 在已授與權限的資料處理站中，建立新的自我裝載 IR (已連結) 並輸入資源識別碼。
      
    ![用來建立自我裝載整合執行時間的按鈕](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![建立已連結自我裝載整合執行階段的按鈕](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![名稱和資源識別碼方塊](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>使用 Azure PowerShell 建立共用的自我裝載 IR

若要使用 Azure PowerShell 建立共用的自我裝載 IR，您可採取下列步驟： 
1. 建立資料處理站。 
1. 建立自我裝載整合執行階段。
1. 與其他資料處理站共用該自我裝載整合執行階段。
1. 建立連結的整合執行階段。
1. 撤銷共用。

### <a name="prerequisites"></a>必要條件 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。 

- **Azure PowerShell**(英文)。 請依照[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 中的指示操作。 您會使用 PowerShell 來執行指令碼，以建立可與其他資料處理站共用的自我裝載整合執行階段。 

> [!NOTE]  
> 如需目前可取得 Data Factory 的 Azure 區域，請在 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory) 上選取您感興趣的區域。

### <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 Windows PowerShell 整合式指令碼環境 (ISE)。

1. 建立變數。 複製並貼上下列指令碼。 請將變數 (例如 **SubscriptionName** 和 **ResourceGroupName**) 取代為實際值： 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. 登入並選取訂用帳戶。 將下列程式碼新增至指令碼，以登入並選取 Azure 訂用帳戶：

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. 建立資源群組和資料處理站。

    > [!NOTE]  
    > 此為選用步驟。 如果您已經有資料處理站，請略過此步驟。 

    使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令來建立 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。 下列範例會在 WestEurope 位置建立名為 `myResourceGroup` 的資源群組： 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    執行下列命令來建立資料處理站： 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>建立自我裝載整合執行階段

> [!NOTE]  
> 此為選用步驟。 如果您已經有要與其他資料處理站共用的自我裝載整合執行階段，請略過此步驟。

執行下列命令來建立自我裝載整合執行階段：

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>取得整合執行階段驗證金鑰並註冊節點

執行下列命令，以取得自我裝載整合執行階段的驗證金鑰：

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

回應會包含此自我裝載整合執行階段的驗證金鑰。 當您註冊整合執行階段節點時，會使用此金鑰。

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>安裝和註冊自我裝載整合執行階段

1. 從 [Azure Data Factory Integration Runtime](https://aka.ms/dmg) 下載自我裝載整合執行階段安裝程式。

2. 執行安裝程式，以在本機電腦上安裝自我裝載整合。

3. 使用您在上一步中擷取的驗證金鑰，註冊新的自我裝載整合。

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>與另一個資料處理站共用該自我裝載整合執行階段

#### <a name="create-another-data-factory"></a>建立另一個資料處理站

> [!NOTE]  
> 此為選用步驟。 如果您已有要共用的資料處理站，請略過此步驟。 但在或中，若要新增或移除其他資料處理站的角色指派，您必須具有 `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 許可權，例如 [[使用者存取系統管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)] 或 [[擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)者]。

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>授與權限

將存取您建立並註冊的自我裝載整合執行階段所需的權限授與資料處理站。

> [!IMPORTANT]  
> 請勿略過此步驟！

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>建立連結的自我裝載的整合執行階段

執行下列命令來建立連結的自我裝載整合執行階段：

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

現在，您可以在任何已連結的服務中，使用此連結的整合執行階段。 連結的整合執行階段是使用共用的整合執行階段來執行活動。

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>將整合執行階段共用從資料處理站撤銷

若要撤銷資料處理站對共用整合執行階段的存取權，請執行下列命令：

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

若要移除現有連結的整合執行階段，請對共用整合執行階段執行下列命令：

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>後續步驟

- 檢閱 [Azure Data Factory 中的整合執行階段概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)。

- 了解如何[在 Azure 入口網站中建立自我裝載的整合執行階段](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。
