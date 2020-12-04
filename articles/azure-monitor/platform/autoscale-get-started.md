---
title: 開始在 Azure 中自動調整規模
description: 了解如何在 Azure 中調整您的資源 Web 應用程式、雲端服務、虛擬機器或虛擬機器擴展集。
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 364309301b403234936da1bac6e1b74af24c2fdb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573301"
---
# <a name="get-started-with-autoscale-in-azure"></a>開始在 Azure 中自動調整規模
本文說明如何在 Microsoft Azure 入口網站中為您的資源設定自動調整規模。

Azure 監視器自動調整僅適用於[虛擬機器擴展集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[雲端服務](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) 與 [API 管理服務](../../api-management/api-management-key-concepts.md)。

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>在您的訂用帳戶中探索自動調整規模設定

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

您可以在 Azure 監視器中探索適用於自動調整規模的所有資源。 如需逐步說明，請依循下列步驟：

1. 開啟 [Azure 入口網站。][1]
1. 按一下左窗格中的 Azure 監視器圖示。
  ![開啟 Azure 監視器][2]
1. 按一下 [自動調整規模]，檢視適用自動調整規模的所有資源，以及其目前的自動調整狀態。
  ![探索 Azure 監視器中的自動調整規模][3]

您可以使用頂端的篩選窗格來縮小清單範圍，以選取特定資源群組中的資源、特定的資源類型或特定的資源。

針對每個資源，您將會找到目前的執行個體計數及自動調整規模狀態。 自動調整規模狀態可以是：

- **未設定**︰您尚未針對此資源啟用自動調整規模。
- **已啟用**：您已針對此資源啟用自動調整規模。
- **已停用**：您已針對此資源停用自動調整規模。

## <a name="create-your-first-autoscale-setting"></a>建立您的第一個自動調整規模設定

現在讓我們逐步執行一個簡單的逐步解說，以建立您的第一個自動調整規模設定。

1. 開啟 Azure 監視器中的 [自動調整規模] 刀鋒視窗，然後選取您想要調整的資源。 (下列步驟會使用與某個 Web 應用程式相關聯的 App Service 方案。 您可以[在 5 分鐘內，將您的第一個 ASP.NET Web 應用程式建立在 Azure 中][4])
1. 請注意，目前的角色執行個體計數是 1。 按一下 [啟用自動調整規模]。
  ![適用於新 Web 應用程式的調整規模設定][5]
1. 提供調整規模設定的名稱，然後按一下 [新增規則]。 請注意，調整規模規則選項會在右邊開啟為內容窗格。 預設會將此設定為如果資源的 CPU 百分比超過百分之七十，就會將您的執行個體計數相應增加 1。 將其保留為預設值，然後按一下 [新增]。
  ![建立 Web 應用程式的調整規模設定][6]
1. 您現在已建立第一個調整規模規則。 請注意，UX 建議了最佳做法，並指出「建議規則中至少有一個調整規模」。 若要這樣做：

    a. 按一下 [新增規則]。

    b. 將 **運算子** 設定為 **小於**。

    c. 將 **閾值** 設定為 **20**。

    d. 將 **作業** 設定為 **減少計數依據**.

   您現在應該會有一個調整規模設定，其會根據 CPU 使用量進行相應放大/相應縮小。
   ![根據 CPU 調整規模][8]
1. 按一下 **[儲存]** 。

恭喜！ 您現在已成功建立第一個調整規模設定，可根據 CPU 使用量自動調整 Web 應用程式的規模。

> [!NOTE]
> 相同的步驟都適用於開始使用虛擬機器擴展集或雲端服務角色。

## <a name="other-considerations"></a>其他考量
### <a name="scale-based-on-a-schedule"></a>根據排程進行調整
除了根據 CPU 調整規模，您可以針對一週內的特定天數設定不同的調整規模。

1. 按一下 [新增調整規模的條件]。
1. 設定調整規模模式和規則，與預設條件一樣。
1. 針對排程選取 [重複特定天數]。
1. 選取天數，以及應套用調整規模條件的開始/結束時間。

![根據排程調整規模的條件][9]
### <a name="scale-differently-on-specific-dates"></a>在特定日期以不同方式調整規模
除了根據 CPU 調整規模，您可以針對特定日期設定不同的調整規模。

1. 按一下 [新增調整規模的條件]。
1. 設定調整規模模式和規則，與預設條件一樣。
1. 針對排程選取 [指定開始/結束日期]。
1. 選取應套用調整規模條件的開始/結束日期和開始/結束時間。

![根據日期調整規模的條件][10]

### <a name="view-the-scale-history-of-your-resource"></a>檢視資源的調整規模歷程記錄
每當相應增加或減少您的資源時，就會在活動記錄中記錄一個事件。 您可以藉由切換至 [執行歷程記錄] 索引標籤，來檢視過去 24 小時資源的調整規模歷程記錄。

![執行記錄][11]

如果您想要檢視完整的調整規模歷程記錄 (最多 90 天)，可選取 [按一下此處以查看詳細資料]。 活動記錄隨即開啟，其中含有針對您的資源與類別而預先選取的「自動調整規模」。

### <a name="view-the-scale-definition-of-your-resource"></a>檢視資源的調整規模定義
自動調整規模是 Azure Resource Manager 的資源。 您可以藉由切換至 [JSON] 索引標籤，在 JSON 中檢視調整規模定義。

![調整規模定義][12]

您可以視需要直接在 JSON 中進行變更。 變更將在儲存後生效。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>停用自動調整規模並手動調整執行個體規模
有時您可能想要停用目前的調整規模設定，並手動調整資源規模。

按一下頂端的 [停用自動調整規模] 按鈕。
![停用自動調整規模][13]

> [!NOTE]
> 此選項會停用您的設定。 不過，您可以重新啟用自動調整規模以取回設定。

您現在可以設定想要手動調整規模的執行個體數目。

![設定手動調整規模][14]

您隨時都能藉由依序按一下 [啟用自動調整規模] 和 [儲存]，來返回自動調整規模。

## <a name="route-traffic-to-healthy-instances-app-service"></a>將流量路由傳送至狀況良好的實例 (App Service) 

當您相應放大至多個實例時，App Service 可以對實例執行健康情況檢查，只將流量路由傳送至狀況良好的實例。 若要這樣做，請開啟入口網站 App Service，然後選取 [**監視**] 底下的 [**健康情況檢查**]。 選取 [ **啟用** ]，並在您的應用程式上提供有效的 URL 路徑，例如 `/health` 或 `/api/health` 。 按一下 **[儲存]** 。

若要啟用 ARM 範本的功能，請將 `healthcheckpath` 資源的屬性設定 `Microsoft.Web/sites` 為您網站上的健康情況檢查路徑，例如： `"/api/health/"` 。 若要停用此功能，請將屬性設定回空字串 `""` 。

### <a name="health-check-path"></a>健康情況檢查路徑

路徑必須在一分鐘內回應，狀態碼介於200到299之間， (包含) 。 如果路徑未在一分鐘內回應，或傳回範圍外的狀態碼，則會將實例視為「狀況不良」。 App Service 不會遵循健康情況檢查路徑上的302重新導向。 健康情況檢查會與 App Service 的驗證和授權功能整合，即使啟用了這些 microsoft.powershell.secuity 功能，系統還是會到達端點。 如果您使用自己的驗證系統，健康情況檢查路徑必須允許匿名存取。 如果網站已啟用僅限 HTTP **s**，則會透過 HTTP **s** 傳送 healthcheck 要求。

健康情況檢查路徑應該會檢查應用程式的重要元件。 例如，如果您的應用程式相依于資料庫和訊息系統，則健康情況檢查端點應該會連接到這些元件。 如果應用程式無法連接至重要元件，則路徑應該會傳回500層級的回應碼，表示應用程式狀況不良。

#### <a name="security"></a>安全性 

大型企業的開發團隊通常需要遵守其公開 Api 的安全性需求。 若要保護 healthcheck 端點，您應該先使用 [IP 限制](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)、 [用戶端憑證](../../app-service/app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虛擬網路等功能來限制對應用程式的存取。 您可以要求傳入要求的必須符合，以保護 healthcheck 端點本身 `User-Agent` `ReadyForRequest/1.0` 。 因為先前的安全性功能已保護要求，所以 User-Agent 無法詐騙。

### <a name="behavior"></a>行為

當提供健康情況檢查路徑時，App Service 將會偵測所有實例上的路徑。 如果在5次 ping 之後未收到成功的回應碼，該實例就會被視為「狀況不良」。 如果您要相應放大至2個以上的實例，並使用 [基本層](../../app-service/overview-hosting-plans.md) 或更高版本，則負載平衡器輪替將會排除狀況不良的實例 (s) 。 您可以使用應用程式設定來設定所需的失敗 ping 數目 `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` 。 此應用程式設定可以設定為介於2到10之間的任何整數。 例如，如果這是設定為 `2` ，則會在兩個失敗的 ping 之後，從負載平衡器中移除您的實例。 此外，當您相應增加或相應放大時，App Service 會偵測健康情況檢查路徑，以確保新的實例已準備好要求，再新增至負載平衡器。

> [!NOTE]
> 請記住，您的 App Service 方案必須相應放大為2個或更多個實例，而且必須是 **基本層或更高** ，才能進行負載平衡器排除。 如果您只有1個實例，就不會從負載平衡器中移除它，即使其狀況不良也一樣。 

其餘狀況良好的實例可能會產生更高的負載。 為了避免剩餘的實例過多，系統不會排除超過一半的實例。 例如，如果 App Service 的方案相應放大至4個實例，其中3個狀況不良，則會將最多2個從 loadbalancer 旋轉中排除。 其他2個實例 (1 狀況良好，而1個狀況不良的) 將會繼續接收要求。 在所有實例都狀況不良的最糟情況下，將不會排除任何實例。如果您想要覆寫此行為，您可以將 `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` 應用程式設定設為和之間的值 `0` `100` 。 將此設定為較高的值表示將會移除更多狀況不良的實例 (預設值為 50) 。

如果實例在一小時內保持不健全，則會以新的實例取代。 每小時最多隻能取代一個實例，每個 App Service 方案最多可有三個實例。

### <a name="monitoring"></a>監視

提供應用程式的健康情況檢查路徑之後，您可以使用 Azure 監視器來監視網站的健康情況。 從入口網站的 **健康情況檢查** 分頁，按一下頂端工具列中的 **計量** 。 這會開啟新的分頁，您可以在其中看到網站的歷程記錄健康情況狀態，以及建立新的警示規則。 如需有關監視網站的詳細資訊， [請參閱 Azure 監視器上的指南](../../app-service/web-sites-monitor.md)。

## <a name="moving-autoscale-to-a-different-region"></a>將自動調整移至不同區域
本節說明如何將 Azure 自動調整移至相同訂用帳戶和資源群組下的另一個區域。 您可以使用 REST API 來移動自動調整設定。
### <a name="prerequisite"></a>必要條件
1. 請確認訂用帳戶和資源群組可供使用，而且來源和目的地區域中的詳細資料都相同。
1. 確定 Azure 自動調整功能可在 [您想要移至的 azure 區域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)中使用。

### <a name="move"></a>移動
使用 [REST API](/rest/api/monitor/autoscalesettings/createorupdate) 在新的環境中建立自動調整設定。 在目的地區域中建立的自動調整規模設定將會是來源區域中自動調整規模設定的複本。

無法移動與來源區域中的自動調整設定關聯所建立的[診斷設定](./diagnostic-settings.md)。 完成 autosale 設定的建立之後，您必須在目的地區域中重新建立診斷設定。 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>深入瞭解如何在 Azure 區域間移動資源
若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱[將資源移至新的資源群組或訂](../../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶

## <a name="next-steps"></a>後續步驟
- [建立活動記錄警示以監視訂用帳戶的所有自動調整引擎作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [建立活動記錄警示以監視訂用帳戶中所有失敗的相應縮小/相應放大自動調整作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
