---
title: 將 Azure 開發人員測試實驗室集成到 Azure 管道中
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293210"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>將 Azure 開發人員測試實驗室集成到 Azure 管道 CI/CD 管道中

可以使用 Azure*開發人員測試實驗室任務*擴展將 Azure 管道持續集成和連續交付 （CI/CD） 生成和發佈管道與 Azure DevTest 實驗室集成。 擴展安裝多個任務，包括： 

- 建立虛擬機器 (VM)
- 從 VM 建立自訂映像
- 刪除 VM 

例如，這些任務便於快速部署特定測試工作*的黃金映射*VM，然後在測試完成後刪除 VM。

本文演示如何使用 Azure DevTest Labs 任務創建和部署 VM、創建自訂映射，然後刪除 VM，所有這些都作為一個發佈管道。 通常，您將在您自己的自訂生成、測試和部署管道中單獨執行任務。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Prerequisites

- 註冊或登錄到[Azure DevOps](https://dev.azure.com)組織，並在組織中[創建專案](/vsts/organizations/projects/create-project)。 
  
- 從視覺化工作室應用商店安裝 Azure 開發人員測試實驗室任務擴展。
  
  1. 移至 [Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
  1. 選擇**獲取它免費**。
  1. 從下拉清單中選擇 Azure DevOps 組織，然後選擇 **"安裝**"。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>創建範本以生成 Azure VM 

本節介紹如何創建用於按需創建 Azure VM 的 Azure 資源管理器範本。

1. 要在訂閱中創建資源管理器範本，請按照["使用資源管理器"範本](devtest-lab-use-resource-manager-template.md)中的過程操作。
   
1. 產生 Resource Manager 範本之前，請先在建立 VM 的過程中新增 [WinRM 構件](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。 部署任務（如*目的電腦上的 Azure 檔副本*和*PowerShell）* 需要 WinRM 存取權限。 WinRM 專案需要主機名稱作為參數，該參數應為 VM 的完全限定功能變數名稱 （FQDN）。 
   
   > [!NOTE]
   > 當您透過共用 IP 位址使用 WinRM 時，您必須新增將外部連接埠對應至 WinRM 連接埠的 NAT 規則。 如果使用公共 IP 位址創建 VM，則不需要 NAT 規則。
   
   
1. 將範本另存為名為*CreateVMTemplate.json*的檔。
   
1. 將範本簽入原始程式碼管理系統。

## <a name="create-a-script-to-get-vm-properties"></a>創建腳本以獲取 VM 屬性

在發佈管道中運行任務步驟（如*Azure 檔副本*或*PowerShell）時*，以下腳本將收集將應用部署到 VM 所需的值。 通常使用這些任務將應用部署到 Azure VM。 這些任務需要 VM 資源組名稱、IP 位址和 FQDN 等值。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要創建指令檔，

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

1. 使用*GetLabVMParams.ps1*等名稱保存檔，並將其簽入原始程式碼管理系統。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>在 Azure Pipelines 中建立發行管線

要創建新的發佈管道，請進行：

1. 從 Azure DevOps 專案頁中，從左側導航中選擇**管道** > **發佈**。
1. 選取 [新增管線]****。
1. 在 **"選擇範本**"下，向下滾動並選擇 **"空作業**"，然後選擇"**應用**"。

### <a name="add-and-set-variables"></a>新增和設定變數

管道任務使用在 Azure 門戶中創建資源管理器範本時分配給 VM 的值。 

要添加值的變數， 

1. 在管道頁上，選擇 **"變數"** 選項卡。
   
1. 對於每個變數，選擇 **"添加"** 並輸入名稱和值：
   
   |名稱|值|
   |---|---|
   |*vmName*|在資源管理器範本中分配的 VM 名稱|
   |*使用者*|訪問 VM 的使用者名|
   |*密碼*|使用者名的密碼。 選擇鎖定圖示以隱藏並保護密碼。

### <a name="create-a-devtest-labs-vm"></a>創建開發人員測試實驗室 VM

下一步是創建用於未來部署的黃金映射 VM。 通過使用*Azure 開發人員測試實驗室創建 VM*任務，在 Azure 開發人員測試實驗室實例中創建 VM。

1. 在發佈**管道管道**選項卡上，選擇**階段 1**中的超連結文本以查看**階段任務**，然後選擇 **"代理作業**"旁邊的**+** 加號。 
   
1. 在 **"添加任務**"下，選擇**Azure 開發人員測試實驗室創建 VM，** 然後選擇"**添加**"。 
   
1. 選擇在左側窗格中**創建 Azure 開發人員測試實驗室 VM。** 

1. 在右窗格中，填寫表單，如下所示：
   
   |欄位|值|
   |---|---|
   |**Azure RM 訂閱**|在下拉清單中從可用**Azure 服務連接**或**可用 Azure 訂閱**中選擇服務連接或訂閱，然後根據需要選擇 **"授權**"。<br /><br />**注：** 有關創建到 Azure 訂閱的更多受限許可權連接的資訊，請參閱[Azure 資源管理器服務終結點](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)。|
   |**實驗室名稱**|選擇將在其中創建實驗室 VM 的現有實驗室的名稱。|
   |**範本名稱**|輸入保存到原始程式碼存儲庫的範本檔的完整路徑和名稱。 您可以使用內置屬性來簡化路徑，例如：<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**範本參數**|輸入前面定義的變數的參數：<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**輸出變數** > **實驗室 VM ID**|輸入創建的實驗室 VM ID 的變數。 如果使用預設**的 labVMId**，則可以在後續任務中將變數引用為 *$（labVMId）。*<br /><br />您可以創建預設名稱以外的名稱，但請記住在後續任務中使用正確的名稱。 您可以以以下形式編寫實驗室 VM ID：<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>收集開發人員測試實驗室 VM 的詳細資訊

執行您先前建立的指令碼，以收集 DevTest Labs VM 的詳細資料。 

1. 在發佈**管道管道**選項卡上，選擇**階段 1**中的超連結文本以查看**階段任務**，然後選擇 **"代理作業**"旁邊的**+** 加號。 
   
1. 在 **"添加任務**"下，選擇**Azure PowerShell，** 然後選擇 **"添加**"。 
   
1. 選擇**Azure PowerShell 腳本：** 左側窗格中的檔路徑。 
   
1. 在右窗格中，填寫表單，如下所示：
   
   |欄位|值|
   |---|---|
   |**Azure 連線類型**|選擇**Azure 資源管理器**。|
   |**Azure 訂閱**|選擇服務連接或訂閱。| 
   |**腳本類型**|選擇**指令檔路徑**。|
   |**腳本路徑**|輸入保存到原始程式碼存儲庫的 PowerShell 腳本的完整路徑和名稱。 您可以使用內置屬性來簡化路徑，例如：<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**腳本參數**|輸入由上一個任務填充的*labVmId*變數的名稱，例如：<br /><br />`-labVmId '$(labVMId)'`|

腳本收集所需的值並將其存儲在發佈管道中的環境變數中，因此您可以輕鬆地在後續步驟中引用它們。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>從開發人員測試實驗室 VM 創建 VM 映射

下一個任務是在 Azure DevTest Labs 實例中創建新部署的 VM 映射。 後續當您想要執行開發工作或執行某些測試時，您即可使用此映像隨需建立 VM 的複本。 

1. 在發佈**管道管道**選項卡上，選擇**階段 1**中的超連結文本以查看**階段任務**，然後選擇 **"代理作業**"旁邊的**+** 加號。 
   
1. 在 **"添加任務**"下，選擇**Azure 開發人員測試實驗室創建自訂映射**，然後選擇"**添加**"。 
   
1. 請依照下列方式設定工作：
   
   |欄位|值|
   |---|---|
   |**Azure RM 訂閱**|選擇服務連接或訂閱。|
   |**實驗室名稱**|選擇將在其中創建映射的現有實驗室的名稱。|
   |**自訂圖像名稱**|輸入自訂圖像的名稱。|
   |**描述**（可選）|輸入說明，以便以後輕鬆選擇正確的圖像。|
   |**源實驗室 VM** > **源實驗室 VM ID**|如果更改 LabVMId 變數的預設名稱，請在此處輸入它。 預設值為 **$(labVMId)**。|
   |**輸出變數** > **自訂圖像 ID**|如有必要，可以編輯變數的預設名稱。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>將應用部署到開發人員測試實驗室 VM（可選）

您可以添加任務以將應用部署到新的 DevTest Labs VM。 通常用於部署應用的任務是在目的電腦上執行*Azure 檔副本*和*PowerShell。*

這些任務的參數所需的 VM 資訊存儲在發佈管道中的三個組態變數中，分別名為**labVmRgName、labVMIp****位址**和**實驗室VMFqdn。** 如果您只想要試驗性地建立 DevTest Labs VM 和自訂映像，而不為其部署應用程式，則可以略過此步驟。

### <a name="delete-the-vm"></a>刪除 VM

最後一項任務是刪除在 Azure 開發人員測試實驗室實例中部署的 VM。 在已部署的 VM 上執行您所需的開發工作或測試之後，您通常會刪除 VM。 

1. 在發佈**管道管道**選項卡上，選擇**階段 1**中的超連結文本以查看**階段任務**，然後選擇 **"代理作業**"旁邊的**+** 加號。 
   
1. 在 **"添加任務**"下，選擇**Azure 開發人員測試實驗室刪除 VM，** 然後選擇"**添加**"。 
   
1. 請依照下列方式設定工作：
   
   - 在**Azure RM 訂閱**下，選擇服務連接或訂閱。 
   - 對於**實驗室 VM ID**，如果更改 LabVMId 變數的預設名稱，請在此處輸入它。 預設值為 **$(labVMId)**。
   
### <a name="save-the-release-pipeline"></a>保存發佈管道

要保存新版本管道：

1. 在發佈管道頁上選擇"**新建"管道**的名稱，然後輸入管道的新名稱。 
   
1. 選擇右上角的 **"保存"** 圖示。 

## <a name="create-and-run-a-release"></a>創建並運行版本

要使用新管道創建和運行版本，請：

1. 選擇 **"在**發佈管道頁面上的右上角創建版本"。 
   
1. 在 **"專案"** 下，選擇最新生成，然後選擇 **"創建**"。
   
1. 在每個發佈階段，刷新 Azure 門戶中的 DevTest Labs 實例視圖，以查看 VM 創建、映射創建和 VM 刪除。

您可以隨時使用自訂映射創建 VM。

## <a name="next-steps"></a>後續步驟
- 了解如何[使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
- 從[公用 DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)探索更多 DevTest Labs 自動化的快速入門 Resource Manager 範本。
- 如有必要，請參閱[Azure DevOps 故障排除](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)頁面。
 
