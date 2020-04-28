---
title: 將 Azure DevTest Labs 整合到您的 Azure Pipelines
description: 了解如何將 Azure DevTest Labs 整合到 Azure Pipelines 持續整合和傳遞管線
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76293210"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>將 Azure DevTest Labs 整合到您的 Azure Pipelines CI/CD 管線

您可以使用*Azure DevTest Labs Tasks*延伸模組，將您的 Azure Pipelines 持續整合和持續傳遞（CI/CD）組建和發行管線與 Azure DevTest Labs 整合。 延伸模組會安裝數個工作，包括： 

- 建立虛擬機器 (VM)
- 從 VM 建立自訂映像
- 刪除 VM 

例如，這些工作可讓您輕鬆地為特定的測試工作部署*黃金映射*VM，然後在測試完成時刪除 vm。

本文說明如何使用 Azure DevTest Labs 工作來建立和部署 VM、建立自訂映射，然後刪除 VM，全都是一個發行管線。 您通常會在自己的自訂群組建、測試和部署管線中個別執行工作。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Prerequisites

- 註冊或登入您的[Azure DevOps](https://dev.azure.com)組織，並在組織中[建立專案](/vsts/organizations/projects/create-project)。 
  
- 從 Visual Studio Marketplace 安裝 Azure DevTest Labs Tasks 延伸模組。
  
  1. 移至 [Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
  1. 選取 [**免費取得**]。
  1. 從下拉式清單中選取您的 Azure DevOps 組織，然後選取 [**安裝**]。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>建立範本來建立 Azure VM 

本節說明如何建立您用來視需要建立 Azure VM 的 Azure Resource Manager 範本。

1. 若要在您的訂用帳戶中建立 Resource Manager 範本，請遵循[使用 Resource Manager 範本](devtest-lab-use-resource-manager-template.md)中的程式。
   
1. 產生 Resource Manager 範本之前，請先在建立 VM 的過程中新增 [WinRM 構件](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。 *在目的電腦上的*部署工作（例如*Azure 檔案複製*和 PowerShell）需要 WinRM 存取。 WinRM 成品需要主機名稱作為參數，這應該是 VM 的完整功能變數名稱（FQDN）。 
   
   > [!NOTE]
   > 當您透過共用 IP 位址使用 WinRM 時，您必須新增將外部連接埠對應至 WinRM 連接埠的 NAT 規則。 如果您使用公用 IP 位址建立 VM，則不需要 NAT 規則。
   
   
1. 以名為*createvmtemplate.json*的檔案，將範本儲存到您的電腦。
   
1. 將範本簽入您的原始檔控制系統。

## <a name="create-a-script-to-get-vm-properties"></a>建立腳本以取得 VM 屬性

當您在發行管線中的*目的電腦上*執行*Azure 檔案複製*或 PowerShell 之類的工作步驟時，下列腳本會收集將應用程式部署至 VM 所需的值。 您通常會使用這些工作，將您的應用程式部署至 Azure VM。 這些工作需要 VM 資源組名、IP 位址和 FQDN 之類的值。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要建立腳本檔案：

1. 開啟文字編輯器，並在其中貼上下列程式碼。
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 使用類似*GetLabVMParams*的名稱儲存檔案，並將其簽入您的原始檔控制系統。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>在 Azure Pipelines 中建立發行管線

若要建立新的發行管線：

1. 從您的 Azure DevOps 專案] 頁面，選取左側導覽中的 [**管線** > **發行**]。
1. 選取 [**新增管線**]。
1. 在 [**選取範本**] 底下，向下**並選取 [****空白作業**]，然後選取 [套用]。

### <a name="add-and-set-variables"></a>新增和設定變數

當您在 Azure 入口網站中建立 Resource Manager 範本時，管線工作會使用您指派給 VM 的值。 

若要新增值的變數： 

1. 在 [管線] 頁面上，選取 [**變數**] 索引標籤。
   
1. 針對每個變數，選取 [**新增**]，然後輸入名稱和值：
   
   |名稱|值|
   |---|---|
   |*vmName*|您在 Resource Manager 範本中指派的 VM 名稱|
   |*userName*|存取 VM 的使用者名稱|
   |*password*|使用者名稱的密碼。 選取 [鎖定] 圖示，以隱藏並保護密碼。

### <a name="create-a-devtest-labs-vm"></a>建立 DevTest Labs VM

下一個步驟是建立要用於未來部署的黃金映射 VM。 您可以使用*Azure DevTest Labs 建立 vm*工作，在 Azure DevTest Labs 實例中建立 vm。

1. 在 [發行管線**管線**] 索引標籤上，選取 [**階段 1** ] 中的超連結文字來**查看階段**工作**+** ，然後選取 [**代理程式作業**] 旁的加號。 
   
1. 在 [**新增**工作] 底下，選取 [ **Azure DevTest Labs 建立 VM**]，然後選取 [**新增**]。 
   
1. 在左窗格中選取 [**建立 AZURE DEVTEST LABS VM** ]。 

1. 在右窗格中，填寫表單，如下所示：
   
   |欄位|值|
   |---|---|
   |**Azure RM 訂用帳戶**|從 [**可用的 Azure 服務**連線] 或下拉式清單中的 [**可用的 azure 訂用**帳戶] 選取服務連線或訂閱，並視需要選取 [**授權**]。<br /><br />**注意：** 如需有關為您的 Azure 訂用帳戶建立更受限制的許可權連線的詳細資訊，請參閱[Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)。|
   |**實驗室名稱**|選取將在其中建立實驗室 VM 的現有實驗室名稱。|
   |**範本名稱**|輸入您儲存到原始程式碼存放庫之範本檔案的完整路徑和名稱。 您可以使用內建屬性來簡化路徑，例如：<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**範本參數**|輸入您稍早定義之變數的參數：<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**輸出變數** > **實驗室 VM 識別碼**|輸入所建立之實驗室 VM 識別碼的變數。 如果您使用預設**labVMId**，您可以在後續工作中將變數參考為 *$ （labVMId）*。<br /><br />您可以建立預設值以外的名稱，但請記得在後續工作中使用正確的名稱。 您可以用下列格式撰寫實驗室 VM 識別碼：<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>收集 DevTest Labs VM 的詳細資料

執行您先前建立的指令碼，以收集 DevTest Labs VM 的詳細資料。 

1. 在 [發行管線**管線**] 索引標籤上，選取 [**階段 1** ] 中的超連結文字來**查看階段**工作**+** ，然後選取 [**代理程式作業**] 旁的加號。 
   
1. 在 [**新增**工作] 底下，選取 [ **Azure PowerShell**]，然後選取 [**新增**]。 
   
1. 在左窗格中選取 [ **Azure PowerShell 腳本： FilePath** ]。 
   
1. 在右窗格中，填寫表單，如下所示：
   
   |欄位|值|
   |---|---|
   |**Azure 連線類型**|選取 [ **Azure Resource Manager**]。|
   |**Azure 訂用帳戶**|選取您的服務連接或訂用帳戶。| 
   |**腳本類型**|選取 [**指令檔路徑**]。|
   |**腳本路徑**|輸入您儲存到原始程式碼存放庫之 PowerShell 腳本的完整路徑和名稱。 您可以使用內建屬性來簡化路徑，例如：<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**腳本引數**|輸入前一項工作所填入的*labVmId*變數名稱，例如：<br /><br />`-labVmId '$(labVMId)'`|

此腳本會收集所需的值，並將它們儲存在發行管線內的環境變數中，以便您在後續步驟中輕鬆地參考它們。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>從 DevTest Labs VM 建立 VM 映射

下一個工作是在您的 Azure DevTest Labs 實例中建立新部署 VM 的映射。 後續當您想要執行開發工作或執行某些測試時，您即可使用此映像隨需建立 VM 的複本。 

1. 在 [發行管線**管線**] 索引標籤上，選取 [**階段 1** ] 中的超連結文字來**查看階段**工作**+** ，然後選取 [**代理程式作業**] 旁的加號。 
   
1. 在 [**新增**工作] 底下，選取 [ **Azure DevTest Labs 建立自訂映射**]，然後選取 [**新增**]。 
   
1. 請依照下列方式設定工作：
   
   |欄位|值|
   |---|---|
   |**Azure RM 訂用帳戶**|選取您的服務連接或訂用帳戶。|
   |**實驗室名稱**|選取將在其中建立映射之現有實驗室的名稱。|
   |**自訂映射名稱**|輸入自訂映射的名稱。|
   |**描述**（選擇性）|輸入描述，讓您稍後可以輕鬆地選取正確的映射。|
   |**來源實驗室 vm** > **來源實驗室 vm 識別碼**|如果您變更了 LabVMId 變數的預設名稱，請在這裡輸入。 預設值為 **$(labVMId)**。|
   |**輸出變數** > **自訂映射識別碼**|您可以視需要編輯變數的預設名稱。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>將您的應用程式部署至 DevTest Labs VM （選擇性）

您可以新增工作，將您的應用程式部署到新的 DevTest Labs VM。 您通常用來部署應用程式的工作是*目的電腦上*的*Azure 檔案複製*和 PowerShell。

這些工作的參數所需的 VM 資訊，會儲存在發行管線內名為**labVmRgName**、 **labVMIpAddress**和**labVMFqdn**的三個設定變數中。 如果您只想要試驗性地建立 DevTest Labs VM 和自訂映像，而不為其部署應用程式，則可以略過此步驟。

### <a name="delete-the-vm"></a>刪除 VM

最後一項工作是刪除您在 Azure DevTest Labs 實例中部署的 VM。 在已部署的 VM 上執行您所需的開發工作或測試之後，您通常會刪除 VM。 

1. 在 [發行管線**管線**] 索引標籤上，選取 [**階段 1** ] 中的超連結文字來**查看階段**工作**+** ，然後選取 [**代理程式作業**] 旁的加號。 
   
1. 在 [**新增**工作] 底下，選取 [ **Azure DevTest Labs 刪除 VM**]，然後選取 [**新增**]。 
   
1. 請依照下列方式設定工作：
   
   - 在 [ **AZURE RM 訂**用帳戶] 下，選取您的服務連接或訂用帳戶。 
   - 針對 [**實驗室 VM 識別碼**]，如果您變更 LabVMId 變數的預設名稱，請在這裡輸入。 預設值為 **$(labVMId)**。
   
### <a name="save-the-release-pipeline"></a>儲存發行管線

若要儲存新的發行管線：

1. 在 [發行管線] 頁面上選取 [為**新發行管線**命名]，然後輸入管線的新名稱。 
   
1. 選取右上方的 [**儲存**] 圖示。 

## <a name="create-and-run-a-release"></a>建立和執行發行

若要使用新管線建立和執行發行：

1. 在 [發行管線] 頁面上，選取右上方的 [**建立發行**]。 
   
1. 在 [**構件**] 底下，選取最新的組建，然後選取 [**建立**]。
   
1. 在每個發行階段，重新整理 Azure 入口網站中的 DevTest Labs 實例視圖，以查看建立 VM、建立映射和刪除 VM。

您可以在需要時使用自訂映射來建立 Vm。

## <a name="next-steps"></a>後續步驟
- 了解如何[使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
- 從[公用 DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)探索更多 DevTest Labs 自動化的快速入門 Resource Manager 範本。
- 如有必要，請參閱[Azure DevOps 疑難排解](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)頁面。
 
