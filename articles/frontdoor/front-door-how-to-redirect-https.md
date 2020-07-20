---
title: 使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front 門
description: 瞭解如何使用 Azure 入口網站，建立具有從 HTTP 到 HTTPS 的重新導向流量的 Front 門。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 05699c827af251b890de4c6f195df5872bfbe364
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743604"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front 門

您可以使用 Azure 入口網站來建立具有憑證的[Front 門](front-door-overview.md)以進行 TLS 終止。 路由規則可用來將 HTTP 流量重新導向至 HTTPS。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立具有現有 Web 應用程式資源的 Front
> * 新增具有 TLS/SSL 憑證的自訂網域 
> * 在自訂網域上設定 HTTPS 重新導向

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>建立具有現有 Web 應用程式資源的 Front

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 按一下 Azure 入口網站左上角的 [建立資源]****。
3. 使用搜尋列來搜尋**Front** ，然後在找到資源類型之後，按一下 [**建立**]。
4. 選擇訂用帳戶，然後使用現有的資源群組或建立一個新的。 請注意，在 UI 中要求的位置僅適用于資源群組。 您的 Front 門板設定會部署到所有[Azure Front 門板的 POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    ![設定新 Front 門的基本概念](./media/front-door-url-redirect/front-door-create-basics.png)

5. 按 **[下一步**] 以輸入 [設定] 索引標籤。Front 的設定會在三個步驟中進行：新增預設前端主機、在後端集區中新增後端，然後建立路由規則來對應前端主機的路由行為。 

     ![Front 門板設定設計工具](./media/front-door-url-redirect/front-door-designer.png)

6. 按一下前端 **+** _主機_上的 ['] 圖示，以建立前端主機，並為您的前端輸入預設前端主機的全域唯一名稱（ `\<**name**\>.azurefd.net` ）。 按一下 [**新增**] 繼續進行下一個步驟。

     ![新增前端主機](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 按一下 **+** _後端_集區上的 ['] 圖示，以建立後端集區。 提供後端集區的名稱，然後按一下 [**新增後端**]。
8. 選取後端主機類型作為 [ _App service_]。 選取您的 web 應用程式裝載所在的訂用帳戶，然後從下拉式清單中選取 [**後端主機名稱**] 的特定 web 應用程式。
9. 按一下 [**新增**] 以儲存後端，然後按一下 [**新增**] 以儲存後端集區設定。  ![在後端集區中新增後端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 按一下 **+** _路由規則_上的 ['] 圖示，以建立路由。 提供路由的名稱，例如 ' HttpToHttpsRedirect '，然後將 [_接受的通訊協定_] 欄位設為 [**僅限 HTTP**]。 確定已選取適當的_前端主機_。  
11. 在 [_路由詳細資料_] 區段上，將_路由類型_設定為 [重新**導向**]，確定 [重新導向_類型_] 設定為 [**找到（302）** ]，而 [重新_導向通訊協定_] 設定為 [**僅 HTTPS** 
12. 按一下 [新增] 以儲存 HTTP 至 HTTPS 重新導向的路由規則。
     ![將 HTTP 新增至 HTTPS 重新導向路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 新增另一個用來處理 HTTPS 流量的路由規則。 按一下 **+** _路由規則_上的 [登入]，並提供路由的名稱（例如 ' DefaultForwardingRoute '），然後將 [接受的_通訊協定_] 欄位設為 [**僅 HTTPS**]。 確定已選取適當的_前端主機_。
14. 在 [路由詳細資料] 區段上，將 [_路由類型_] 設定為 [**轉寄**]，確定已選取正確的後端集區，而且_轉送通訊協定_設定為 [**僅限 HTTPS**]。 
15. 按一下 [新增] 以儲存要求轉送的路由規則。
     ![新增 HTTPS 流量的正向路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 按一下 [審核] [ **+ 建立**]，然後按一下 [**建立**] 以建立您的 Front 設定檔。 建立之後，移至資源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>將自訂網域新增至您的 Front 門並在其上啟用 HTTPS
下列步驟展示如何在現有的 Front a 資源上新增自訂網域，然後在其上啟用 HTTP 對 HTTPS 的重新導向。 

### <a name="add-a-custom-domain"></a>新增自訂網域

在此範例中，您會新增子域的 CNAME 記錄 `www` （例如 `www.contosonews.com` ）。

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄以將子域對應至您的 Front 前端預設前端主機（ `<name>.azurefd.net` ，其中 `<name>` 是您的前門設定檔名稱）。

如需 `www.contoso.com` 網域的範例，請新增將名稱對應至的 CNAME 記錄 `www` `<name>.azurefd.net` 。

新增 CNAME 之後，DNS 記錄分頁看起來如下列範例所示：

![從 CNAME 自訂網域到 Front](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>在您的 Front 中將自訂網域上線

1. 在 [Front 入口設計工具] 索引標籤上，按一下 [前端主機] 區段上的 [+] 圖示，以新增自訂網域。 
2. 在 [自訂主機名稱] 欄位中輸入完整的自訂 DNS 名稱，例如 `www.contosonews.com` 。 
3. 一旦將來自網域的 CNAME 對應驗證完成後，請按一下 [**新增**] 以新增自訂網域。
4. 按一下 [**儲存**] 以提交變更。

![[自訂網域] 功能表](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>在您的自訂網域上啟用 HTTPS

1. 按一下已新增的自訂網域，然後在 [**自訂網域 HTTPS**] 區段下，將狀態變更為 [**已啟用**]。
2. 您可以將 [**憑證管理類型**] 設定為 [前端]，並由 front 門板維護、管理及 autorotated 的免費憑證進行_管理_。 您也可以選擇使用與 Azure Key Vault 一起儲存的自訂 TLS/SSL 憑證。 本教學課程假設您使用的是 Front-受管理的憑證。
![為自訂網域啟用 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 按一下 [**更新**] 以儲存選取專案，然後按一下 [**儲存**]。
4. 在**幾分鐘後按一下**[重新整理]，然後再次按一下自訂網域，以查看憑證布建的進度。 

> [!WARNING]
> 針對自訂網域啟用 HTTPS 可能需要幾分鐘的時間，而且如果 CNAME 未直接對應至您的 Front 主機，也會依賴網域擁有權驗證 `<name>.azurefd.net` 。 深入瞭解[如何為自訂網域啟用 HTTPS](./front-door-custom-domain-https.md)。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>設定自訂網域的路由規則

1. 按一下稍早建立的重新導向路由規則。
2. 按一下 [前端主機] 的下拉式清單，然後選取您的自訂網域，將此路由套用至您的網域。
3. 按一下 [更新]  。
4. 也請針對其他路由規則執行相同的作業，也就是您的轉送路由新增自訂網域。
5. 按一下 [**儲存**] 以提交變更。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 深入瞭解如何[在前門上重新導向 URL](front-door-url-redirect.md)。
