---
title: 使用入口網站建立自訂探查
titleSuffix: Azure Application Gateway
description: 了解如何使用入口網站建立應用程式閘道的自訂探查
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74074599"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用入口網站建立應用程式閘道的自訂探查

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 傳統 PowerShell](application-gateway-create-probe-classic-ps.md)

在本文中，您會透過 Azure 入口網站，將自訂健康情況探查新增至現有的應用程式閘道。 Azure 應用程式閘道會使用健康情況探查，監視後端集區中資源的健全狀況。

## <a name="before-you-begin"></a>開始之前

如果您還沒有應用程式閘道，請瀏覽[建立應用程式閘道](application-gateway-create-gateway-portal.md)以建立要使用的應用程式閘道。

## <a name="create-probe-for-application-gateway-v2-sku"></a>建立應用程式閘道 v2 SKU 的探查

您可以透過入口網站中兩個步驟的程序來設定探查。 第一個步驟是輸入探查設定所需的值。 在第二個步驟中，您會使用此探查設定來測試後端健全狀況，並儲存探查。 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>輸入探查屬性

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您還沒有帳戶，您可以註冊一個[月免費試用](https://azure.microsoft.com/free)

2. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 partners.contoso.net 輕鬆地存取應用程式閘道。

3. 選取 [**健康情況探查**]，然後選取 [新增] 以新增健全狀況探查。 **Add**

   ![加入新的探查][4]

4. 在 [**新增健康情況探查**] 頁面上，填寫探查的必要資訊，然後在完成時選取 **[確定]**。

   |**設定** | **ReplTest1** | **詳細資料**|
   |---|---|---|
   |**名稱**|customProbe|此值是可在入口網站中存取之探查的易記名稱。|
   |**通訊協定**|HTTP 或 HTTPS | 健康狀態探查所使用的通訊協定。 |
   |**主機**|亦即 contoso.com|此值是在應用程式伺服器上執行的虛擬主機名稱稱（與 VM 主機名稱不同）。 探查會傳送至（通訊協定）：//（主機名稱）:(HTTPsetting 的埠）/urlPath。  這適用于在應用程式閘道上設定多網站時。 如果已針對單一網站設定應用程式閘道，請輸入 ' 127.0.0.1 '。|
   |**從後端 HTTP 設定挑選主機名稱**|[是] 或 [否]|將探查中的*主機*標頭設定為後端集區中，與與此探查相關聯之 HTTP 設定相關聯的後端資源的主機名稱。 在多租使用者後端（例如 Azure app service）的情況下特別需要。 [深入了解](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**路徑**|/ 或另一個路徑|自訂探查完整 URL 的其餘部分。 有效路徑的開頭為 '/'。 針對 HTTP：\//contoso.com 的預設路徑，只要使用 '/' |
   |**間隔 (秒)**|30|執行探查以檢查健康狀態的頻率。 建議您不要設定低於 30 秒。|
   |**逾時 (秒)**|30|探查在超時前等待的時間量。如果在此超時期間內未收到有效的回應，則會將探查標示為失敗。 逾時間隔需要高到足以進行 http 呼叫，以確保可使用後端的健康狀態頁面。 請注意，超時值不應大於此探查設定中使用的「間隔」值，或 HTTP 設定中將與此探查相關聯的「要求超時」值。|
|**狀況不良閾值**|3|連續失敗嘗試視為狀況不良的次數。 閾值可以設定為1或更多。|
   |**使用探查符合條件**|[是] 或 [否]|根據預設，狀態碼介於200和399之間的 HTTP （S）回應視為狀況良好。 您可以變更後端回應碼或後端回應主體的可接受範圍。 [深入了解](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP 設定**|從下拉式清單選取|探查會與此處所選的 HTTP 設定相關聯，因此，會監視與所選 HTTP 設定相關聯之後端集區的健全狀況。 它會使用與所選 HTTP 設定中所使用的相同的探查要求埠。 您只能選擇與其他任何自訂探查沒有關聯的 HTTP 設定。 <br>請注意，只有這些 HTTP 設定適用于關聯，其通訊協定與此探查設定中所選擇的通訊協定相同，而且具有*從後端 HTTP 設定參數挑選主機名稱*的相同狀態。|
   
   > [!IMPORTANT]
   > 探查只會在與一或多個 HTTP 設定相關聯時，才會監視後端的健康狀態。 它會監視那些後端集區的後端資源，這些後端集區會與與此探查相關聯的 HTTP 設定相關聯。 探查要求將會從 HTTPsetting 傳送至 HTTP：//（主機名稱）:(埠）/urlPath。

### <a name="test-backend-health-with-the-probe"></a>使用探查測試後端健全狀況

輸入探查屬性之後，您可以測試後端資源的健全狀況，以確認探查設定是否正確，以及後端資源是否如預期般運作。

1. 選取 [**測試**] 並記下探查的結果。 應用程式閘道會測試後端集區中所有後端資源的健全狀況，此集區與用於此探查的 HTTP 設定相關聯。 

   ![測試後端健全狀況][5]

2. 如果有任何狀況不良的後端資源，請檢查 [**詳細資料**] 欄，以瞭解資源狀況不良狀態的原因。 如果資源因為不正確的探查設定而被標示為狀況不良，請選取 [**返回探查**] 連結，然後編輯探查設定。 否則，如果因後端發生問題而將資源標示為狀況不良，請解決後端資源的問題，然後選取 [**回到探查**] 連結並選取 [**測試**]，再次測試後端。

   > [!NOTE]
   > 您可以選擇儲存探查，即使使用狀況不良的後端資源，但不建議這麼做。 這是因為應用程式閘道會從後端集區中移除那些後端資源，這些是由探查所決定的狀況不良。 萬一後端集區中沒有狀況良好的資源，您將無法存取您的應用程式，而且會收到502錯誤。

   ![查看探查結果][6]

3. 選取 [**新增**] 以儲存探查。 

## <a name="create-probe-for-application-gateway-v1-sku"></a>建立應用程式閘道 v1 SKU 的探查

您可以透過入口網站中兩個步驟的程序來設定探查。 第一個步驟是建立探查。 在第二個步驟中，將探查新增至應用程式閘道的後端 http 設定。

### <a name="create-the-probe"></a><a name="createprobe"></a>建立探查

1. 登入 [Azure 入口網站](https://portal.azure.com)。 如果您還沒有帳戶，您可以註冊一個[月免費試用](https://azure.microsoft.com/free)

2. 在 Azure 入口網站的 [我的最愛] 窗格中，選取 [所有資源]****。 在 [**所有資源**] 頁面中選取應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 partners.contoso.net 輕鬆地存取應用程式閘道。

3. 選取 [**探查**]，然後選取 [**新增**] 以新增探查。

   ![已填入資訊的 [新增探查] 刀鋒視窗][1]

4. 在 [**新增健康情況探查**] 分頁上，填寫探查的必要資訊，然後在完成時選取 **[確定]**。

   |**設定** | **ReplTest1** | **詳細資料**|
   |---|---|---|
   |**名稱**|customProbe|此值是可在入口網站中存取之探查的易記名稱。|
   |**通訊協定**|HTTP 或 HTTPS | 健康狀態探查所使用的通訊協定。 |
   |**主機**|亦即 contoso.com|此值是在應用程式伺服器上執行的虛擬主機名稱稱（與 VM 主機名稱不同）。 探查會傳送至（通訊協定）：//（主機名稱）:(HTTPsetting 的埠）/urlPath。  這適用于在應用程式閘道上設定多網站時。 如果已針對單一網站設定應用程式閘道，請輸入 ' 127.0.0.1 '。|
   |**從後端 HTTP 設定挑選主機名稱**|[是] 或 [否]|將探查中的*主機*標頭設定為後端集區中，與與此探查相關聯之 HTTP 設定相關聯的後端資源的主機名稱。 在多租使用者後端（例如 Azure app service）的情況下特別需要。 [深入了解](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**路徑**|/ 或另一個路徑|自訂探查完整 URL 的其餘部分。 有效路徑的開頭為 '/'。 針對 HTTP：\//contoso.com 的預設路徑，只要使用 '/' |
   |**間隔 (秒)**|30|執行探查以檢查健康狀態的頻率。 建議您不要設定低於 30 秒。|
   |**逾時 (秒)**|30|探查在超時前等待的時間量。如果在此超時期間內未收到有效的回應，則會將探查標示為失敗。 逾時間隔需要高到足以進行 http 呼叫，以確保可使用後端的健康狀態頁面。 請注意，超時值不應大於此探查設定中使用的「間隔」值，或 HTTP 設定中將與此探查相關聯的「要求超時」值。|
|**狀況不良閾值**|3|連續失敗嘗試視為狀況不良的次數。 閾值可以設定為1或更多。|
   |**使用探查符合條件**|[是] 或 [否]|根據預設，狀態碼介於200和399之間的 HTTP （S）回應視為狀況良好。 您可以變更後端回應碼或後端回應主體的可接受範圍。 [深入了解](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > 主機名稱與伺服器名稱不同。 此值是在應用程式伺服器上執行的虛擬主機名稱。 探查會傳送到 http://(host name):(port from httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>將探查新增到閘道

既然已經建立探查，此時即可將它新增到閘道。 探查設定是在應用程式閘道的後端 http 設定上設定。

1. 按一下應用程式閘道的 [HTTP 設定]**** 來開啟組態刀鋒視窗，然後按一下視窗中列出的目前後端 http 設定。

   ![[https 設定] 視窗][2]

2. 在 [ **appGatewayBackEndHttpSettings**設定] 頁面上，核取 [**使用自訂探查**] 核取方塊，然後在 [**自訂探查**] 下拉式方塊中選擇 [[建立探查](#createprobe)] 區段中所建立的探查。
   完成時，按一下 [儲存]**** 即可套用設定。

## <a name="next-steps"></a>後續步驟

使用[後端健全狀況視圖](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)來查看由探查決定的後端資源健康狀態。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
