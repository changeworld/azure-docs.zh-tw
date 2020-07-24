---
title: 教學課程-整合 Azure 春季雲端與 Azure 負載平衡解決方案
description: 如何整合 Azure 春季雲端與 Azure 負載平衡解決方案
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3a7ed148c4bc8d58b2dcbc344656137a403c62eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037537"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>整合 Azure Spring Cloud 與 Azure 負載平衡解決方案

Azure 春季雲端支援 Azure 上的微服務。  增加企業可能需要多個資料中心，並管理 Azure 春季雲端的多個實例。

Azure 已提供不同的負載平衡解決方案。 有三個選項可整合 Azure 春季雲端與 Azure 負載平衡解決方案：

1.  整合 Azure 春季雲端與 Azure 流量管理員
2.  整合 Azure 春季雲端與 Azure App 閘道
3.  整合 Azure 春季雲端與 Azure Front

## <a name="prerequisites"></a>先決條件

* Azure 春季雲端：[如何建立 azure 春季雲端服務](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)
* Azure 流量管理員：[如何建立流量管理員](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure App 閘道：[如何建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Azure Front 門板：[如何建立 Front 門](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>整合 Azure 春季雲端與 Azure 流量管理員

若要將 Azure 春季雲端與流量管理員整合，請將其公用端點新增為流量管理員的端點，然後設定流量管理員和 Azure 春季雲端的自訂網域。

### <a name="add-endpoint-in-traffic-manager"></a>在流量管理員中新增端點
在流量管理員中新增端點：
1.  將 [**類型**] 指定為 [*外部端點*]。
1.  輸入每個 Azure 春季雲端公用端點的完整功能變數名稱（FQDN）。
1. 按一下 [確定]  。

    ![流量管理員 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ 流量管理員2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>設定自訂網域
若要完成設定：
1.  登入網域提供者的網站，並從您的自訂網域建立 CNAME 記錄對應至流量管理員的 Azure 預設功能變數名稱。
1.  遵循[如何將自訂網域新增至 Azure 春季雲端](spring-cloud-tutorial-custom-domain.md)的指示。
1. 將上述的自訂網域系結新增至 Azure 春季雲端對應的 app service，並在該處上傳 SSL 憑證。

    ![流量管理員3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>整合 Azure 春季雲端與 Azure App 閘道

若要與 azure 春季雲端服務整合，請完成下列設定：

### <a name="configure-backend-pool"></a>設定後端集區
1. 將 [**目標型別**] 指定為 [ *IP 位址*] 或 [ *FQDN*]。
1. 輸入您的 Azure 春季雲端公用端點。

    ![應用程式閘道1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>新增自訂探查
1. 選取 [**健康情況探查**]，然後按一下 [**新增**] 以開啟自訂**探查**對話方塊。 
1. 重點是針對 [**從後端 HTTP 設定選擇主機名稱**] 選項選取 *[是]* 。

    ![應用程式閘道2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>設定 Http 設定
1.  選取 [ **Http 設定**]，然後按一下 [**新增**] 以新增 Http 設定。
1.  **以新的主機名稱覆寫：** 選取 *[是]*。
1.  **主機名稱覆寫**：選取 [**從後端目標挑選主機名稱**]。
1.  **使用自訂探查**：選取 *[是]* ，然後挑選上面建立的自訂探查。

    ![應用程式閘道3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>整合 Azure 春季雲端與 Azure Front

若要與 Azure 春季雲端服務整合並設定後端集區， 
1. **新增後端集**區。
1. 藉由新增主機來指定後端端點。

    ![Front 門板1](media/spring-cloud-load-balancers/front-door-1.png)

1.  指定**後端主機類型**做為*自訂主機*。
1.  在**後端主機名稱**中輸入 Azure 春季 Cloud 公用端點的 FQDN。
1.  接受**後端主機標頭**預設值，這與**後端主機名稱**相同。

    ![Front 門板2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>接下來的步驟
* [如何建立流量管理員](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [如何建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [如何建立 front](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
