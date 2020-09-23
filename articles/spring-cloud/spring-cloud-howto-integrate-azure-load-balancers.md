---
title: 教學課程-將 Azure 春天雲端與 Azure 負載平衡解決方案整合
description: 如何整合 Azure 春天雲端與 Azure 負載平衡解決方案
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 02772f153cdda7e3f3c866c727d589e755e19033
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906941"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>整合 Azure Spring Cloud 與 Azure 負載平衡解決方案

本文**適用于：** ✔️ JAVA ✔️ C#

Azure 春季 Cloud 支援 Azure 上的微服務。  提高企業需求的多個資料中心，可以管理多個 Azure 春季雲端實例。

Azure 已提供不同的負載平衡解決方案。 有三個選項可整合 Azure 春天雲端與 Azure 負載平衡解決方案：

1.  整合 Azure 春季 Cloud 與 Azure 流量管理員
2.  整合 Azure 春季 Cloud 與 Azure App 閘道
3.  整合 Azure 春季 Cloud 與 Azure Front Door

## <a name="prerequisites"></a>必要條件

* Azure 春季雲端： [如何建立 azure 春季雲端服務](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart)
* Azure 流量管理員： [如何建立流量管理員](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure App 閘道： [如何建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Azure Front Door： [如何建立前門](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>整合 Azure 春季 Cloud 與 Azure 流量管理員

若要整合 Azure 春季 cloud 與流量管理員，請將其公用端點新增為流量管理員的端點，然後為流量管理員和 Azure 春季雲端設定自訂網域。

### <a name="add-endpoint-in-traffic-manager"></a>在流量管理員中新增端點
在流量管理員中新增端點：
1.  將 **類型** 指定為 *外部端點*。
1.  輸入每個 Azure 春季雲端公用端點 (FQDN) 的完整功能變數名稱。
1. 按一下 [確定]  。

    ![流量管理員 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ 流量管理員2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>設定自訂網域
完成設定：
1.  登入網域提供者的網站，並從您的自訂網域建立 CNAME 記錄對應至流量管理員的 Azure 預設功能變數名稱。
1.  請依照指示 [，將自訂網域新增至 Azure 春季雲端](spring-cloud-tutorial-custom-domain.md)。
1. 將上述的自訂網域系結新增至 Azure 春季 cloud 對應的 app service，並在該處上傳 SSL 憑證。

    ![流量管理員3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>整合 Azure 春季 Cloud 與 Azure App 閘道

若要與 Azure 春季雲端服務整合，請完成下列設定：

### <a name="configure-backend-pool"></a>設定後端集區
1. 將 [ **目標型別** ] 指定為 [ *IP 位址* ] 或 [ *FQDN*]。
1. 輸入您的 Azure 春季雲端公用端點。

    ![應用程式閘道1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>新增自訂探查
1. 選取 [ **健康情況探查** ]，然後 **新增** 以開啟 [自訂 **探查** ] 對話方塊。 
1. 重點是針對 [**從後端 HTTP 設定挑選主機名稱**] 選項選取 *[是]* 。

    ![應用程式閘道2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>設定 Http 設定
1.  選取 [ **Http 設定** ]，然後 **新增** 以新增 HTTP 設定。
1.  **以新的主機名稱覆寫：** 選取 *[是]*。
1.  **主機名稱覆寫**：選取 [ **從後端目標挑選主機名稱**]。
1.  **使用自訂探查**：選取 *[是]* ，然後挑選上面建立的自訂探查。

    ![應用程式閘道3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>整合 Azure 春季 Cloud 與 Azure Front Door

若要與 Azure 春季雲端服務整合並設定後端集區， 
1. **新增後端集**區。
1. 藉由新增主機來指定後端端點。

    ![Front Door 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  將 **後端主機類型** 指定為 *自訂主機*。
1.  **後端主機名稱**中您的 Azure 春季雲端公用端點的輸入 FQDN。
1.  接受 **後端主機標頭** 預設值（與 **後端主機名稱**相同）。

    ![Front Door 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>下一步
* [如何建立流量管理員](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [如何建立應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [如何建立前門](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
