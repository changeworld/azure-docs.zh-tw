---
title: 快速入門：建立 Front Door 設定檔以達到應用程式的高可用性
description: 本快速入門文章將說明如何建立 Front Door，以供高可用性且高效能的全域 Web 應用程式使用。
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521458"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>快速入門：為高可用性的全球 Web 應用程式建立 Front Door

本快速入門將說明如何建立 Front Door 設定檔，以便為您的全域 Web 應用程式提供高可用性和高效能。 

本快速入門所述的案例包含兩個在不同 Azure 區域中執行的 Web 應用程式執行個體。 根據相等的[加權和相同優先順序的後端](front-door-routing-methods.md)來建立 Front Door 設定，有助於將使用者流量導向至正在執行應用程式的網站後端中最接近的一組。 Front Door 會持續監視 Web 應用程式，並在最接近的網站無法使用時，自動容錯移轉至下一個可用的後端。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure 
在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="prerequisites"></a>Prerequisites
要進行本快速入門，您必須部署兩個在不同 Azure 區域 (美國東部  和西歐  ) 中執行的 Web 應用程式執行個體。 這兩個 Web 應用程式執行個體都會以主動/主動模式執行，也就是，不同於主動/待命設定會將其中一個用來作為容錯移轉，它們其中任一個隨時都能接受流量。

1. 在畫面左上方，選取 [建立資源]   > [Web]   > [Web 應用程式]  。
2. 在 [Web 應用程式]  中，輸入或選取下列資訊，然後在未指定任何值的地方輸入預設設定：

     | 設定         | 值     |
     | ---              | ---  |
     | 資源群組          | 選取 [新增]  ，然後輸入 *myResourceGroupFD1* |
     | 名稱           | 輸入 Web 應用程式的唯一名稱  |
     | 執行階段堆疊          | 選取應用程式執行階段堆疊 |
     |      區域  |   美國西部        |
     | App Service 方案/位置         | 選取 [ **新增**]。  在 App Service 方案中，輸入「myAppServicePlanEastUS」  ，然後選取 [確定]  。| 
     |SKU 和大小  | 選取 [變更大小]  選取 [標準 S1 100 總 ACU，1.75 GB 記憶體]  |
     
3. 選取 [檢閱 + 建立]  。
4. 檢閱 Web 應用程式摘要資訊。 選取 [建立]  。
5. 大約 5 分鐘之後，在成功部署 Web 應用程式時，預設網站便已建立完成。
6. 使用下列設定重複步驟 1 至 3，在不同 Azure 區域中建立第二個網站：

     | 設定         | 值     |
     | ---              | ---  |
     | 資源群組          | 選取 [新增]  ，然後輸入 *myResourceGroupFD2* |
     | 名稱           | 輸入 Web 應用程式的唯一名稱  |
     | 執行階段堆疊          | 選取應用程式執行階段堆疊 |
     |      區域  |   西歐      |
     | App Service 方案/位置         | 選取 [ **新增**]。  在 App Service 方案中，輸入「myAppServicePlanWestEurope」  ，然後選取 [確定]  。|   
     |SKU 和大小  | 選取 [變更大小]  選取 [標準 S1 100 總 ACU，1.75 GB 記憶體]  |
    
## <a name="create-a-front-door-for-your-application"></a>為您的應用程式建立 Front Door
### <a name="a-add-a-frontend-host-for-front-door"></a>A. 新增適用於 Front Door 的前端主機
建立 Front Door 設定，根據兩個後端之間的最低延遲來引導使用者流量。

1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [Front Door]  。
2. 在 [建立 Front Door]  中，輸入或選取下列資訊，然後在未指定任何值的地方輸入預設設定：

     | 設定         | 值     |
     | ---              | ---  |
     |訂用帳戶  | 選取您要在其中建立 Front Door 的訂用帳戶。|
     | 資源群組          | 選取 [新增]  ，然後輸入 myResourceGroupFD0  |
     | 資源群組位置  |   美國中部        |
     
     > [!NOTE]
     > 您不需要建立新的資源群組，即可部署 Front Door。  您也可以選取現有的資源群組。
     
3. 按一下 **[下一步**組態]。
4. 按一下前端/網域卡片上的 [+] 圖示。  在 [主機名稱]  中輸入 `<Your Initials>frontend`。 此主機名稱必須是全域唯一的，Front Door 會負責驗證。
5. 按一下 [新增]  。

### <a name="b-add-application-backend-and-backend-pools"></a>B. 新增應用程式後端和後端集區

接下來，您需要在後端集區中設定前端/和應用程式後端，讓 Front Door 知道您應用程式的所在位置。 

1. 按一下後端集區卡片上的 [+] 圖示，為您的後端集區新增**名稱**，然後輸入 `myBackendPool`。
2. 接下來，按一下 [新增後端]  以新增您稍早建立的網站。
3. 選取 [後端主機類型]  作為「應用程式服務」、選取您用來建立網站的訂用帳戶，然後從 [後端主機名稱]  下拉式清單中選擇第一個網站。
4. 目前依現狀保留其餘欄位，然後按一下 [新增]  。
5. 選取 [後端主機類型]  作為「應用程式服務」、選取您用來建立網站的訂用帳戶，然後從 [後端主機名稱]  下拉式清單選擇中第**二**個網站。
6. 目前依現狀保留其餘欄位，然後按一下 [新增]  。
7. 您可以選擇性地選擇更新後端集區的健康情況探查和負載平衡設定，但預設值應該也能運作。 在任一種情況下，按一下 [新增]  。


### <a name="c-add-a-routing-rule"></a>C. 新增路由規則
1. 最後，按一下路由規則卡上的 [+] 圖示以設定路由規則。 這需要將您的前端主機對應到後端集區，如果要求會傳入 `myappfrontend.azurefd.net`，基本上就需要設定此對應，然後再將它轉送到後端集區 `myBackendPool`。 
2. 在 [名稱]  中輸入 'LocationRule'。
3. 按一下 [新增]  ，為您的 Front Door 新增路由規則。 
4. 按一下 [檢閱及建立]  。
5. 檢閱 Front Door 建立的設定。 按一下 [建立] 

>[!WARNING]
> 您**必須**確保 Front Door 中的每部前端主機都具有一個路由規則，其中含有與其相關聯的預設路徑 ('/\*')。 也就是說，在您的所有路由規則中，至少必須有一個適用於您每個前端主機的路由規則，這類主機會以預設路徑 ('/\*') 來定義。 無法執行這項操作，可能導致您的使用者流量無法被正確路由傳送。

## <a name="view-front-door-in-action"></a>檢視動作中的 Front Door
一旦建立 Front Door 之後，將需要幾分鐘的時間，才能在世界各地部署該設定。 完成後，存取您所建立的前端主機，也就是前往網頁瀏覽器並點擊 URL `myappfrontend.azurefd.net`。 您的要求將會從後端集區中指定的後端，自動路由傳送到最接近您的後端。 

### <a name="view-front-door-handle-application-failover"></a>檢視 Front Door 處理應用程式容錯移轉
如果您想要測試 Front Door 動作中的即時全域容錯移轉，可以前往您所建立的其中一個網站並停止它。 根據針對後端集區所定義的健康情況探查設定，我們會立即將流量容錯移轉至另一個網站部署。 您也可以藉由在 Front Door 的後端集區設定中停用後端來測試行為。 

## <a name="clean-up-resources"></a>清除資源
若不再需要，請刪除 **myResourceGroupFD1**、**myResourceGroupFD2** 和 **myResourceGroupFD0** 資源群組：

## <a name="next-steps"></a>後續步驟
在本快速入門中，您建立了 Front Door，讓您能夠針對需要高可用性和最大效能的 Web 應用程式引導使用者流量。 若要深入了解如何路由傳送流量，請閱讀 Front Door 所使用的[路由方法](front-door-routing-methods.md)。
