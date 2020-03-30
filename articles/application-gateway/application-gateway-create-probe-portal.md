---
title: 使用門戶創建自訂探測
titleSuffix: Azure Application Gateway
description: 了解如何使用入口網站建立應用程式閘道的自訂探查
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074599"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>使用入口網站建立應用程式閘道的自訂探查

> [!div class="op_single_selector"]
> * [Azure 門戶](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 經典電源外殼](application-gateway-create-probe-classic-ps.md)

在本文中，通過 Azure 門戶將自訂運行狀況探測添加到現有應用程式閘道。 使用運行狀況探測，Azure 應用程式閘道監視後端池中資源的運行狀況。

## <a name="before-you-begin"></a>開始之前

如果您還沒有應用程式閘道，請瀏覽[建立應用程式閘道](application-gateway-create-gateway-portal.md)以建立要使用的應用程式閘道。

## <a name="create-probe-for-application-gateway-v2-sku"></a>為應用程式閘道 v2 SKU 創建探測

您可以透過入口網站中兩個步驟的程序來設定探查。 第一步是輸入探測配置所需的值。 在第二步中，使用此探測配置測試後端運行狀況並保存探測器。 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>輸入探測屬性

1. 登錄到 Azure[門戶](https://portal.azure.com)。 如果您還沒有帳戶，您可以註冊一[個月的免費試用](https://azure.microsoft.com/free)

2. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 partners.contoso.net 輕鬆地存取應用程式閘道。

3. 選擇 **"運行狀況"探測器**，然後選擇 **"添加"** 以添加新的運行狀況探測器。

   ![添加新探頭][4]

4. 在 **"添加運行狀況探測"** 頁上，填寫探頭所需的資訊，並在完成後選擇 **"確定**"。

   |**設定** | **價值** | **詳細資料**|
   |---|---|---|
   |**名稱**|customProbe|此值是為門戶中可訪問的探測器命名的易記名稱。|
   |**協定**|HTTP 或 HTTPS | 健康狀態探查所使用的通訊協定。 |
   |**主機**|亦即 contoso.com|此值是在應用程式伺服器上運行的虛擬主機（與 VM 主機名稱不同）的名稱。 探測器從 HTTP 設置發送到（協定）：//（主機名稱）:(埠）/urlPath。  這在應用程式閘道上配置多網站時適用。 如果為單個網站配置了應用程式閘道，則輸入"127.0.0.1"。|
   |**從後端 HTTP 設置中選擇主機名稱**|[是] 或 [否]|將探測中的*主機*標頭設置為與此探測器關聯的 HTTP 設置關聯的後端池中的後端資源的主機名稱。 在多租戶後端（如 Azure 應用服務）的情況下特別需要。 [深入了解](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**路徑**|/ 或另一個路徑|自訂探查完整 URL 的其餘部分。 有效路徑的開頭為 '/'。 對於 HTTP：\//contoso.com 的預設路徑，只需使用 "/" |
   |**間隔 (秒)**|30|執行探查以檢查健康狀態的頻率。 建議您不要設定低於 30 秒。|
   |**逾時 (秒)**|30|探頭在超時前等待的時間量。如果在此超時期間未收到有效的回應，則探測器將標記為失敗。 逾時間隔需要高到足以進行 http 呼叫，以確保可使用後端的健康狀態頁面。 請注意，超時值不應超過此探測設置中使用的"間隔"值或與此探測器關聯的 HTTP 設置中的"請求超時"值。|
|**不正常的閾值**|3|被視為不正常的連續失敗嘗試數。 閾值可以設置為 1 或更多。|
   |**使用探頭匹配條件**|[是] 或 [否]|預設情況下，狀態碼介於 200 和 399 之間的 HTTP（S） 回應被視為正常。 您可以更改可接受的後端回應代碼或後端回應正文的範圍。 [深入了解](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP 設置**|從下拉清單中選擇|探測將與此處選擇的 HTTP 設置相關聯，因此，它將監視與所選 HTTP 設置關聯的後端池的運行狀況。 它將對探測請求使用與所選 HTTP 設置中使用的埠相同的埠。 您只能選擇與任何其他自訂探測不關聯的 HTTP 設置。 <br>請注意，只有這些 HTTP 設置可用於具有與此探測配置中選擇的協定相同的協定且具有*從後端 HTTP 設置開關選擇主機名稱*的相同狀態的關聯。|
   
   > [!IMPORTANT]
   > 僅當後端與一個或多個 HTTP 設置關聯時，探測器才會監視後端的運行狀況。 它將監視與此探測器關聯的 HTTP 設置關聯的後端池的後端資源。 探測請求將從 HTTP 設置發送到 HTTP：//（主機名稱）:(埠）/urlPath。

### <a name="test-backend-health-with-the-probe"></a>使用探頭測試後端運行狀況

輸入探測屬性後，可以測試後端資源的運行狀況，以驗證探測配置是否正確，以及後端資源是否按預期工作。

1. 選擇 **"測試**"並記下探頭的結果。 應用程式閘道測試與此探測使用的 HTTP 設置關聯的後端池中的所有後端資源的運行狀況。 

   ![測試後端運行狀況][5]

2. 如果存在任何不正常的後端資源，請檢查 **"詳細資訊"** 列以瞭解資源不正常的原因。 如果由於探測配置不正確而標記資源不正常，請選擇 **"返回以探測**"連結並編輯探測配置。 否則，如果由於後端問題而標記資源不正常，則解決後端資源的問題，然後通過選擇 **"返回以探測**"連結並選擇 **"測試**"再次測試後端。

   > [!NOTE]
   > 即使使用不正常的後端資源，也可以選擇保存探測器，但不建議這樣做。 這是因為應用程式閘道從後端池中刪除那些被探測器確定為不正常的後端資源。 如果後端池中沒有正常運行的資源，您將無法訪問您的應用程式，並且將出現 502 錯誤。

   ![查看探測結果][6]

3. 選擇 **"添加"** 以保存探頭。 

## <a name="create-probe-for-application-gateway-v1-sku"></a>為應用程式閘道 v1 SKU 創建探測

您可以透過入口網站中兩個步驟的程序來設定探查。 第一個步驟是建立探查。 在第二個步驟中，將探查新增至應用程式閘道的後端 http 設定。

### <a name="create-the-probe"></a><a name="createprobe"></a>建立探查

1. 登錄到 Azure[門戶](https://portal.azure.com)。 如果您還沒有帳戶，您可以註冊一[個月的免費試用](https://azure.microsoft.com/free)

2. 在 Azure 入口網站的 [我的最愛] 窗格中，選取 [所有資源]****。 在 **"所有資源"** 頁中選擇應用程式閘道。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選] 方塊中輸入 partners.contoso.net 輕鬆地存取應用程式閘道。

3. 選擇 **"探頭**"，然後選擇 **"添加**"以添加探測器。

   ![已填入資訊的 [新增探查] 刀鋒視窗][1]

4. 在 **"添加運行狀況探測"** 邊欄選項卡上，填寫探頭所需的資訊，並在完成後選擇 **"確定**"。

   |**設定** | **價值** | **詳細資料**|
   |---|---|---|
   |**名稱**|customProbe|此值是為門戶中可訪問的探測器命名的易記名稱。|
   |**協定**|HTTP 或 HTTPS | 健康狀態探查所使用的通訊協定。 |
   |**主機**|亦即 contoso.com|此值是在應用程式伺服器上運行的虛擬主機（與 VM 主機名稱不同）的名稱。 探測器從 HTTP 設置發送到（協定）：//（主機名稱）:(埠）/urlPath。  這在應用程式閘道上配置多網站時適用。 如果為單個網站配置了應用程式閘道，則輸入"127.0.0.1"。|
   |**從後端 HTTP 設置中選擇主機名稱**|[是] 或 [否]|將探測中的*主機*標頭設置為與此探測器關聯的 HTTP 設置關聯的後端池中的後端資源的主機名稱。 在多租戶後端（如 Azure 應用服務）的情況下特別需要。 [深入了解](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**路徑**|/ 或另一個路徑|自訂探查完整 URL 的其餘部分。 有效路徑的開頭為 '/'。 對於 HTTP：\//contoso.com 的預設路徑，只需使用 "/" |
   |**間隔 (秒)**|30|執行探查以檢查健康狀態的頻率。 建議您不要設定低於 30 秒。|
   |**逾時 (秒)**|30|探頭在超時前等待的時間量。如果在此超時期間未收到有效的回應，則探測器將標記為失敗。 逾時間隔需要高到足以進行 http 呼叫，以確保可使用後端的健康狀態頁面。 請注意，超時值不應超過此探測設置中使用的"間隔"值或與此探測器關聯的 HTTP 設置中的"請求超時"值。|
|**不正常的閾值**|3|被視為不正常的連續失敗嘗試數。 閾值可以設置為 1 或更多。|
   |**使用探頭匹配條件**|[是] 或 [否]|預設情況下，狀態碼介於 200 和 399 之間的 HTTP（S） 回應被視為正常。 您可以更改可接受的後端回應代碼或後端回應正文的範圍。 [深入了解](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > 主機名稱與伺服器名稱不同。 此值是在應用程式伺服器上執行的虛擬主機名稱。 探查會傳送到 http://(host name):(port from httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>將探查新增到閘道

既然已經建立探查，此時即可將它新增到閘道。 探查設定是在應用程式閘道的後端 http 設定上設定。

1. 按一下應用程式閘道的 [HTTP 設定]**** 來開啟組態刀鋒視窗，然後按一下視窗中列出的目前後端 http 設定。

   ![[https 設定] 視窗][2]

2. 在**AppGatewayBackEndHttpSettings 設置**頁面上，選中 **"使用自訂探測**"核取方塊，然後選擇在 **"自訂探測器**下拉清單"上的["創建探測](#createprobe)"部分中創建的探測器。
   完成時，按一下 [儲存]**** 即可套用設定。

## <a name="next-steps"></a>後續步驟

使用[後端運行狀況視圖](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)查看探測器確定的後端資源的運行狀況。

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
