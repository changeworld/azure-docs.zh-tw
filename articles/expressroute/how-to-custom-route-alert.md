---
title: ExpressRoute：如何設定公告路由的自訂警示
description: 本文說明如何使用 Azure 自動化和 Logic Apps 來監視從 ExpressRoute 閘道公告至內部部署網路的路由數目，以避免達到200路由限制。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: cherylmc
ms.openlocfilehash: 42f416cf6f297eb54298a10162e7ba28f7acd1bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738476"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>設定自訂警示以監視已公告路由

本文可協助您使用 Azure 自動化和 Logic Apps，持續監視從 ExpressRoute 閘道公告至內部部署網路的路由數目。 監視有助於防止達到[200 路由限制](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)。

**Azure 自動化**可讓您自動執行儲存在*runbook*中的自訂 PowerShell 腳本。 當您使用本文中的設定時，runbook 會包含可查詢一或多個 ExpressRoute 閘道的 PowerShell 腳本。 它會收集一個資料集，其中包含資源群組、ExpressRoute 閘道名稱，以及在內部部署中通告的網路首碼數目。

**Azure Logic Apps**會排程呼叫 Azure 自動化 runbook 的自訂工作流程。 Runbook 的執行是使用作業來完成。 執行資料收集之後，Azure Logic Apps 工作流程會根據高於或低於預先定義閾值的網路首碼數，將資料分類，並將資訊傳送至目的地電子郵件地址。

### <a name="workflow"></a><a name="workflow"></a>工作流程

設定自訂警示是以三個主要步驟為基礎：

1. 建立具有「執行身分」帳戶和許可權的自動化帳戶。

2. 建立和設定 runbook。

3. 建立將引發自動化帳戶的邏輯應用程式，並在此數目大於閾值時傳送警示電子郵件（例如，160）。

## <a name="before-you-begin"></a><a name="before"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 您的部署中至少有一個 ExpressRoute 閘道。

* 您對 Azure 自動化中的[執行身分帳戶](../automation/manage-runas-account.md)有基本瞭解。

* 您已熟悉[Azure Logic Apps](../logic-apps/logic-apps-overview.md)。

* 您已熟悉如何使用 Azure PowerShell。 需要 Azure PowerShell 才能收集 ExpressRoute 閘道中的網路首碼。 如需 Azure PowerShell 的一般詳細資訊，請參閱[Azure PowerShell 檔](https://docs.microsoft.com/powershell/azure/?view=azps-4.1.0)。

### <a name="notes-and-limitations"></a><a name="limitations"></a>注意事項和限制

* 本文所討論的自訂警示是附加元件，可達成更佳的操作和控制。 它不會取代 ExpressRoute 中的原生警示。
* ExpressRoute 閘道的資料收集會在背景中執行。 執行時間可能會比預期的長。 若要避免工作佇列，必須正確設定工作流程週期。
* 腳本或 ARM 範本的部署速度可能會比自訂警示觸發程式更快。 這可能會導致 ExpressRoute 閘道中的網路首碼數目增加超過200個路由的限制。

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>建立和設定帳戶

當您在 Azure 入口網站中建立自動化帳戶時，系統會自動建立[執行](../automation/manage-runas-account.md#types-of-run-as-accounts)身分帳戶。 此帳戶會採取下列動作：

* 建立具有自我簽署憑證的 Azure Active Directory （Azure AD）應用程式。 執行身分帳戶本身具有每年都必須更新的憑證。

* 在 Azure AD 中建立應用程式的服務主體帳戶。

* 在使用中的 Azure 訂用帳戶上，將參與者角色（RBAC）指派給自己。 此角色會使用 runbook 來管理 Azure Resource Manager 資源。

為了建立自動化帳戶，您需要許可權和許可權。 如需相關資訊，請參閱[建立自動化帳戶所需的許可權](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)。

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. 建立自動化帳戶

建立具有「執行身分」許可權的自動化帳戶。 如需指示，請參閱[建立 Azure 自動化帳戶](../automation/automation-quickstart-create-account.md)。

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="新增自動化帳戶" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. 指派角色給執行身分帳戶

根據預設，**參與者**角色會指派給您的**執行**身分帳戶所使用的服務主體。 您可以保留指派給服務主體的預設角色，也可以藉由指派[內建角色](../role-based-access-control/built-in-roles.md)（例如，讀取者）或[自訂角色](../active-directory/users-groups-roles/roles-create-custom.md)來限制許可權。

 使用下列步驟來判斷指派給執行身分帳戶所使用之服務主體的角色：

1. 瀏覽至您的自動化帳戶。 流覽至 [**帳戶設定**]，然後選取 [**執行身分帳戶**]。

2. 選取 [**角色**] 以查看正在使用的角色定義。

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="指派角色":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>建立和設定 runbook

### <a name="1-install-modules"></a><a name="install-modules"></a>1. 安裝模組

為了在 Azure 自動化 runbook 中執行 PowerShell Cmdlet，您必須安裝一些額外的 Azure PowerShell Az 模組。 使用下列步驟來安裝模組：

1. 開啟您的 Azure 自動化帳戶，然後流覽至 [**模組**]。

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="流覽至模組":::

2. 搜尋資源庫並匯入下列模組： **az. Accounts**、 **az. Network**、 **Az. Automation**和**Az. Profile**。

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="搜尋和匯入模組" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. 建立 runbook

1. 若要建立 PowerShell runbook，請流覽至您的自動化帳戶。 在 [**進程自動化**] 底下，選取 [ **runbook** ] 圖格，然後選取 [**建立 runbook**]。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="建立 runbook。":::

2. 選取 [**建立**] 以建立 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="選取 [建立]。":::

3. 選取新建立的 runbook，然後選取 [**編輯**]。

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="編輯 runbook":::

4. 在 [**編輯**] 中，貼上 PowerShell 腳本。 您可以修改[範例腳本](#script)，並使用它來監視一或多個資源群組中的 ExpressRoute 閘道。

   在範例腳本中，請注意下列設定：

    * 陣列 **$rgList**包含具有 ExpressRoute 閘道的資源群組清單。 您可以自訂以清單為基礎的 ExpressRoute 閘道。
    * 變數 **$thresholdNumRoutes**定義從 ExpressRoute 閘道公告至內部部署網路的網路首碼數目閾值。

#### <a name="example-script"></a><a name="script"></a>範例指令碼

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. 儲存併發布 runbook

1. 選取 [**儲存**] 以儲存 runbook 的草稿複本。
2. 選取 [**發佈**]，將 runbook 發佈為自動化帳戶中的官方 runbook 版本。

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="儲存併發布 runbook。":::

當您執行 PowerShell 腳本時，系統會收集值清單：
 
* 資源群組

* ExpressRoute 閘道名稱

* 第一個 BGP 對等體的 IP 位址（peer1）

* 第二個 BGP 對等體的 IP 位址（peer2）

* 從 ExpressRoute 閘道向第一個 BGP 對等互連（peer1）通告的網路首碼數

* 從 ExpressRoute 閘道向第二個 BGP 對等互連（peer2）通告的網路首碼數

* 時間戳記

* 狀態，分類為：

  * 如果路由數目小於臨界值，則為「確定」
  * 如果路由數目超過閾值，則為「警示」
  * 如果向兩個 BGP 對等通告的網路首碼數目不同，則為 ' WARNING '

* 警示訊息，以取得狀態的詳細描述（[確定]、[警示]、[警告]）

PowerShell 腳本會將收集到的資訊轉換為 JSON 輸出。 Runbook 會使用 PowerShell Cmdlet[寫入輸出](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Write-Output?)作為輸出串流，將資訊傳達給用戶端。

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. 驗證 runbook

建立 runbook 之後，就必須進行驗證。 選取 [**開始**]，然後檢查不同作業資料流程的輸出和錯誤。

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="驗證 runbook" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>建立並設定邏輯應用程式

Azure Logic Apps 是集合和動作之所有進程的協調器。 在下列各節中，您會使用邏輯應用程式來建立工作流程。

### <a name="workflow"></a>工作流程

在此工作流程中，您會建立一個可定期監視 ExpressRoute 閘道的邏輯應用程式。 如果存在新的項目，邏輯應用程式會針對每個項目傳送電子郵件。 當您完成時，邏輯應用程式大致如下列工作流程所示︰

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps 工作流程":::

### <a name="1-create-a-logic-app"></a>1. 建立邏輯應用程式

在**邏輯應用程式設計**工具中，使用空白的**邏輯應用程式**範本來建立邏輯應用程式。 如需相關步驟，請參閱[Create Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)。

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="空白範本":::

### <a name="2-add-a-trigger"></a>2. 新增觸發程式

觸發程式會啟動每個邏輯應用程式。 觸發程式會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Azure Logic Apps 引擎都會建立一個可啟動並執行您工作流程的邏輯應用程式執行個體。

若要定期執行以預先定義的時間排程為基礎的邏輯應用程式，請將內建的**週期：排程**加入至您的工作流程。 在搜尋方塊中，輸入**schedule**。 選取 [觸發程序]。 從觸發程式清單中，選取 **[週期排程**]。

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="週期：排程":::

在 [迴圈排程] 觸發程式中，您可以設定 [時區] 和 [週期]，以重複該工作流程。 時間間隔和頻率可一起為邏輯應用程式的觸發程序定義排程。 若要建立合理的最小週期頻率，請考慮下列因素：

* 自動化 runbook 中的 PowerShell 腳本需要一些時間才能完成。 執行時間取決於要監視的 ExpressRoute 閘道數目。 迴圈頻率過短會導致工作佇列。

* PowerShell 腳本會以工作的形式在背景中執行。 它不會立即啟動;它會在變數延遲之後執行。

* 過短的週期頻率將會在您的 Azure ExpressRoute 閘道上產生不必要的負載。

在工作流程設定結束時，您可以執行工作流程幾次來檢查迴圈頻率的一致性，然後在**執行歷程記錄**中驗證結果。

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="週期性" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. 建立作業

邏輯應用程式會透過連接器存取其他應用程式、服務和平臺。 此工作流程中的下一個步驟是選取連接器來存取稍早定義的 Azure 自動化帳戶。

1. 在**Logic Apps 設計**工具的 **[週期**] 底下，選取 [**新增步驟**]。 在 **[選擇動作**] 和 [搜尋] 方塊中，選取 [**全部**]。
2. 在搜尋方塊中，輸入**Azure 自動化**和搜尋。 選取 [**建立作業**]。 **建立作業**將用來引發稍早建立的自動化 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="建立作業":::

3. 使用服務主體登入。 您可以使用現有的服務主體，也可以建立一個新的。 若要建立新的服務主體，請參閱[如何使用入口網站建立可存取資源的 Azure AD 服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。 選取 **[使用服務主體連線]**。

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="登入":::

4. 輸入連線**名稱**，並新增您的**用戶端識別碼**（應用程式識別碼）、**用戶端密碼**和您的**租使用者識別碼**。 然後，選取 [Create] \(建立\)。

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="連線至服務主體":::

5. 在 [**建立作業**] 頁面上，服務主體在裝載自動化帳戶的**資源群組**上應具有「讀取者」角色，並在**自動化帳戶**上具有「自動化作業操作員」。 此外，請確認您已新增**Runbook 名稱**做為新的參數。

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="角色" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. 取得作業輸出

1. 選取 [新增步驟]。 搜尋「Azure 自動化」。 從 [**動作**] 清單中，選取 [**取得作業輸出**]。

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="取得作業輸出":::

2. 在 [**取得作業輸出**] 頁面上，指定要存取自動化帳戶的必要資訊。 選取您要使用的 [訂用帳戶] **、[資源群組**] 和 [**自動化帳戶**]。 按一下 [**作業識別碼**] 方塊內部。 **動態內容**清單出現時，請選取 [**作業識別碼**]。

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="作業識別碼" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. 剖析 JSON

[Azure 自動化建立作業動作] （先前步驟）的輸出中所包含的資訊會產生 JSON 物件。 Logic Apps **PARSE json**是一個內建動作，可從屬性及其在 JSON 內容中的值建立易記的權杖。 接著，您可以在工作流程中使用這些屬性。

1. 新增動作。 在 [**取得作業輸出->] 動作**底下，選取 [**新增步驟**]。
2. 在 [**選擇動作**] 搜尋方塊中，輸入 "parse json" 以搜尋提供此動作的連接器。 在 [**動作**] 清單下，針對您要使用的資料作業選取 [**剖析 JSON** ] 動作。

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="剖析 JSON":::

3. 按一下 [**內容**] 方塊內部。 當動態內容清單出現時，選取 [**內容**]。

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="內容" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. 剖析 JSON 需要架構。 您可以使用自動化 runbook 的輸出來產生架構。 開啟新的網頁瀏覽器會話，執行自動化 runbook 並抓取輸出。 返回 [ **Logic Apps 剖析 JSON 資料作業**] 動作。 在頁面底部，選取 [**使用範例承載來產生架構**]。

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="使用範例承載產生架構":::

5. 針對 [**輸入或貼上範例 JSON**承載]，貼上自動化 runbook 的輸出，然後選取 [**完成**]。

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="貼上範例承載" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. 架構是藉由剖析 JSON 輸入裝載而自動產生。

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="產生架構" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. 定義和初始化變數

在工作流程的此步驟中，我們會建立一個條件，以透過電子郵件傳送警示。 若要為電子郵件內文訊息提供彈性的自訂格式，請在工作流程中引進輔助變數。

1. 在 [**取得作業輸出] 動作**底下，選取 [**新增步驟**]。 在搜尋方塊中，尋找並選取 [**變數**]。

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="變數":::

2. 從 [**動作**] 清單中，選取 [**初始化變數**] 動作。

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="初始化變數":::

3. 指定變數的名稱。 針對 [**類型**]，選取 [**字串**]。 稍後在工作流程中會指派變數的**值**。

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="String" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. 建立 "For each" 動作

剖析 JSON 之後， **PARSE Json 資料作業**動作會將內容儲存在*主體*輸出中。 若要處理輸出，您可以建立「For each」迴圈，針對陣列中的每個專案重複一個或多個動作。

1. 在 [**初始化變數**] 底下，選取 [**新增動作**]。 在搜尋方塊中，輸入 "for each" 作為篩選條件。

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="控制":::

2. 從 [**動作**] 清單中，選取**每個控制項**的 [動作]。

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="針對每個控制項":::

3. 按一下 [**選取先前步驟中的輸出**] 文字方塊。 當**動態內容**清單出現時，請選取 [**主體**]，這是已剖析 JSON 的輸出。

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="本文":::

4. 針對 JSON 主體的每個元素，我們想要設定條件。 從 [動作] 群組中，選取 [**控制項**]。

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="控制":::

5. 在 [**動作**] 清單中，選取 [**條件控制**]。 條件控制項的控制結構會比較工作流程中的資料與特定值或欄位。 接著，您可以根據資料是否符合條件，指定要執行的不同動作。

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="條件控制項":::

6. 在 [**條件**的根] 動作中，將邏輯運算變更為**或**。

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="或" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. 檢查 ExpressRoute 閘道向兩個 BGP 對等互連通告的網路首碼數值。 路由的數目可在**動態內容**的 "numRoutePeer1" 和 "numRoutePeer2" 中取得。 在 [值] 方塊中，輸入**numRoutePeer1**的值。

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. 若要在條件中加入另一個資料列，請選擇 [**加入-> 加入資料列**]。 在第二個方塊中，從 [**動態內容**] 選取 [ **numRoutePeer2**]。

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. 當兩個動態變數（numRoute1 或 numRoute2）其中之一大於臨界值時，邏輯條件為 true。 在此範例中，閾值固定為160（最大值為200個路由的80%）。 您可以變更臨界值以符合您的需求。 為求一致，此值應該與 runbook PowerShell 腳本中使用的值相同。

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="邏輯條件":::

10. 在 [**如果為 true**] 底下，格式化並建立動作，以透過電子郵件傳送警示。 在 [選擇動作] 中，搜尋並選取 [**變數**]。

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="若為 true":::

11. 在 [變數] 中，選取 [**新增動作**]。 在 [**動作**] 清單中，選取 [**設定變數**]。

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="設定變數":::

12. 在 [**名稱**] 中，選取您先前建立的名為**EmailBody**的變數。 針對 [**值**]，貼上設定警示電子郵件所需的 HTML 腳本。 使用**動態內容**來包含 JSON 主體的值。 進行這些設定之後，結果會是變數**Emailbody**包含與警示相關的所有資訊（以 HTML 格式表示）。

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="設定變數":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. 新增電子郵件連接器

Logic Apps 提供許多電子郵件連接器。 在此範例中，我們新增了 Outlook 連接器，以透過電子郵件傳送警示。 在 [**設定變數**] 底下，選取 [**新增動作**]。 在 **[選擇動作**] 的 [搜尋] 方塊中，輸入「傳送電子郵件」。

1. 選取 [ **Office 365 Outlook**]。

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="傳送電子郵件":::

2. 在 [**動作**] 清單中，選取 [**傳送電子郵件（V2）**]。

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="傳送電子郵件（V2）":::

3. 登入以建立與 Office 365 Outlook 的連線。

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="登入":::

4. **在 [內**文] 欄位中，按一下 [**新增動態內容**]。 從動態內容面板中，新增變數**Emailbody**。 填寫 **[主旨**] 和 [**到**] 欄位。

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="本文":::

5. [**傳送電子郵件（v2）** ] 動作會完成工作流程設定。

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="傳送電子郵件 v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. 工作流程驗證

最後一個步驟是工作流程驗證。 在**Logic Apps] [總覽**] 中，選取 [**執行觸發**程式]。 選取 **[週期**]。 工作流程可以在**執行歷程記錄**中進行監視和驗證。

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="執行觸發程式":::

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何自訂工作流程，請參閱[Azure Logic Apps](../logic-apps/logic-apps-overview.md)。
