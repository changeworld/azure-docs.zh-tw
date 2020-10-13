---
title: 使用流量管理員跨多個端點進行容錯移轉
titleSuffix: Azure Content Delivery Network
description: 瞭解如何使用 Azure 流量管理員設定跨多個 Azure 內容傳遞網路端點的容錯移轉。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b75643d0d526bae4d7b2879dffab3d90dbcbe1eb
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875864"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>使用 Azure 流量管理員跨多個端點進行容錯移轉

當您設定 Azure 內容傳遞網路 (CDN) 時，可以針對您的需求選取最佳提供者和定價層。 

具有全域分散式基礎結構的 Azure CDN，預設會建立本機和異地備援與全域負載平衡，來改善服務可用性和效能。 

如果有位置無法提供內容，則會自動將要求路由傳送到另一個位置。 最佳狀態 (POP) 用來服務每個用戶端要求。 自動路由是以要求位置和伺服器負載的因素為基礎。
 
如果您有多個 CDN 設定檔，可以使用 Azure 流量管理員進一步改善可用性和效能。 

使用 Azure 流量管理員搭配 Azure CDN，以在多個 CDN 端點之間進行負載平衡：
 
* 容錯移轉
* 異地負載平衡 

在典型的容錯移轉案例中，所有用戶端要求都會導向至主要 CDN 設定檔。 

如果設定檔無法使用，則會將要求導向至次要設定檔。  當您的主要設定檔重新上線時，要求會繼續進行。

以這種方式使用 Azure 流量管理員，可確保您的 Web 應用程式永遠可用。 

本文提供的指引，以及如何使用設定檔設定容錯移轉的範例： 

* **來自 Verizon 的 Azure CDN 標準**
* **來自 Akamai 的 Azure CDN 標準**

也支援**來自 Microsoft 的 AZURE CDN** 。

## <a name="create-azure-cdn-profiles"></a>建立 Azure CDN 設定檔
建立具有不同提供者的兩個或多個 Azure CDN 設定檔和端點。

1. 建立兩個 CDN 設定檔：
    * **來自 Verizon 的 Azure CDN 標準**
    * **來自 Akamai 的 Azure CDN 標準** 

    遵循 [建立新的 CDN 設定檔](cdn-create-new-endpoint.md#create-a-new-cdn-profile)中的步驟建立設定檔。
 
   ![CDN 多重設定檔](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 在每個新的設定檔中，請遵循[建立新的 CDN 端點](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)中的步驟，建立至少一個端點。

## <a name="create-traffic-manager-profile"></a>建立流量管理員設定檔
建立 Azure 流量管理員設定檔，並在您的 CDN 端點之間設定負載平衡。 

1. 藉由遵循[建立流量管理員設定檔](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)中的步驟，建立 Azure 流量管理員設定檔。 

    * **路由方法**，請選取 [ **優先權**]。

2. 藉由遵循[新增流量管理員端點](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)中的步驟，在您的流量管理員設定檔中新增 CDN 端點

    * **輸入**，選取 **外部端點**。
    * [**優先順序**] 中，輸入數位。

    例如，建立 cdndemo101akamai.azureedge.net****，其優先順序為 1****，建立 cdndemo101verizon.azureedge.net****，其優先順序為 2****。

   ![CDN 流量管理員端點](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>在 Azure CDN 和 Azure 流量管理員上設定自訂網域
設定 CDN 和流量管理員設定檔之後，請遵循下列步驟來新增 DNS 對應，並向 CDN 端點註冊自訂網域。 例如，自訂網域名稱是 cdndemo101.dustydogpetcare.online****。

1. 請移至自訂網域網域提供者的網站，例如 GoDaddy，然後建立兩個 DNS CNAME 項目。 

    a. 針對第一個 CNAME 項目，將具有 cdnverify 子網域的自訂網域對應至 CDN 端點。 此項目是必要步驟，如此才能向您在步驟 2 中新增至流量管理員的 CDN 端點註冊自訂網域。

      例如： 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 針對第二個 CNAME 項目，將沒有 cdnverify 子網域的自訂網域對應至 CDN 端點。 此項目會將自訂網域對應至流量管理員。 

      例如： 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > 如果您的網域目前為使用中且無法中斷，請最後再執行這個步驟。 請確認 CDN 端點和流量管理員網域目前為使用中，然後再將自訂網域 DNS 更新至流量管理員。
    >


2.  從您的 Azure CDN 設定檔中，選取第一個 CDN 端點 (Akamai)。 選取 [**新增自訂網域**] 和 [輸入**cdndemo101] dustydogpetcare。** 確認用來驗證自訂網域的核取記號為綠色。 

    Azure CDN 會使用 cdnverify**** 子網域來驗證 DNS 對應，以完成此註冊程序。 如需詳細資訊，請參閱[建立 CNAME DNS 記錄](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)。 這個步驟可讓 Azure CDN 辨識自訂網域，以便回應其要求。
    
    > [!NOTE]
    > 若要從 Akamai 設定檔啟用 **AZURE CDN** 上的 TLS，您必須將自訂網域直接 cname 至您的端點。 尚不支援啟用 TLS 的 cdnverify。 
    >

3.  返回您自訂網域的網域提供者的網站。 更新您建立的第一個 DNS 對應。 將自訂網域對應至第二個 CDN 端點。
                             
    例如： 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. 從您的 Azure CDN 設定檔中，選取第二個 CDN 端點 (Verizon)，然後重複步驟 2。 選取 [ **新增自訂網域**]，然後輸入 **cdndemo101 dustydogpetcare. online**。
 
完成這些步驟之後，具有容錯移轉功能的多 CDN 服務會設定 Azure 流量管理員。 

您可以從自訂網域存取測試 Url。 

若要測試功能，請停用主要 CDN 端點，並確認該要求已正確移往次要 CDN 端點。 

## <a name="next-steps"></a>後續步驟
您可以設定其他路由方法（例如地理）來平衡不同 CDN 端點之間的負載。 

如需詳細資訊，請參閱[使用流量管理員來設定地理流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。



