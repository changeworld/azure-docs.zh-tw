---
title: 設定預備環境
description: 瞭解如何將應用程式部署到非生產位置，以及進行進入生產環境。 增加可靠性並消除部署中的應用程式停機時間。
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300838"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>在 Azure App Service 中設定預備環境
<a name="Overview"></a>

當您將 web 應用程式、Linux 上的 web 應用程式、行動後端或 API 應用程式部署到[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)時，您可以在 [**標準**]、[ **Premium**] 或 [**隔離**式] App Service 方案層中執行時，使用個別的部署位置，而不是預設的生產位置。 部署位置是具有專屬主機名稱的即時應用程式。 兩個部署位置 (包括生產位置) 之間的應用程式內容與設定項目可以互相交換。 

將應用程式部署至非生產位置具有下列優點：

* 您可以先驗證預備部署位置中的應用程式變更，再將它與生產位置進行交換。
* 先將應用程式部署至某個位置，然後再將它交換到生產位置，可確保該位置的所有執行個體在交換到生產位置之前都已準備就緒。 這麼做可以排除部署應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 當不需要預先交換驗證時，您可以設定[自動交換](#Auto-Swap)，以自動化整個工作流程。
* 交換之後，先前具有預備應用程式的位置，現在已經有之前的生產應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以恢復「上一個已知的良好網站」。

每個 App Service 方案層所支援的部署位置個數都不一樣。 使用部署位置不需要額外付費。 若要找出應用程式層支援的位置數目，請參閱[App Service 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。 

若要將您的應用程式調整為不同的層級，請確定目標層支援您的應用程式已使用的位置數目。 例如，如果您的應用程式有五個以上的位置，您就無法將其向下調整為**標準**層，因為**標準**層只支援五個部署位置。 

<a name="Add"></a>

## <a name="add-a-slot"></a>新增位置
應用程式必須在 [標準]、[進階] 或 [隔離] 層中執行，您才能啟用多個部署位置。


1. 在  [Azure 入口網站](https://portal.azure.com/)中，搜尋並選取 **應用程式服務**，然後選取您的應用程式。 
   
    ![搜尋應用程式服務](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. 在左窗格中，選取 [**部署**位置] > [**新增**位置]。
   
    ![新增部署位置](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果應用程式還不在**標準**、 **Premium**或**隔離**層中，您會收到一則訊息，指出啟用分段發行的支援層。 此時，您可以選擇選取 [**升級**]，然後移至應用程式的 [**調整規模**] 索引標籤，再繼續進行。
   > 

3. 在 [**新增**位置] 對話方塊中，提供位置名稱，然後選取是否要從另一個部署位置複製應用程式設定。 選取 [**新增**] 以繼續。
   
    ![設定來源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    您可以從任何現有的位置複製設定。 可複製的設定包括應用程式設定、連接字串、語言架構版本、Web 通訊端、HTTP 版本和平台位元。

4. 新增位置之後，請選取 [**關閉**] 以關閉對話方塊。 新位置現在會顯示在 [**部署**位置] 頁面上。 根據預設，新位置的**流量%** 會設定為0，而所有客戶流量則會路由傳送至生產位置。

5. 選取新的部署位置，以開啟該位置的資源頁面。
   
    ![部署位置標題](./media/web-sites-staged-publishing/StagingTitle.png)

    預備位置和任何其他 App Service 應用程式一樣，具有管理頁面。 您可以變更位置的組態。 頁面頂端會顯示位置的名稱，提醒您正在檢視部署位置。

6. 在位置的資源頁面上選取應用程式 URL。 部署位置有自己的主機名稱，而且也是即時應用程式。 若要限制對部署位置的公用存取，請參閱[AZURE APP SERVICE IP 限制](app-service-ip-restrictions.md)。

新的部署位置中沒有任何內容，即使您複製其他位置的設定，仍是如此。 例如，您可以[使用 Git 發行到此](app-service-deploy-local-git.md)位置。 您可以從不同的存放庫分支或不同的存放庫部署至位置。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>交換期間會發生什麼事

### <a name="swap-operation-steps"></a>交換操作步驟

當您將兩個位置（通常是從臨時區域移至生產位置）交換時，App Service 會執行下列動作，以確保目標插槽不會遇到停機時間：

1. 將下列設定從目標位置（例如，生產位置）套用至來源位置的所有實例： 
    - 位置[特定的](#which-settings-are-swapped)應用程式設定和連接字串（如果適用的話）。
    - [持續部署](deploy-continuous-deployment.md)設定（若已啟用）。
    - [App Service 驗證](overview-authentication-authorization.md)設定（若已啟用）。
    
    其中任何一種情況都會觸發來源位置中的所有實例重新開機。 在[使用預覽交換](#Multi-Phase)期間，這會標示第一個階段的結尾。 交換作業已暫停，您可以使用目標位置的設定來驗證來源位置是否正常運作。

1. 等候來源位置中的每個實例完成其重新開機。 如果有任何實例無法重新開機，則交換作業會將所有變更還原到來源位置，並停止作業。

1. 如果已啟用[本機](overview-local-cache.md)快取，請在來源位置的每個實例上對應用程式根目錄（"/"）提出 HTTP 要求，以觸發本機快取初始化。 等待每個實例傳回任何 HTTP 回應。 本機快取初始化會導致在每個實例上重新開機一次。

1. 如果使用[自訂](#Warm-up)準備啟用[自動交換](#Auto-Swap)，則會對來源位置的每個實例上的應用程式根目錄（"/"）提出 HTTP 要求，以觸發[應用程式初始](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)。

    如果未指定 `applicationInitialization`，請在每個實例上對來源位置的應用程式根目錄觸發 HTTP 要求。 
    
    如果實例傳回任何 HTTP 回應，則會被視為準備就緒。

1. 如果來源位置上的所有實例都已成功準備就緒，請切換兩個位置的路由規則來交換兩個插槽。 在此步驟之後，目標位置（例如，生產位置）會有先前在來源插槽中準備就緒的應用程式。

1. 現在，來源位置已有先前在目標插槽中的預先交換應用程式，請套用所有設定並重新啟動實例，以執行相同的操作。

在交換作業的任何時間點，初始化交換應用程式的所有工作都會在來源位置上發生。 當來源位置正在準備並準備就緒時，不論交換成功或失敗的位置為何，目標位置都會維持上線狀態。 若要交換預備位置與生產位置，請確定生產位置一定是目標位置。 如此一來，交換作業就不會影響您的生產應用程式。

### <a name="which-settings-are-swapped"></a>哪些設定已交換？

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

若要將應用程式設定或連接字串設定為停留在特定位置（未交換），請移**至該位置的 [設定**] 頁面。 新增或編輯設定，然後選取 [**部署位置設定**]。 選取此核取方塊會告訴 App Service 該設定不是可交換的。 

![位置設定](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交換兩個位置 
您可以在應用程式的 [**部署**位置] 頁面和 [**總覽**] 頁面上交換部署位置。 如需位置交換的技術詳細資料，請參閱[交換期間發生的狀況](#AboutConfiguration)。

> [!IMPORTANT]
> 在您將應用程式從部署位置交換到生產環境之前，請確定生產環境是您的目標位置，而且來源位置中的所有設定都已完全依照您想要在生產環境中擁有的方式設定。
> 
> 

若要交換部署位置：

1. 移至您應用程式的 [**部署**位置] 頁面，然後選取 [**交換**]。
   
    ![交換按鈕](./media/web-sites-staged-publishing/SwapButtonBar.png)

    [**交換**] 對話方塊會顯示所選取來源和目標位置中將變更的設定。

2. 選取所需的 [來源] 和 [目標] 位置。 目標通常是生產位置。 此外，選取 [**來源變更**] 和 [**目標變更**] 索引標籤，並確認是否需要設定變更。 當您完成時，您可以藉由選取 [**交換**] 來立即交換位置。

    ![完整的交換](./media/web-sites-staged-publishing/SwapImmediately.png)

    若要查看您的目標位置如何在交換實際發生之前，以新的設定執行，請不要選取 [**交換**]，但請遵循[與預覽交換](#Multi-Phase)中的指示進行。

3. 當您完成時，請選取 [**關閉**] 來關閉對話方塊。

如果您有任何問題，請參閱針對[交換進行疑難排解](#troubleshoot-swaps)。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>使用預覽交換 (多階段交換)

在您交換至生產環境作為目標位置之前，請先驗證應用程式是否以交換的設定執行。 來源位置也會在交換完成之前準備就緒，這是任務關鍵性應用程式的理想做法。

當您執行使用預覽交換時，App Service 會執行相同的[交換](#AboutConfiguration)作業，但在第一個步驟之後暫停。 接著，您可以在完成交換之前，先在預備位置上驗證結果。 

如果您取消交換，App Service 將設定元素重新套用至來源位置。

若要使用預覽交換：

1. 遵循[交換部署](#Swap)位置中的步驟，但選取 [**執行與預覽交換**]。

    ![使用預覽交換](./media/web-sites-staged-publishing/SwapWithPreview.png)

    此對話方塊會顯示來源位置中的設定在階段1中的變更，以及來源和目標位置在第2階段中的變更方式。

2. 當您準備好要開始交換時，請選取 [**開始交換**]。

    當第1階段完成時，您會在對話方塊中收到通知。 前往 [`https://<app_name>-<source-slot-name>.azurewebsites.net`]，預覽來源位置中的交換。 

3. 當您準備好要完成暫止的交換時，請選取 [在**交換動作**中**完成交換**]，然後選取 [**完成交換**]。

    若要解除擱置中的交換，請改為選取 [**取消交換**]。

4. 當您完成時，請選取 [**關閉**] 來關閉對話方塊。

如果您有任何問題，請參閱針對[交換進行疑難排解](#troubleshoot-swaps)。

若要自動執行多階段交換，請參閱[使用 PowerShell 自動執行](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>復原交換
在交換位置後，若目標位置 (例如生產位置) 發生任何錯誤，請立即交換相同的兩個位置，將位置還原成交換前的狀態。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>設定自動交換

> [!NOTE]
> Linux 上的 web 應用程式不支援自動交換。

自動交換可簡化 Azure DevOps 案例，您想要在不冷啟動的情況下持續部署應用程式，並讓應用程式的客戶零停機。 從位置啟用自動交換至生產環境時，每次您將程式碼變更推送至該位置時，App Service 會在來源位置中準備就緒應用程式之後，自動[將它交換至生產環境](#swap-operation-steps)。

   > [!NOTE]
   > 在您為生產位置設定自動交換之前，請考慮在非生產目標位置上測試自動交換。
   > 

若要設定自動交換：

1. 移至您應用程式的資源頁面。 選取 **部署**位置 >  *\<所需的來源*位置，> > 設定 >  **一般設定**。
   
2. 若**已啟用自動交換**，請選取 [**開啟**]。 然後選取所需的 [**自動交換部署**位置] 目標位置，然後在命令列上選取 [**儲存**]。 
   
    ![設定自動交換的選項](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 執行推送至來源位置的程式碼。 自動交換會在短時間後發生，而且更新會反映在您的目標位置的 URL。

如果您有任何問題，請參閱針對[交換進行疑難排解](#troubleshoot-swaps)。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>指定自訂的暖開機

某些應用程式在交換之前可能需要自訂的準備動作。 Web.config 中的 `applicationInitialization` configuration 元素可讓您指定自訂的初始化動作。 [交換](#AboutConfiguration)作業會等候此自訂準備完成，然後再與目標位置交換。 以下是範例 web.config 片段。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

如需自訂 `applicationInitialization` 元素的詳細資訊，請參閱[最常見的部署位置交換失敗和修正方法](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)。

您也可以使用下列其中一個或兩個[應用程式設定](configure-common.md)來自訂暖開機行為：

- `WEBSITE_SWAP_WARMUP_PING_PATH`：用來讓您的網站進行 ping 的路徑。 請指定開頭為斜線的自訂路徑作為值，以新增此應用程式設定。 例如 `/statuscheck`。 預設值是 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`：準備操作的有效 HTTP 回應碼。 請以 HTTP 代碼的逗號分隔清單方式新增此應用程式設定。 `200,202` 的範例。 如果傳回的狀態碼不在清單中，則會停止準備和交換作業。 預設是所有回應碼都有效。

> [!NOTE]
> `<applicationInitialization>` 設定元素是每個應用程式啟動的一部分，而兩個準備行為應用程式設定只適用于位置交換。

如果您有任何問題，請參閱針對[交換進行疑難排解](#troubleshoot-swaps)。

## <a name="monitor-a-swap"></a>監視交換

如果[交換](#AboutConfiguration)作業需要很長的時間才能完成，您可以在 [[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)] 中取得交換操作的相關資訊。

在入口網站的應用程式資源頁面上，選取左窗格中的 [**活動記錄**]。

交換作業在記錄查詢中會顯示為 `Swap Web App Slots`。 您可以展開它，然後選取其中一個子作業或錯誤，以查看詳細資料。

## <a name="route-traffic"></a>路由流量

根據預設，對應用程式生產 URL (`http://<app_name>.azurewebsites.net`) 的所有用戶端要求都會路由至生產位置。 您可以將部分流量路由至其他位置。 如果您需要使用者針對新的更新提供意見反應，但尚未準備好要將更新發行至生產環境，這項功能將有其效用。

### <a name="route-production-traffic-automatically"></a>自動路由生產流量

若要自動路由生產流量：

1. 移至您應用程式的資源頁面，然後選取 [**部署**位置]。

2. 針對您要路由到的位置，在其 [流量百分比] 資料行中指定百分比 (介於 0 到 100 之間)，以代表您要路由的總流量。 選取 [儲存]。

    ![設定流量百分比](./media/web-sites-staged-publishing/RouteTraffic.png)

儲存設定之後，會隨機將指定百分比的用戶端路由傳送到非生產位置。 

用戶端自動路由至特定位置之後，就會在該位置「釘選」在該用戶端會話的生命週期內。 用戶端瀏覽器中，您可以查看 HTTP 標頭中的 `x-ms-routing-name` Cookie，以確認您的工作階段固定到哪個位置。 路由至「預備」位置的要求具有 Cookie `x-ms-routing-name=staging`。 路由至生產位置的要求具有 Cookie `x-ms-routing-name=self`。

   > [!NOTE]
   > 在 [Azure 入口網站] 旁，您也可以使用 Azure CLI 中的 [ [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) ] 命令，從 DevOps 管線或其他自動化系統等 CI/CD 工具設定路由百分比。
   > 

### <a name="route-production-traffic-manually"></a>手動路由生產流量

除了自動流量路由以外，App Service 也可以將要求路由至特定位置。 當您想要讓使用者能夠加入宣告或退出您的 Beta 應用程式時，這會很有用。 若要手動路由生產流量，您可以使用 `x-ms-routing-name` 查詢參數。

例如，若要讓使用者退出宣告您的 Beta 應用程式，您可以將此連結放在您的網頁上：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字串 `x-ms-routing-name=self` 會指定生產位置。 在用戶端瀏覽器存取連結之後，它會重新導向至生產位置。 每個後續要求都有 `x-ms-routing-name=self` cookie，可將會話釘選到生產位置。

若要讓使用者加入宣告您的 Beta 應用程式，請將相同的查詢參數設定為非生產位置的名稱。 以下是範例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

根據預設，新的位置會提供 `0%`的路由規則，以灰色顯示。 當您明確地將此值設定為 `0%` （以黑色文字顯示）時，您的使用者可以使用 `x-ms-routing-name` 查詢參數，以手動方式存取預備位置。 但因為路由百分比設定為0，所以不會自動將它們路由傳送至位置。 這是一個先進的案例，您可以從公用「隱藏」您的預備位置，同時允許內部團隊測試位置上的變更。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>刪除位置

搜尋並選取您的應用程式。 選取**部署**位置 >  *\<位置以刪除 >*  > **總覽**。 選取命令列上的 [**刪除**]。  

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
### <a name="create-a-slot"></a>建立位置
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>使用預覽起始交換（多階段交換），並將目的地位置設定套用至來源位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>解除擱置中的交換（使用審核交換），並還原來源位置設定
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交換部署位置
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>監視活動記錄中的交換事件
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>刪除位置
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>使用 Resource Manager 範本進行自動化

[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview)是用來自動化 Azure 資源部署和設定的宣告式 JSON 檔案。 若要使用 Resource Manager 範本交換位置，您會在*Microsoft web/sites/* 位置和*microsoft web/sites*資源上設定兩個屬性：

- `buildVersion`：這是字串屬性，代表在位置中部署之應用程式的目前版本。 例如： "v1"、"1.0.0.1" 或 "2019-09-20T11：53： 25.2887393-07： 00"。
- `targetBuildVersion`：這是一個字串屬性，可指定位置 `buildVersion` 應包含的內容。 如果 targetBuildVersion 不等於目前的 `buildVersion`，就會藉由尋找具有指定 `buildVersion`的位置來觸發交換操作。

### <a name="example-resource-manager-template"></a>範例 Resource Manager 範本

下列 Resource Manager 範本會更新預備位置的 `buildVersion`，並在生產位置上設定 `targetBuildVersion`。 這會交換兩個位置。 此範本假設您已經使用名為「預備」的位置建立 webapp。

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

此 Resource Manager 範本具有等冪性，這表示它可以重複執行，並產生相同的位置狀態。 第一次執行之後，`targetBuildVersion` 會符合目前的 `buildVersion`，因此不會觸發交換。

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>使用 CLI 自動化

如需適用於部署位置的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，請參閱 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>針對交換進行疑難排解

如果在位置[交換](#AboutConfiguration)期間發生任何錯誤，則會記錄在*D:\home\LogFiles\eventlog.xml*中。 它也會記錄在應用程式特定的錯誤記錄檔中。

以下是一些常見的交換錯誤：

- 應用程式根目錄的 HTTP 要求已計時。 交換作業會等待每個 HTTP 要求90秒，然後重試最多5次。 如果所有重試都已超時，則會停止交換作業。

- 當應用程式內容超過為本機快取指定的本機磁片配額時，本機快取初始化可能會失敗。 如需詳細資訊，請參閱本機快取[總覽](overview-local-cache.md)。

- 在[自訂](#Warm-up)準備期間，HTTP 要求會在內部進行（而不會經過外部 URL）。 它們可能會*因 web.config 中*的某些 URL 重寫規則而失敗。例如，重新導向功能變數名稱或強制執行 HTTPS 的規則，可能會讓準備要求無法到達應用程式的程式碼。 若要解決此問題，請新增下列兩個條件來修改您的重寫規則：

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 如果沒有自訂的準備，URL 重寫規則仍然可以封鎖 HTTP 要求。 若要解決此問題，請新增下列條件來修改您的重寫規則：

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 某些[IP 限制規則](app-service-ip-restrictions.md)可能會讓交換作業無法將 HTTP 要求傳送至您的應用程式。 開頭為 `10.` 和 `100.` 的 IPv4 位址範圍是您的部署內部。 您應該允許它們連接到您的應用程式。

- 在位置交換之後，應用程式可能會遇到非預期的重新開機。 這是因為在交換之後，主機名稱系結設定會不同步，而這本身不會造成重新開機。 不過，某些基礎儲存體事件（例如存放磁片區容錯移轉）可能會偵測到這些不一致的情況，並強制重新開機所有工作者進程。 若要將這些重新開機類型降到最低，請在*所有*位置上設定[`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` 應用程式](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig)設定。 不過，此應用程式設定*無法*與 WINDOWS COMMUNICATION FOUNDATION （WCF）應用程式搭配使用。

## <a name="next-steps"></a>後續步驟
[封鎖對非生產位置的存取](app-service-ip-restrictions.md)
