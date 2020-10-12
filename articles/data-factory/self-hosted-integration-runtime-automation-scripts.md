---
title: 使用本機 PowerShell 指令碼自動進行自我裝載整合執行階段安裝
description: 在本機電腦上自動安裝自我裝載整合執行階段。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 068586a96ad3655cb70171266bd58f56ed320fc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83662856"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>使用本機 PowerShell 指令碼自動進行自我裝載整合執行階段安裝
若要在本機電腦上自動安裝自我裝載整合執行階段 (而不是在可改為利用 Resource Manager 範本的 Azure VM 上)，您可使用本機 PowerShell 指令碼。 本文介紹可使用的兩個指令碼。

## <a name="prerequisites"></a>Prerequisites

* 在本機電腦上啟動 PowerShell。 若要執行指令碼，則必須選擇 [以系統管理員身分執行]。
* [下載](https://www.microsoft.com/download/details.aspx?id=39717)自我裝載整合執行階段軟體。 複製下載檔案所在的路徑。 
* 您也需要**驗證金鑰**，以註冊自我裝載整合執行階段。
* 若要自動進行手動更新，您需要有預先設定的自我裝載整合執行階段。

## <a name="scripts-introduction"></a>指令碼簡介 

> [!NOTE]
> 這些指令碼是使用自我裝載整合執行時間中[記載的命令列公用程式](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#set-up-an-existing-self-hosted-ir-via-local-powershell)所建立。 如有需要，可根據自動化需求以適當地自訂這些指令碼。
> 指令碼必須套用至每個節點，因此在高可用性設定 (2 個或更多節點) 時，請務必在所有節點上執行。

* 自動化安裝：使用 **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** 安裝並註冊新的自我裝載整合執行階段節點 - 這個指令碼可用來安裝自我裝載整合執行階段節點，並使用驗證金鑰進行註冊。 指令碼接受兩個引數，**第一個**指定[自我裝載整合執行階段](https://www.microsoft.com/download/details.aspx?id=39717)在本機磁碟上的位置，**第二個**指定**驗證金鑰** (用於註冊自我裝載 IR 節點)。

* 自動進行手動更新：將自我裝載 IR 節點更新為特定版本或最新版本的 **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** - 如果已關閉自動更新，或想要更充分掌控更新，也支援此功能。 指令碼可用來將自我裝載整合執行階段節點更新為最新版本或指定的較高版本 (無法進行降級)。 它會接受用來指定版本號碼的引數 (範例：-version 3.13.6942.1)。 當未指定任何版本時，它一律會將自我裝載 IR 更新為[下載](https://www.microsoft.com/download/details.aspx?id=39717)中找到的最新版本。
    > [!NOTE]
    > 只能指定最後 3 個版本。 在理想的情況下，這是用來將現有的節點更新為最新版本。 **它假設已註冊自我裝載 IR**。 

## <a name="usage-examples"></a>使用範例

### <a name="for-automating-setup"></a>自動化安裝
1. 從[這裡](https://www.microsoft.com/download/details.aspx?id=39717)下載自我裝載 IR。 
1. 指定上述下載 SHIR MSI (安裝檔案) 所在的路徑。 例如，如果路徑是 *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1*，則可使用下列 PowerShell 命令列範例來執行這項工作：

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > 將 [key] 取代為驗證金鑰，以註冊 IR。
    > 將 "username" 取代為使用者名稱。
    > 執行指令碼時，指定 "InstallGatewayOnLocalMachine.ps1" 檔案的位置。 在此範例中，我們將其儲存在桌面上。

1. 如果電腦上有一個預先安裝的自我裝載 IR，指令碼會自動解除安裝，然後設定一個新的。 您會看到下列快顯視窗：![設定整合執行階段](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. 當安裝和金鑰註冊完成時，即會在本機 PowerShell 中看到「成功安裝閘道」和「成功註冊閘道」結果。
        [![指令碼 1 執行結果](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>自動進行手動更新
這個指令碼用來更新/安裝 + 註冊最新的自我裝載整合執行階段。 指令碼會執行下列步驟：
1. 檢查目前的自我裝載 IR 版本
2. 取得最新版本或從引數取得指定的版本
3. 如果有比目前版本還新的版本：
    * 下載自我裝載 IR msi
    * 將其升級

您可遵循下列命令列範例，以使用這個指令碼：
* 下載並安裝最新的閘道：

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* 下載並安裝指定版本的閘道：
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   如果目前的版本已經是最新版本，則會看到建議無須更新的下列結果。   
    [![指令碼 2 執行結果](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)
