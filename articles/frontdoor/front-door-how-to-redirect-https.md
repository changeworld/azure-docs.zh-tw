---
title: 使用 Azure 門戶建立具有 HTTP 到 HTTPS 重定向的前門
description: 瞭解如何使用 Azure 門戶創建具有從 HTTP 重定向流量重定向到 HTTPS 的前門。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877995"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 門戶建立具有 HTTP 到 HTTPS 重定向的前門

可以使用 Azure 門戶建立具有 TLS 終止憑證[的前門](front-door-overview.md)。 路由規則用於將 HTTP 流量重定向到 HTTPS。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 使用現有的 Web 應用程式建立前門
> * 使用 TLS/SSL 憑證新增自訂網域 
> * 設定在自訂網域上重定向 HTTPS

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用現有的 Web 應用程式建立前門

1. 登錄到中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。
2. 按一下 Azure 入口網站左上角的 [建立資源]****。
3. 使用搜索欄搜索**前門**,找到資源類型後,按兩下"**創建**"。
4. 選擇訂閱,然後使用現有資源組或創建新資源組。 請注意,UI 中請求的位置僅適用於資源組。 您的前門設定將部署在 Azure 前門的所有[POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    ![為新前門配置基礎知識](./media/front-door-url-redirect/front-door-create-basics.png)

5. 按下 **「下一步**」輸入設定選項卡。前門的配置分三個步驟進行 - 添加預設前端主機,在後端池中添加後端,然後創建路由規則以映射前端主機的路由行為。 

     ![前門設定設計器](./media/front-door-url-redirect/front-door-designer.png)

6. 按一**+** 下_前端主機_上的『圖示以創建前端主機,為前門的預設前端主機輸入全域唯一名稱)。`\<**name**\>.azurefd.net` 按下「**添加**」以繼續下一步。

     ![新增前端主機](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 單擊**+**_後端池_上的『圖示以創建後端池』 。 提供後端介面的名稱,然後按下「**新增後端**」。
8. 選擇「後端介面主機類型」 作為_套用服務_。 選擇託管 Web 應用的訂閱,然後從**後端主機名**的下拉清單中選擇特定的 Web 應用。
9. 按下「**新增」** 以儲存後端,然後按一下「再次**新增**」 以儲存後端池配置。  ![在後端介面中加入後端介面](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 按一**+** 下_路由規則_上的 '' 圖示以建立路由。 提供路由的名稱,例如"HttpToHTTtextsRedirect",然後將 _「接受的協定」_ 欄位設定為 **「僅 HTTP」。。** 確保選擇適當的_前端主機_。  
11. 在 _「路徑詳細資訊_」部分,將 _「路由類型_」設置為**重定向**,_確保重定向類型_設定為 **「找到」(302),** 重定向_協定_設定為**僅 HTTPS。** 
12. 按下「添加」將 HTTP 的路由規則保存到 HTTPS 重定向。
     ![將 HTTP 新增到 HTTPS 重定向路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 添加用於處理 HTTPS 流量的另一個路由規則。 按下**+**_路由規則_上的『符號,並為路由提供名稱,例如"默認轉發路由",然後將 _"接受的協定"_ 欄位設置為 **「僅 HTTPS」。。** 確保選擇適當的_前端主機_。
14. 在"路徑詳細資訊"部分,將_路由類型_設置為 **"轉發 ",** 確保選擇了正確的後端池,並且 _"轉發協定_**"設置為僅 HTTPS。** 
15. 按下「添加」以保存路由規則以進行請求轉發。
     ![為 HTTPS 流量新增轉寄路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 按下 **「審閱 + 創建**」,然後**創建**,以創建前門配置檔。 轉到創建後的資源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>將自訂網域加入前門,並在其上啟用 HTTPS
以下步驟演示如何在現有前門資源上添加自定義域,然後啟用 HTTP 到 HTTPS 重定向。 

### <a name="add-a-custom-domain"></a>新增自訂網域

這個功能中,為`www`子域加入 CNAME 紀錄(例如`www.contosonews.com`,

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

添加 CNAME 記錄以將子域映射到前門的預設前端主機`<name>.azurefd.net`(, 前門配置檔的`<name>`名稱)。

對於`www.contoso.com`網域,例如,添加將`www`名稱映射`<name>.azurefd.net`到的CNAME記錄。

新增 CNAME 之後，DNS 記錄分頁看起來如下列範例所示：

![CNAME 自訂網域到前門](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>在前門上的自訂網域

1. 在「前門設計器」選項卡上,按一下「前端主機」部分上的「+」圖示以添加新的自定義域。 
2. 在自訂主機名稱段中輸入完全限定的自訂 DNS`www.contosonews.com`名稱, 範例 。 
3. 驗證從網域到前門的 CNAME 映射後,按下「**添加」** 以添加自訂域。
4. 按下 **「保存」** 以提交更改。

![[自訂網域] 功能表](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>在自訂網域上啟用 HTTPS

1. 按下已添加的自訂網域,並在 **「自訂域 HTTPS」** 部分下將狀態更改為 **「已啟用**」 。
2. 您可以將**憑證管理類型**設置為_前門管理_,以便由前門維護、管理和自動旋轉免費證書。 您還可以選擇使用與 Azure 密鑰保管庫一起存儲的自定義 TLS/SSL 證書。 本教程假定使用前門託管證書。
![為自訂網域開啟 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 按下 **「更新」** 以保存所選內容,然後單擊「**保存**」 。
4. 按下幾分鐘後**刷新**,然後再次按下自定義域以查看證書預配的進度。 

> [!WARNING]
> 為自訂網域啟用 HTTPS 可能需要幾分鐘時間,並且如果 CNAME 未直接映`<name>.azurefd.net`射到前門主機 ,則還要依賴於網域擁有權驗證。 詳細瞭解[如何為自定義域啟用 HTTPS。](./front-door-custom-domain-https.md)

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>設定自訂網域的路由規則

1. 按下之前創建的重定向路由規則。
2. 按下 Frontend 主機的下拉清單,並選擇自訂網域以為您的域應用此路由。
3. 按一下 [更新]****。
4. 對其他路由規則執行相同的操作,即為轉發路由添加自定義域。
5. 按下 **「保存」** 以提交更改。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 瞭解有關前門[URL 重定向](front-door-url-redirect.md)的更多資訊。
