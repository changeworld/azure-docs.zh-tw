---
title: 監視 App Service 實例的健全狀況
description: 瞭解如何使用健康情況檢查來監視 App Service 實例的健康情況。
keywords: azure app service，web 應用程式，健康情況檢查，路由流量，狀況良好的實例，路徑，監視，
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 60a210c6c336c1b820015304e8ab53bc894c17bf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792482"
---
# <a name="monitor-app-service-instances-using-health-check"></a>使用健康情況檢查來監視 App Service 實例

![健康情況檢查失敗][2]

本文使用 Azure 入口網站中的健康情況檢查來監視 App Service 的實例。 健康情況檢查藉由移除狀況不良的實例來提高應用程式的可用性。 您的 [App Service 計畫](/overview-hosting-plans) 應調整為兩個或更多個實例，才能使用健康情況檢查。 健康情況檢查路徑應該會檢查應用程式的重要元件。 例如，如果您的應用程式相依于資料庫和訊息系統，則健康情況檢查端點應該會連接到這些元件。 如果應用程式無法連接至重要元件，則路徑應該會傳回500層級的回應碼，以指出應用程式狀況不良。

## <a name="what-app-service-does-with-health-checks"></a>健康情況檢查的 App Service 用途

- 當您的應用程式上有路徑時，健康情況檢查會以1分鐘的間隔，在 App Service 應用程式的所有實例上 ping 此路徑。
- 如果實例在兩個或多個要求之後，不會以狀態碼回應 200-299 (包含) ，或回應偵測失敗，系統就會判斷它是否狀況不良，並將其移除。
- 移除之後，健全狀況檢查會繼續偵測狀況不良的實例。 如果持續回應失敗，App Service 會重新開機基礎 VM，以將實例恢復正常狀態。
- 如果實例在一小時內保持不健全，則會以新的實例取代。
- 此外，當您相應增加或相應放大時，App Service 會偵測健康情況檢查路徑，以確保新的實例就緒。

> [!NOTE]
> 健康情況檢查未遵循302重新導向。 每小時最多隻能取代一個實例，每個 App Service 方案最多可有三個實例。
>

## <a name="enable-health-check"></a>啟用健康情況檢查

![Azure 入口網站中的健康情況檢查流覽][3]

- 若要啟用健康情況檢查，請流覽至 Azure 入口網站，然後選取您的 App Service 應用程式。
- 在 [ **監視**] 底下，選取 [ **健康情況檢查**]。
- 選取 [ **啟用** ]，並在您的應用程式上提供有效的 URL 路徑，例如 `/health` 或 `/api/health` 。
- 按一下 [儲存]。

> [!CAUTION]
> 健康情況檢查設定變更會重新開機您的應用程式。 若要將對生產應用程式的影響降到最低，建議您設定 [預備](deploy-staging-slots.md) 位置，並交換至生產環境。
>

### <a name="configuration"></a>組態

除了設定健康情況檢查選項以外，您也可以設定下列 [應用程式設定](configure-common.md)：

| 應用程式設定名稱 | 允許的值 | 描述 |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Ping 失敗的最大數目。 例如，當設定為時 `2` ，您的實例會在失敗的 ping 之後移除 `2` 。 此外，當您相應增加或相應放大時，請 App Service ping 健康情況檢查路徑，以確保新的實例就緒。 |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | 為了避免狀況不良的實例，將不會排除超過一半的實例。 例如，如果 App Service 方案調整為四個實例，且三個狀況不良，則最多會排除兩個實例。 另外兩個實例 (一個狀況良好，而一個狀況不良的) 將會繼續接收要求。 在所有實例都狀況不良的最糟情況下，將不會排除任何實例。 若要覆寫此行為，請將應用程式設定設為與之間的值 `0` `100` 。 值愈高表示將移除更多狀況不良的實例 (預設值為 50) 。 |

#### <a name="authentication-and-security"></a>驗證和安全性

健康情況檢查會與 App Service 的驗證和授權功能整合。 如果啟用了這些安全性功能，就不需要其他設定。 但是，如果您使用自己的驗證系統，健康情況檢查路徑必須允許匿名存取。 如果網站僅啟用 HTTP **s**，則會透過 HTTP **s** 傳送健康情況檢查要求。

大型企業開發團隊通常需要遵守公開 Api 的安全性需求。 若要保護健康情況檢查端點，您應該先使用 [IP 限制](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、 [用戶端憑證](app-service-ip-restrictions.md#set-an-ip-address-based-rule)或虛擬網路等功能來限制應用程式存取。 您可以要求傳入要求的要求相符，以保護健康情況檢查端點 `User-Agent` `ReadyForRequest/1.0` 。 由於要求已受到先前安全性功能的保護，因此 User-Agent 無法偽造。

## <a name="monitoring"></a>監視

提供應用程式的健康情況檢查路徑之後，您可以使用 Azure 監視器來監視網站的健康情況。 從入口網站的 **健康情況檢查** 分頁，按一下頂端工具列中的 **計量** 。 這會開啟新的分頁，您可以在其中看到網站的歷程記錄健康情況狀態，以及建立新的警示規則。 如需有關監視網站的詳細資訊， [請參閱 Azure 監視器上的指南](web-sites-monitor.md)。

## <a name="next-steps"></a>後續步驟
- [建立活動記錄警示以監視訂用帳戶的所有自動調整引擎作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [建立活動記錄警示以監視訂用帳戶中所有失敗的相應縮小/相應放大自動調整作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
