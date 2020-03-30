---
title: 設置暫存環境
description: 瞭解如何將應用部署到非生產槽，並自動交換到生產中。 提高可靠性並消除部署中應用停機時間。
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300838"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>在 Azure App Service 中設定預備環境
<a name="Overview"></a>

將 Web 應用、Linux 上的 Web 應用、移動後端或 API 應用部署到[Azure 應用服務](https://go.microsoft.com/fwlink/?LinkId=529714)時，可以在 **"標準**"、"**高級**"或 **"獨立**應用服務方案"層中運行時使用單獨的部署槽，而不是預設生產槽。 部署槽是具有其自己的主機名稱的即時應用。 兩個部署位置 (包括生產位置) 之間的應用程式內容與設定項目可以互相交換。 

將應用程式部署至非生產位置具有下列優點：

* 您可以先驗證預備部署位置中的應用程式變更，再將它與生產位置進行交換。
* 先將應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可以排除部署應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 在不需要交換前驗證時配置[自動交換](#Auto-Swap)，可以自動執行整個工作流。
* 交換之後，先前具有預備應用程式的位置，現在已經有之前的生產應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以恢復「上一個已知的良好網站」。

每個 App Service 方案層所支援的部署位置個數都不一樣。 使用部署槽不收取額外費用。 要瞭解應用的層支援的插槽數，請參閱[應用服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。 

要將應用擴展到其他層，請確保目標層支援應用已使用的插槽數。 例如，如果應用的插槽超過 5 個，則無法將其向下縮放到**標準**層，因為**標準**層僅支援五個部署槽。 

<a name="Add"></a>

## <a name="add-a-slot"></a>新增位置
應用程式必須在 [標準]****、[進階]**** 或 [隔離]**** 層中執行，您才能啟用多個部署位置。


1. 在[Azure 門戶](https://portal.azure.com/)中，搜索並選擇**應用服務**並選擇應用。 
   
    ![搜索應用服務](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. 在左側窗格中，選擇 **"部署"槽** > **"添加插槽**。
   
    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果應用尚未位於**標準**、**高級**或**隔離**層中，您將收到一條消息，指示支援的層以啟用暫存發佈。 此時，您可以選擇 **"升級**"，然後轉到應用的 **"縮放"** 選項卡，然後再繼續。
   > 

3. 在"**添加槽**"對話方塊中，為槽指定名稱，並選擇是否從其他部署槽克隆應用配置。 選擇 **"添加**"以繼續。
   
    ![配置源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    可以從任何現有插槽克隆配置。 可複製的設定包括應用程式設定、連接字串、語言架構版本、Web 通訊端、HTTP 版本和平台位元。

4. 添加插槽後，選擇 **"關閉"** 以關閉對話方塊。 新插槽現在顯示在 **"部署"插槽**頁上。 預設情況下，新插槽的**流量百分比**設置為 0，所有客戶流量都路由到生產槽。

5. 選擇新的部署槽以打開該槽的資源頁。
   
    ![部署槽標題](./media/web-sites-staged-publishing/StagingTitle.png)

    預備位置和任何其他 App Service 應用程式一樣，具有管理頁面。 您可以變更位置的組態。 頁面頂端會顯示位置的名稱，提醒您正在檢視部署位置。

6. 在槽的資源頁面上選擇應用 URL。 部署槽有自己的主機名稱，也是即時應用。 要限制對部署槽的公共訪問，請參閱[Azure 應用服務 IP 限制](app-service-ip-restrictions.md)。

新的部署位置中沒有任何內容，即使您複製其他位置的設定，仍是如此。 例如，您可以使用[Git 發佈到此槽](app-service-deploy-local-git.md)。 您可以從不同的存放庫分支或不同的存放庫部署至位置。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>交換期間發生的情況

### <a name="swap-operation-steps"></a>交換操作步驟

當您將兩個插槽（通常從暫存槽交換到生產槽）時，App Service 會執行以下操作以確保目標槽不會遇到停機：

1. 將目標槽（例如生產槽）中的以下設置應用於源槽的所有實例： 
    - [特定于插槽](#which-settings-are-swapped)的應用設置和連接字串（如果適用）。
    - [連續部署](deploy-continuous-deployment.md)設置（如果已啟用）。
    - [應用服務身份驗證](overview-authentication-authorization.md)設置（如果已啟用）。
    
    這些情況中的任何一個都觸發源插槽中的所有實例以重新開機。 [在與預覽交換](#Multi-Phase)期間，這標誌著第一階段的結束。 交換操作已暫停，您可以驗證源槽是否與目標槽的設置正常工作。

1. 等待源插槽中的每個實例完成重新開機。 如果任何實例無法重新開機，交換操作將恢復所有更改到源槽並停止該操作。

1. 如果啟用[了本機快取](overview-local-cache.md)，則通過在源槽的每個實例上向應用程式根（"/"）發出 HTTP 要求來觸發本機快取初始化。 等待，直到每個實例返回任何 HTTP 回應。 本機快取初始化會導致每個實例上再次重新開機。

1. 如果通過[自訂預熱](#Warm-up)啟用[自動交換](#Auto-Swap)，則通過在源槽的每個實例上向應用程式根（"/"）發出 HTTP 要求來觸發[應用程式啟動](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)。

    如果未`applicationInitialization`指定，則將 HTTP 要求觸發每個實例上的源槽的應用程式根。 
    
    如果實例返回任何 HTTP 回應，則認為該回應已預熱。

1. 如果源插槽上的所有實例都成功預熱，則通過切換兩個插槽的路由規則來交換兩個插槽。 在此步驟之後，目標槽（例如，生產槽）具有以前在源槽中預熱的應用。

1. 現在，源槽以前在目標槽中具有預交換應用，則通過應用所有設置並重新啟動實例來執行相同的操作。

在交換操作的任何時候，初始化交換應用的所有工作都發生在源插槽上。 在準備和預熱源槽時，無論交換成功或失敗，目標槽都保持線上狀態。 要將暫存槽與生產槽交換，請確保生產槽始終為目標槽。 這樣，交換操作不會影響您的生產應用。

### <a name="which-settings-are-swapped"></a>哪些設定已交換？

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

要將應用設置或連接字串配置為粘附到特定插槽（未交換），請轉到該插槽的 **"配置"** 頁。 添加或編輯設置，然後選擇**部署槽設置**。 選中此核取方塊會告訴應用服務，該設置不可交換。 

![位置設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交換兩個位置 
您可以在應用的部署槽頁和 **"概述"** 頁上交換部署**槽**。 有關插槽交換的技術詳細資訊，請參閱[交換期間發生的情況](#AboutConfiguration)。

> [!IMPORTANT]
> 在將應用從部署槽交換到生產中之前，請確保生產是目標槽，並且源槽中的所有設置都完全按照您希望在生產中配置它們的方式配置。
> 
> 

要交換部署槽：

1. 轉到應用的部署**槽**頁面，然後選擇 **"交換**"。
   
    ![交換按鈕](./media/web-sites-staged-publishing/SwapButtonBar.png)

    "**交換**"對話方塊顯示所選源槽和目標插槽中的設置，這些設置將被更改。

2. 選取所需的 [來源]**** 和 [目標]**** 位置。 目標通常是生產位置。 此外，選擇"**源更改**和目標**更改**"選項卡，並驗證配置更改是否預期。 完成後，您可以通過選擇 **"交換**"來立即交換插槽。

    ![完整的交換](./media/web-sites-staged-publishing/SwapImmediately.png)

    要查看目標槽在交換發生之前如何使用新設置運行，請不要選擇 **"交換**"，而是按照["交換與預覽"](#Multi-Phase)中的說明操作。

3. 完成後，通過選擇 **"關閉**"關閉對話方塊來關閉對話方塊。

如果遇到任何問題，請參閱[故障排除交換](#troubleshoot-swaps)。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>使用預覽交換 (多階段交換)

在作為目標槽交換到生產之前，請驗證應用是否使用交換的設置運行。 源槽在交換完成之前也會預熱，這對於任務關鍵型應用程式是可取的。

使用預覽執行交換時，應用服務執行相同的[交換操作](#AboutConfiguration)，但在第一步後暫停。 然後，您可以在完成交換之前驗證暫存插槽上的結果。 

如果取消交換，應用服務將配置元素重新應用於源槽。

要與預覽交換：

1. 按照[交換部署槽](#Swap)中的步驟操作，但選擇 **"使用預覽執行交換**"。

    ![使用預覽交換](./media/web-sites-staged-publishing/SwapWithPreview.png)

    該對話方塊顯示源插槽中的配置在第 1 階段中如何更改，以及源槽和目標插槽在第 2 階段如何更改。

2. 準備好開始交換時，選擇 **"開始交換**"。

    當階段 1 結束時，在對話方塊中通知您。 通過 前往`https://<app_name>-<source-slot-name>.azurewebsites.net`預覽源槽中的交換。 

3. 準備好完成待定交換時，在交換操作中選擇 **"完成掉期****"，** 然後選擇 **"完成掉期**"。

    要取消掛起的交換，請選擇 **"取消掉期**"。

4. 完成後，通過選擇 **"關閉**"關閉對話方塊來關閉對話方塊。

如果遇到任何問題，請參閱[故障排除交換](#troubleshoot-swaps)。

要自動進行多相交換，請參閱[使用 PowerShell 進行自動化](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>回滾交換
在交換位置後，若目標位置 (例如生產位置) 發生任何錯誤，請立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>設定自動交換

> [!NOTE]
> Linux 上的 Web 應用不支援自動交換。

自動交換簡化了 Azure DevOps 方案，您希望在應用客戶零冷啟動和零停機時間的情況下連續部署應用。 當從插槽啟用自動交換到生產時，每次將代碼更改推送到該插槽時，App Service 會在應用在源槽中預熱後自動[將應用交換到生產](#swap-operation-steps)中。

   > [!NOTE]
   > 在為生產槽配置自動交換之前，請考慮在非生產目標槽上測試自動交換。
   > 

要配置自動交換：

1. 轉到應用的資源頁面。 選擇 **"部署"槽** > *\<所需的源插槽>* > **配置** > **常規設置**。
   
2. 對於**啟用的自動交換**，請選擇 **"打開**"。 然後選擇**自動交換部署槽**所需的目標槽，然後在命令列上選擇 **"保存**"。 
   
    ![配置自動交換的選擇](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 執行推送至來源位置的程式碼。 自動交換在短時間後發生，更新將反映在目標插槽的 URL 中。

如果遇到任何問題，請參閱[故障排除交換](#troubleshoot-swaps)。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>指定自訂預熱

某些應用可能需要在交換前進行自訂預熱操作。 Web.config`applicationInitialization`中的配置元素允許您指定自訂初始化操作。 [交換操作](#AboutConfiguration)等待此自訂預熱完成，然後再與目標插槽交換。 下面是一個示例 Web.config 片段。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

有關自訂元素的詳細資訊，`applicationInitialization`請參閱[最常見的部署槽交換失敗以及如何修復它們](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)。

您還可以使用以下一個或兩個[應用設置](configure-common.md)自訂預熱行為：

- `WEBSITE_SWAP_WARMUP_PING_PATH`：ping 來預熱網站的路徑。 請指定開頭為斜線的自訂路徑作為值，以新增此應用程式設定。 例如 `/statuscheck`。 預設值是 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`：預熱操作的有效 HTTP 回應代碼。 請以 HTTP 代碼的逗號分隔清單方式新增此應用程式設定。 例如`200,202`。 如果返回的狀態碼不在清單中，則停止預熱和交換操作。 預設是所有回應碼都有效。

> [!NOTE]
> `<applicationInitialization>`配置元素是每個應用啟動的一部分，而兩個預熱行為應用設置僅適用于插槽交換。

如果遇到任何問題，請參閱[故障排除交換](#troubleshoot-swaps)。

## <a name="monitor-a-swap"></a>監視交換

如果[交換操作](#AboutConfiguration)需要很長時間才能完成，則可以在[活動日誌](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中獲取有關交換操作的資訊。

在門戶中的應用資源頁上，在左側窗格中，選擇 **"活動日誌**"。

交換作業在記錄查詢中會顯示為 `Swap Web App Slots`。 您可以展開它，然後選取其中一個子作業或錯誤，以查看詳細資料。

## <a name="route-traffic"></a>路由流量

根據預設，對應用程式生產 URL (`http://<app_name>.azurewebsites.net`) 的所有用戶端要求都會路由至生產位置。 您可以將部分流量路由至其他位置。 如果您需要使用者針對新的更新提供意見反應，但尚未準備好要將更新發行至生產環境，這項功能將有其效用。

### <a name="route-production-traffic-automatically"></a>自動路由生產流量

要自動路由生產流量：

1. 轉到應用的資源頁面並選擇 **"部署"槽**。

2. 針對您要路由到的位置，在其 [流量百分比]**** 資料行中指定百分比 (介於 0 到 100 之間)，以代表您要路由的總流量。 選取 [儲存]****。

    ![設置流量百分比](./media/web-sites-staged-publishing/RouteTraffic.png)

保存設置後，用戶端的指定百分比將隨機路由到非生產槽。 

用戶端自動路由到特定插槽後，它將"固定"到該插槽，以達到該用戶端會話的生命週期。 用戶端瀏覽器中，您可以查看 HTTP 標頭中的 `x-ms-routing-name` Cookie，以確認您的工作階段固定到哪個位置。 路由至「預備」位置的要求具有 Cookie `x-ms-routing-name=staging`。 路由至生產位置的要求具有 Cookie `x-ms-routing-name=self`。

   > [!NOTE]
   > 在 Azure 門戶旁邊，還可以使用 Azure [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) CLI 中的命令從 CI/CD 工具（如 DevOps 管道或其他自動化系統）設置路由百分比。
   > 

### <a name="route-production-traffic-manually"></a>手動路由生產流量

除了自動流量路由以外，App Service 也可以將要求路由至特定位置。 當您希望使用者能夠加入宣告或退出宣告測試版應用時，這非常有用。 若要手動路由生產流量，您可以使用 `x-ms-routing-name` 查詢參數。

例如，要讓使用者退出宣告測試版應用，可以將此連結放在網頁上：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字串 `x-ms-routing-name=self` 會指定生產位置。 用戶端瀏覽器訪問連結後，該連結將重定向到生產槽。 每個後續請求都有`x-ms-routing-name=self`將會話固定到生產槽的 Cookie。

要允許使用者加入宣告測試版應用，請將相同的查詢參數設置為非生產槽的名稱。 以下是範例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

預設情況下，新插槽的路由規則顯示為`0%`灰色。 當您顯式將此值設置為`0%`（以黑色文本顯示）時，使用者可以通過使用`x-ms-routing-name`查詢參數手動訪問暫存槽。 但是，它們不會自動路由到插槽，因為路由百分比設置為 0。 這是一個高級方案，您可以在其中將暫存槽"隱藏"到公共位置，同時允許內部團隊測試該插槽上的更改。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>刪除插槽

搜索並選擇你的應用。 選擇 **"部署"槽槽** > *\<以刪除>* > **概述**。 在命令列上選擇 **"刪除**"。  

![刪除部署位置](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>使用 PowerShell 自動執行

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell 模組提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure App Service 中的部署位置。

如需安裝與設定 Azure PowerShell，以及使用您的 Azure 訂用帳戶驗證 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Microsoft Azure PowerShell](/powershell/azure/overview)(英文)。  

---
### <a name="create-a-web-app"></a>建立 Web 應用程式
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>創建插槽
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>使用預覽啟動交換（多相交換），並將目標槽配置應用於源槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>取消掛起的交換（與審核交換）並還原源插槽配置
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交換部署位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>監視活動日誌中的交換事件
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>刪除插槽
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>使用資源管理器範本實現自動化

[Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview)是用於自動部署和配置 Azure 資源的聲明性 JSON 檔。 要使用資源管理器範本交換插槽，您將在*Microsoft.Web/網站/插槽*和*Microsoft.Web/網站*資源上設置兩個屬性：

- `buildVersion`：這是一個字串屬性，表示部署在插槽中的應用程式的當前版本。 例如："v1"，"1.0.0.1"，或"2019-09-20T11：53：25.2887393-07：00"。
- `targetBuildVersion`：這是一個字串屬性，用於指定`buildVersion`槽應具有的內容。 如果目標BuildVersion不等於當前`buildVersion`，那麼這將通過查找具有指定的`buildVersion`插槽來觸發交換操作。

### <a name="example-resource-manager-template"></a>資源管理器範本示例

以下資源管理器範本將更新`buildVersion`暫存槽並設置生產槽上的 。 `targetBuildVersion` 這將交換兩個插槽。 該範本假定您已經創建了一個帶有名為"暫存"的槽的 Web 應用程式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

此資源管理器範本是冪等的，這意味著它可以重複執行並生成插槽的相同狀態。 第一次執行後`targetBuildVersion`，將匹配當前`buildVersion`，因此不會觸發交換。

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>使用 CLI 實現自動化

如需適用於部署位置的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，請參閱 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>故障排除交換

如果在[插槽交換](#AboutConfiguration)期間發生任何錯誤，則將其記錄在*D：\home_LogFiles_eventlog.xml*中。 它還記錄在特定于應用程式的錯誤日誌中。

下面是一些常見的交換錯誤：

- 對應用程式根的 HTTP 要求是超時的。 交換操作為每個 HTTP 要求等待 90 秒，並重試最多 5 次。 如果所有重試都超時，則交換操作將停止。

- 當應用內容超過為本機快取指定的本地磁片配額時，本機快取初始化可能會失敗。 有關詳細資訊，請參閱[本機快取概述](overview-local-cache.md)。

- 在[自訂預熱](#Warm-up)期間，在內部發出 HTTP 要求（不經過外部 URL）。 在*Web.config*中的某些 URL 重寫規則中，它們可能會失敗。例如，重定向功能變數名稱或強制 HTTPS 的規則可以防止預熱請求到達應用代碼。 要解決此問題，請通過添加以下兩個條件來修改重寫規則：

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 如果沒有自訂預熱，URL 重寫規則仍然可以阻止 HTTP 要求。 要解決此問題，請通過添加以下條件修改重寫規則：

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 某些[IP 限制規則](app-service-ip-restrictions.md)可能會阻止交換操作向應用發送 HTTP 要求。 IPv4 位址範圍以部署`10.`開頭`100.`且是部署的內部。 您應該允許他們連接到你的應用。

- 插槽交換後，應用可能會遇到意外的重新開機。 這是因為在交換後，主機名稱綁定配置不同步，這本身不會導致重新開機。 但是，某些基礎存儲事件（如存儲卷容錯移轉）可能會檢測這些差異，並強制所有輔助進程重新開機。 要最小化這些類型的重新開機，請*在所有插槽*上設置[`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1`應用設置](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig)。 但是，此應用*設置不適用於*Windows 通信基礎 （WCF） 應用。

## <a name="next-steps"></a>後續步驟
[封鎖對非生產位置的存取](app-service-ip-restrictions.md)
