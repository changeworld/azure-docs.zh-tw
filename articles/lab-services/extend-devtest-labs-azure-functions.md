---
title: 使用 Azure 功能擴展 Azure 開發人員測試實驗室 |微軟文檔
description: 瞭解如何使用 Azure 函數擴展 Azure 開發人員測試實驗室。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014356"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>使用 Azure Functions 擴充 DevTest Labs
可以使用 Azure 函數支援除 DevTest Labs 已支援的方案之外的其他方案。 Azure 函數可用於擴展服務的內置功能，以滿足特定于業務的需求。 下面的清單提供了一些可能的方案。 本文介紹如何實現這些示例方案之一。

- 在實驗室中提供虛擬機器 （VM） 的頂級摘要
- [設定實驗室以使用遠端桌面閘道](configure-lab-remote-desktop-gateway.md)
- 內部支援頁面上的合規性報告
- 使使用者能夠完成需要在訂閱中增加許可權的操作
- [基於 DevTest 實驗室事件啟動工作流](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>總覽
[Azure 函數](../azure-functions/functions-overview.md)是 Azure 中的無伺服器計算平臺。 使用使用開發人員測試實驗室的解決方案中的 Azure 函數，可以利用自己的自訂代碼增強現有功能。 有關 Azure 函數的詳細資訊，請參閱[Azure 函數文檔](../azure-functions/functions-overview.md)。 為了說明 Azure 函數如何説明滿足 DevTest 實驗室中的要求或完整的方案，本文使用在實驗室中提供 VM 的頂級摘要的示例，如下所示：

**示例要求/方案**：使用者可以查看有關實驗室中所有 VM 的詳細資訊，包括作業系統、擁有者和任何應用的專案。  此外，如果最近未應用 **"應用 Windows 更新**"專案，則應用它的方法很簡單。

要完成方案，您將使用如下圖中所述的兩個函數：  

![總體流程](./media/extend-devtest-labs-azure-functions/flow.png)

這些示例函數的原始程式碼位於[DevTest Labs GitHub 存儲庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)中（C# 和 PowerShell 實現都可用）。

- **更新內部支援頁**：此功能查詢 DevTest 實驗室，並直接更新內部支援頁，並提供有關虛擬機器的詳細資訊。
- **應用 Windows 更新工件**：對於實驗室中的 VM，此功能應用**Windows 更新**專案。

## <a name="how-it-works"></a>運作方式
當使用者選擇 DevTest Labs**中的"內部支援**"頁時，他們有一個預填充的頁面，其中包含有關 VM、實驗室擁有者和支援連絡人的資訊。  

當您選擇 **"選擇此處刷新**"按鈕時，頁面將調用第一個 Azure 函數：**更新內部支援頁**。 該函數查詢 DevTest Labs 的資訊，然後使用新資訊重寫**內部支援**頁。

對於最近未應用 Windows Update 專案的任何 VM，還可以執行其他操作，將有一個按鈕將視窗更新應用於 VM。 當您為 VM 選擇 "**運行 Windows 更新**"按鈕時，該頁將調用第二個 Azure 函數：應用 Windows**更新項**。 此函數檢查虛擬機器是否正在運行，如果是，則直接應用[Windows 更新](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)專案。

## <a name="step-by-step-walkthrough"></a>分步演練
本節提供用於設置更新**內部支援**頁所需的 Azure 資源的分步說明。 本演練提供了擴展 DevTest 實驗室的一個示例。 您可以將此模式用於其他方案。

### <a name="step-1-create-a-service-principal"></a>第 1 步：創建服務主體 
第一步是獲取具有包含實驗室的訂閱許可權的服務主體。 服務主體必須使用基於密碼的身份驗證。 它可以使用 Azure [CLI、Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)或[Azure 門戶](../active-directory/develop/howto-create-service-principal-portal.md)來完成。 如果您已有要使用的服務主體，則可以跳過此步驟。

記下服務主體**的應用程式 ID**、**金鑰**和**租戶 ID。** 在本演練中，您將需要它們。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>第 2 步：下載示例並在 Visual Studio 2019 中打開
在本地下載[C# Azure 函數示例](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)的副本（通過克隆存儲庫或[從這裡](https://github.com/Azure/azure-devtestlab/archive/master.zip)下載存儲庫）。  

1. 使用 Visual Studio 2019 打開示例解決方案。  
1. 如果尚未安裝 Visual Studio，請安裝 Azure**開發**工作負荷。 它可以通過**工具** -> **獲取工具和功能**功能表項目安裝）。

    ![Azure 開發工作負載](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 建置方案。 選擇 **"生成****"，然後選擇"生成解決方案"** 功能表項目。

### <a name="step-3-deploy-the-sample-to-azure"></a>步驟 3：將示例部署到 Azure
在視覺化工作室中，在 **"解決方案資源管理器"** 視窗中，按右鍵**Azure 功能**專案，然後選擇 **"發佈**"。 請按照嚮導完成發佈到新函數應用或現有 Azure 函數應用。 有關使用 Visual Studio 開發和部署 Azure 函數的詳細資訊，請參閱[使用 Visual Studio 開發 Azure 函數](../azure-functions/functions-develop-vs.md)。

![發佈對話方塊](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>第 4 步：收集應用程式設定
發佈函數後，您需要從 Azure 門戶獲取這些函數的 URL。 

1. 導航到[Azure 門戶](https://portal.azure.com)。 
1. 查找功能應用。
1. 在 **"功能應用"** 頁上，選擇該函數。 
1. 選擇 **"獲取函數 URL"，** 如下圖所示。 

    ![Azure 函數 URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. 複製並儲存 URL。 對其他 Azure 函數重複這些步驟。 

您還需要有關服務主體的其他資訊，如應用程式 ID、金鑰和租戶 ID。


### <a name="step-5--update-application-settings"></a>第 5 步：更新應用程式設定
在視覺化工作室中，在發佈 Azure 函數後，選擇"**操作**"下的 **"編輯 Azure 應用服務設置**"。 更新以下應用程式設定（遠端）：

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- Windows 更新允許天（預設為 7）
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![應用程式設定](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>第 6 步：測試 Azure 函數
本演練的最後一步是測試 Azure 函數。  

1. 導航到步驟 3 中創建的函數應用中的 **"更新內部支援Page"** 功能。 
1. 選擇頁面右側的 **"測試**"。 
1. 在路由屬性（LABNAME、資源組名稱和訂閱 ID）中輸入。
1. 選擇 **"運行**"以執行該函數。  

    此功能將更新指定實驗室的內部支援頁。 它還包括一個按鈕，供使用者下次直接調用該函數

    ![測試功能](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>後續步驟
Azure 函數可説明將 DevTest Labs 的功能擴展到已內置功能之外，並説明客戶滿足其團隊的獨特要求。 這種模式可以&進一步擴展，以覆蓋更多。  要瞭解有關 DevTest 實驗室的更多內容，請參閱以下文章： 

- [開發人員測試實驗室企業參考體系結構](devtest-lab-reference-architecture.md)
- [常見問題](devtest-lab-faq.md)
- [擴展開發測試實驗室](devtest-lab-guidance-scale.md)
- [使用 PowerShell 實現自動化開發測試實驗室](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








