---
title: 使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front Door
description: 瞭解如何使用 Azure 入口網站來建立 Front Door，並將流量從 HTTP 重新導向至 HTTPS。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399685"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front Door

您可以使用 Azure 入口網站來建立具有 TLS 終止憑證的 [Front Door](front-door-overview.md) 。 路由規則可用來將 HTTP 流量重新導向至 HTTPS。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 使用現有的 Web 應用程式資源建立 Front Door
> * 新增具有 TLS/SSL 憑證的自訂網域 
> * 在自訂網域上設定 HTTPS 重新導向

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用現有的 Web 應用程式資源建立 Front Door

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 按一下 Azure 入口網站左上角的 [建立資源]****。
3. 使用搜尋列搜尋 **Front Door** ，一旦找到資源類型，請按一下 [ **建立**]。
4. 選擇訂用帳戶，然後使用現有的資源群組或建立一個新的群組。 請注意，在 UI 中要求的位置只適用于資源群組。 您的 Front Door 設定將會部署到所有 [Azure Front Door 的 POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    ![設定新 Front Door 的基本概念](./media/front-door-url-redirect/front-door-create-basics.png)

5. 按一下 **[下一步** ] 進入 [設定] 索引標籤。Front Door 的設定會以三個步驟進行：新增預設前端主機、在後端集區中新增後端，然後建立路由規則來對應前端主機的路由行為。 

     ![Front Door 設定設計工具](./media/front-door-url-redirect/front-door-designer.png)

6. 按一下前端主機 **+** 上的 ' ' _Frontend hosts_圖示來建立前端主機，針對您的 Front Door () 輸入預設前端主機的全域唯一名稱 `\<**name**\>.azurefd.net` 。 按一下 [ **新增** ] 繼續進行下一個步驟。

     ![新增前端主機](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 按一下 **+** _後端_ 集區上的 ' ' 圖示來建立後端集區。 提供後端集區的名稱，然後按一下 [**新增後端**]。
8. 選取後端主機類型作為 _App service_。 選取您的 web 應用程式裝載所在的訂用帳戶，然後從 **後端主機名稱**的下拉式清單中選取特定的 web 應用程式。
9. 按一下 [ **新增** ] 以儲存後端，然後按一下 [ **新增** ] 以儲存後端集區設定。   ![在後端集區中新增後端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 按一下 **+** _路由規則_ 上的 ' ' 圖示來建立路由。 提供路由的名稱，例如 ' HttpToHttpsRedirect '，然後將 [ _接受的通訊協定_ ] 欄位設定為 [ **僅限 HTTP**]。 確定已選取適當的 _前端主機_ 。  
11. 在 [_路由詳細資料_] 區段中，將_路由類型_設定為 [重新**導向**]，確定 [重新_導向類型_] 已設定為 [**找到 (302) ** ，而 [重新_導向通訊協定_] 設定為 [**僅 HTTPS** 
12. 按一下 [新增] 以儲存 HTTP 到 HTTPS 重新導向的路由規則。
     ![將 HTTP 新增至 HTTPS 重新導向路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 新增另一個路由規則來處理 HTTPS 流量。 按一下 [登入 **+** _路由規則_ ]，並提供路由的名稱，例如 ' DefaultForwardingRoute '，然後將 [ _接受的通訊協定_ ] 欄位設定為 [ **僅 HTTPS**]。 確定已選取適當的 _前端主機_ 。
14. 在 [路由詳細資料] 區段中，將 _路由類型_ 設定為 [ **轉寄**]，確定已選取正確的後端集區，且 _轉送通訊協定_ 設定為 [ **僅 HTTPS**]。 
15. 按一下 [新增] 以儲存要求轉送的路由規則。
     ![新增 HTTPS 流量的轉送路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 按一下 [**審核] + [建立**]，然後建立您的 Front Door 設定檔。 **Create** 一旦建立資源，請移至該資源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>將自訂網域新增到您的 Front Door，並在其上啟用 HTTPS
下列步驟示範如何將自訂網域新增至現有的 Front Door 資源，然後在其上啟用 HTTP 到 HTTPS 重新導向。 

### <a name="add-a-custom-domain"></a>新增自訂網域

在此範例中，您會新增子域的 CNAME 記錄 `www` (例如 `www.contosonews.com`) 。

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

新增 CNAME 記錄以將子域對應至 Front Door 的預設前端主機 (`<name>.azurefd.net` ，其中 `<name>` 是您 Front Door 設定檔) 的名稱。

針對 `www.contoso.com` 網域，例如，新增將名稱對應至的 CNAME 記錄 `www` `<name>.azurefd.net` 。

新增 CNAME 之後，DNS 記錄分頁看起來如下列範例所示：

![要 Front Door 的 CNAME 自訂網域](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>在您的 Front Door 上架自訂網域

1. 在 [Front Door 設計工具] 索引標籤上，按一下 [前端主機] 區段上的 [+] 圖示，以新增自訂網域。 
2. 在 [自訂主機名稱] 欄位中，輸入完整的自訂 DNS 名稱，例如 `www.contosonews.com` 。 
3. 一旦驗證從網域到 Front Door 的 CNAME 對應，請按一下 [ **新增** ] 以新增自訂網域。
4. 按一下 [ **儲存** ] 以提交變更。

![[自訂網域] 功能表](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>在您的自訂網域上啟用 HTTPS

1. 按一下已新增的自訂網域，並在 [ **自訂網域 HTTPS**] 區段下，將 [狀態] 變更為 [ **已啟用**]。
2. 您可以 Front Door 保留、管理及 autorotated 的免費憑證，讓 **憑證管理類型** 設定為 _Front Door 受控_ 。 您也可以選擇使用隨 Azure Key Vault 儲存的自訂 TLS/SSL 憑證。 本教學課程假設使用 Front Door 受控憑證。
![啟用自訂網域的 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 按一下 [ **更新** ] 以儲存選取專案，然後按一下 [ **儲存**]。
4. 在 **幾分鐘後按一下** [重新整理]，然後再按一次 [自訂網域] 以查看憑證布建的進度。 

> [!WARNING]
> 針對自訂網域啟用 HTTPS 可能需要幾分鐘的時間，而且如果 CNAME 不會直接對應到您的 Front Door 主機，也需要依賴網域擁有權驗證 `<name>.azurefd.net` 。 深入瞭解 [如何為自訂網域啟用 HTTPS](./front-door-custom-domain-https.md)。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>設定自訂網域的路由規則

1. 按一下稍早建立的重新導向路由規則。
2. 按一下前端主機的下拉式清單，然後選取您的自訂網域，以針對您的網域套用此路由。
3. 按一下 [更新] 。
4. 對另一個路由規則（也就是您的轉送路由）進行相同的作業，以新增自訂網域。
5. 按一下 [ **儲存** ] 以提交變更。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 深入瞭解 [Front Door 上的 URL 重新導向](front-door-url-redirect.md)。
