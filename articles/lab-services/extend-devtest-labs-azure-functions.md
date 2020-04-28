---
title: 使用 Azure Functions 延伸 Azure DevTest Labs |Microsoft Docs
description: 瞭解如何使用 Azure Functions 擴充 Azure DevTest Labs。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70014356"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>使用 Azure Functions 擴充 DevTest Labs
您可以使用 Azure Functions 來支援 DevTest Labs 已支援的其他案例。 Azure Functions 可以用來擴充服務的內建功能，以符合您的商務特定需求。 下列清單提供一些可能的案例。 本文說明如何執行其中一個範例案例。

- 提供實驗室中虛擬機器（Vm）的最上層摘要
- [設定實驗室以使用遠端桌面閘道](configure-lab-remote-desktop-gateway.md)
- 內部支援頁面上的相容性報告
- 讓使用者完成需要在訂用帳戶中增加許可權的作業
- [根據 DevTest Labs 事件啟動工作流程](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>概觀
[Azure Functions](../azure-functions/functions-overview.md)是 Azure 中的無伺服器計算平臺。 在 DevTest Labs 的解決方案中使用 Azure Functions，可讓我們使用自己的自訂程式碼來增強現有的功能。 如需 Azure Functions 的詳細資訊，請參閱[Azure Functions 檔](../azure-functions/functions-overview.md)。 為了說明 Azure Functions 如何協助滿足您的需求或 DevTest Labs 中的完整案例，本文會使用範例來提供實驗室中 Vm 的頂層摘要，如下所示：

**範例需求/案例**：使用者可以查看實驗室中所有 vm 的詳細資料，包括作業系統、擁有者和任何套用的成品。  此外，如果最近未套用 [套用**Windows 更新**] 成品，有一個簡單的方法可以套用它。

若要完成此案例，您將使用下列圖表中所述的兩個功能：  

![整體流程](./media/extend-devtest-labs-azure-functions/flow.png)

這些範例函式的原始程式碼位於[DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)中（可使用 c # 和 PowerShell 部署）。

- **UpdateInternalSupportPage**：此函式會查詢 DevTest Labs，並直接使用虛擬機器的詳細資料來更新內部支援頁面。
- **ApplyWindowsUpdateArtifact**：針對實驗室中的 VM，此函式會套用**Windows update**成品。

## <a name="how-it-works"></a>運作方式
當使用者在 DevTest Labs 中選取 [**內部支援**] 頁面時，他們會有預先填入的頁面，其中包含 vm、實驗室擁有者和支援連絡人的相關資訊。  

當您選取 [**選取此處以**重新整理] 按鈕時，頁面會呼叫第一個 Azure Function： **UpdateInternalSupportPage**。 函式會查詢 DevTest Labs 中的資訊，然後以新的資訊重寫**內部支援**頁面。

有一個可採取的額外動作，適用于最近未套用 Windows Update 成品的任何 Vm，將會有一個按鈕可將 Windows 更新套用至 VM。 當您選取 VM 的 [**執行 Windows update** ] 按鈕時，頁面會呼叫第二個 Azure Function： **ApplyWindowsUpdateArtifact**。 此函式會檢查虛擬機器是否正在執行，如果是，則會直接套用[Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)成品。

## <a name="step-by-step-walkthrough"></a>逐步解說
本節提供逐步指示，說明如何設定更新**內部支援**頁面所需的 Azure 資源。 此逐步解說提供延伸 DevTest Labs 的一個範例。 在其他案例中，您可以使用此模式。

### <a name="step-1-create-a-service-principal"></a>步驟1：建立服務主體 
第一個步驟是取得具有包含實驗室之訂用帳戶許可權的服務主體。 服務主體必須使用以密碼為基礎的驗證。 您可以使用[Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)、 [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)或[Azure 入口網站](../active-directory/develop/howto-create-service-principal-portal.md)來完成此作業。 如果您已經有要使用的服務主體，可以略過此步驟。

記下服務主體的 [**應用程式識別碼**]、[**金鑰**] 和 [**租使用者識別碼**]。 稍後在本逐步解說中，您將需要這些專案。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>步驟2：下載範例，並在 Visual Studio 2019 中開啟
在本機下載[c # Azure Functions 範例](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)的複本（透過複製存放庫或從[這裡](https://github.com/Azure/azure-devtestlab/archive/master.zip)下載儲存機制）。  

1. 使用 Visual Studio 2019 開啟範例解決方案。  
1. 安裝 Visual Studio 的**Azure 開發**工作負載（如果您尚未安裝）。 您可以透過 [**工具** -> ] [**取得工具和功能**] 功能表項目來安裝它。

    ![Azure 開發工作負載](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 建置方案。 選取 [**組建**]，然後選取 [**建立方案**] 功能表項目。

### <a name="step-3-deploy-the-sample-to-azure"></a>步驟3：將範例部署至 Azure
在 Visual Studio 的 [**方案總管**] 視窗中，以滑鼠右鍵按一下**AzureFunctions**專案，然後選取 [**發佈**]。 依照嚮導的指示完成發行至新的或現有的 Azure 函數應用程式。 如需使用 Visual Studio 來開發和部署 Azure 函式的詳細資訊，請參閱[使用 Visual Studio 開發 Azure Functions](../azure-functions/functions-develop-vs.md)。

![發佈對話方塊](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>步驟4：收集應用程式設定
發行函式之後，您必須從 Azure 入口網站取得這些函式的 Url。 

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。 
1. 尋找函數應用程式。
1. 在 [**函數應用程式**] 頁面上，選取函數。 
1. 選取 [**取得函數 URL** ]，如下圖所示。 

    ![Azure 函式 Url](./media/extend-devtest-labs-azure-functions/function-url.png)
4. 複製並儲存 URL。 針對其他 Azure 函數重複這些步驟。 

您也將需要服務主體的其他資訊，例如應用程式識別碼、金鑰和租使用者識別碼。


### <a name="step-5--update-application-settings"></a>步驟5：更新應用程式設定
在 Visual Studio 中，在發佈 Azure 函式之後，選取 [**動作**] 底下的 [**編輯 Azure App Service 設定**]。 更新下列應用程式設定（遠端）：

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays （預設為7）
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![應用程式設定](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>步驟6：測試 Azure 函數
本逐步解說的最後一個步驟是測試 Azure 函數。  

1. 在步驟3中建立的函數應用程式中，流覽至**UpdateInternalSupportPage**函式。 
1. 選取頁面右側的 [**測試**]。 
1. 在路由屬性（LABNAME、RESOURCEGROUPNAME 和 SUBSCRIPTIONID）中輸入。
1. 選取 **[執行]** 以執行函數。  

    此函式會更新指定之實驗室的 [內部支援] 頁面。 它也包含一個按鈕，可讓使用者在下一次直接呼叫函式

    ![測試函式](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>後續步驟
Azure Functions 可以協助擴充 DevTest Labs 的功能，超越已內建的範圍，並協助客戶滿足其小組的獨特需求。 此模式可以擴充 & 進一步擴充，以涵蓋更多的資訊。  若要深入瞭解 DevTest Labs，請參閱下列文章： 

- [DevTest Labs 企業參考架構](devtest-lab-reference-architecture.md)
- [常見問題集](devtest-lab-faq.md)
- [向上擴充 DevTest Labs](devtest-lab-guidance-scale.md)
- [使用 PowerShell 將 DevTest Labs 自動化](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








