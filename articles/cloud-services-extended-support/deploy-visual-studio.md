---
title: '使用雲端服務 (延伸支援)  (Preview) '
description: 立即瞭解如何使用 Azure Resource Manager 搭配 Visual Studio 來建立和部署 Azure 雲端服務
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 1c2264cb76e062f5f22c0460572ec01750086a04
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744942"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>使用 Visual Studio 來建立和部署 Azure 雲端服務 (延伸支援) 

從 Visual Studio 2019 16.9 版 Preview 1 開始，您可以搭配 Azure Resource Manager 使用雲端服務 (延伸支援) ，以大幅簡化及將其現代化 Azure 資源的維護和管理。 您也可以將現有的雲端服務專案轉換成延伸支援雲端服務專案。

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>註冊訂用帳戶的功能
雲端服務 (延伸支援) 目前為預覽狀態。 註冊訂用帳戶的功能，如下所示：

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
如需詳細資訊，請參閱 [部署雲端服務的必要條件 (延伸支援) ](deploy-prerequisite.md)

## <a name="create-a-project"></a>建立專案

Visual Studio 提供專案範本，可讓您建立具有延伸支援的 Azure 雲端服務，名為「 **Azure 雲端服務」 (延伸支援)**。 雲端服務是簡單的一般用途 Azure 服務。 一旦建立專案之後，Visual Studio 可讓您設定、偵測雲端服務，並將其部署到 Azure。

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>若要在 Visual Studio 中建立 Azure 雲端服務 (延伸支援) 專案

本節將逐步引導您在 Visual Studio 中建立具有一或多個 web 角色的 Azure 雲端服務專案。

1. 在 [開始] 視窗中，選擇 [建立新專案]。

1. 在 [搜尋] 方塊中輸入 *Cloud*，然後選擇 [ **Azure 雲端服務 (延伸支援)**。

   ![具有延伸支援的新 Azure 雲端服務](./media/choose-project-template.png)

1. 提供專案名稱，然後選擇 [建立]。

   ![提供專案名稱](./media/configure-new-project.png)

1. 在 [新增 Microsoft Azure 雲端服務] 對話方塊中，選取您想要新增的角色，然後選擇向右箭號按鈕以將它們新增到您的方案。

    ![選取新的 Azure 雲端服務角色](./media/choose-roles.png)

1. 若要將您所新增的角色重新命名，可將滑鼠游標停留在 [新增 Microsoft Azure 雲端服務] 對話方塊中的角色上方，然後從操作功能表中選取[重新命名]。 您也可以在方案內 (在 [方案總管] 中) 為已新增的角色重新命名。

    ![重新命名 Azure 雲端服務角色](./media/new-cloud-service-rename.png)

Visual Studio Azure 專案與方案中的角色專案有關。 專案還包含「服務定義檔」和「服務組態檔」：

- **服務定義** 檔-定義應用程式的執行時間設定，包括需要哪些角色、端點和虛擬機器大小。
- **服務組態檔** - 設定一個角色可以執行的執行個體數目，以及為角色定義的設定值。

如需這些檔案的詳細資訊，請參閱 [使用 Visual Studio 設定 Azure 雲端服務的角色](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)。

## <a name="publish-a-cloud-service"></a>發佈雲端服務

1. 在 Visual Studio 中建立或開啟 Azure 雲端服務專案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後從操作功能表中選取 [發佈]。

   ![登入頁面](./media/publish-step-1.png)

1. **帳戶** - 選取帳戶或選取帳戶下拉式清單中的 [新增帳戶]。

1. **選擇您的訂用帳戶** - 選擇要用於部署的訂用帳戶。 您用來部署雲端服務 (延伸支援的訂用帳戶，) 需要透過角色型存取控制指派擁有者或參與者角色 (RBAC) 。 如果您的訂用帳戶沒有任何一個角色，請參閱 [新增角色指派的步驟](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-steps) ，以新增角色指派，然後再繼續進行。

1. 選擇 [ **下一步]** 移至 [ **設定** ] 頁面。

   ![一般設定](./media/publish-settings.png)

1. **雲端服務** -使用下拉式清單，選取現有的雲端服務，或選取 [ **建立新** 的]，然後建立雲端服務。 資料中心會在每個雲端服務的括弧中顯示。 建議雲端服務的資料中心位置與儲存體帳戶的資料中心位置相同。

   如果您選擇建立新的雲端服務，您會看到 [ **建立雲端服務 (延伸支援)** ] 對話方塊。 指定您要用於雲端服務的位置和資源群組。

   ![建立具有延伸支援的雲端服務](./media/extended-support-dialog.png)

1. **建置組態** - 選取 [偵錯] 或 [發行]。

1. **服務組態** - 選取 [雲端] 或 [本機]。

1. **儲存體帳戶** -選取要用於此部署的儲存體帳戶，或 **建立新** 的以建立儲存體帳戶。 區域會針對每個儲存體帳戶顯示在括弧中。 建議您將儲存體帳戶的資料中心位置與雲端服務的資料中心位置相同， (一般設定) 。

   Azure 儲存體帳戶會儲存應用程式部署的封裝。 部署應用程式之後，封裝會從儲存體帳戶中移除。

1. **Key Vault** -指定包含這個雲端服務之秘密的 Key Vault。 如果啟用遠端桌面，或將憑證新增至設定，則會啟用此功能。

1. **啟用所有角色的遠端桌面**：如果您想要從遠端連線到服務，請選取此選項。 系統會要求您指定認證。

   ![遠端桌面設定](./media/remote-desktop-configuration.png)

1. 選擇 [ **下一步]** 移至 [ **診斷設定** ] 頁面。

   ![診斷設定](./media/diagnostics-settings.png)

   診斷可讓您針對 Azure 雲端服務 (或 Azure 虛擬機器) 進行疑難排解。 如需診斷的相關資訊，請參閱[為 Azure 雲端服務和虛擬機器設定診斷功能](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)。 如需 Application Insights 的相關資訊，請參閱[什麼是 Application Insights？](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。

1. 選擇 [ **下一步]** 以移至 [ **摘要** ] 頁面。

   ![[摘要]](./media/publish-summary.png)

1. **目標設定檔** - 您可以選擇從您所選擇的設定建立發行設定檔。 例如，您可能會建立一個設定檔用於測試環境，並建立另一個用於生產。 若要儲存這個設定檔，請選擇 [**儲存**] 圖示。 此精靈會建立設定檔並將它儲存在 Visual Studio 專案。 若要修改設定檔名稱，請開啟 [目標設定檔] 清單，然後選擇 [**管理...]**。

   > [!Note]
   > 發佈設定檔會出現在 Visual Studio 的 [方案總管] 中，而且設定檔設定會寫入至副檔名為.azurePubxml 的檔案。 設定會儲存為 XML 標記的屬性。

1. 設定專案部署的所有設定後，請選取對話方塊底部的 [發佈]。 您可以在 Visual Studio 的 [ **Azure 活動記錄** ] 輸出視窗中，監視進程狀態。

恭喜！ 您已將延伸支援雲端服務專案發佈至 Azure。 若要使用相同的設定再次發行，您可以重複使用發行設定檔，或重複這些步驟來建立新的設定檔。

## <a name="clean-up-azure-resources"></a>清除 Azure 資源

若要清除遵循本教學課程所建立的 Azure 資源，請移至 [Azure 入口網站](https://portal.azure.com)，選擇 [ **資源群組**]，尋找並開啟您用來建立服務的資源群組，然後選擇 [ **刪除資源群組**]。

## <a name="next-steps"></a>後續步驟

使用 [**發佈**] 畫面上的 [設定 **] 按鈕，** 設定 (CI) 的持續整合。 如需詳細資訊，請參閱 [Azure Pipelines 檔](https://docs.microsoft.com/azure/devops/pipelines)。
