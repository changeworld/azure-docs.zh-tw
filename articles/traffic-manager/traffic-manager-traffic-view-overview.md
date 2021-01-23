---
title: Azure 流量管理員中的流量檢視
description: 在此簡介中，您將瞭解流量管理員流量視圖的運作方式。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743044"
---
# <a name="traffic-manager-traffic-view"></a>流量管理員流量檢視

流量管理員為您提供 DNS (的網域名稱系統) 層級路由。 這項服務可讓您的終端使用者根據您所選擇的路由方法，導向至狀況良好的端點。 流量檢視提供流量管理員一個使用者群體 (在 DNS 解析程式資料粒度層級) 及其流量模式的檢視。 當您啟用流量檢視時，系統會處理這項資訊以提供可採取動作的深入資訊。 

藉由使用流量檢視，您可以：
- 了解您的使用者群體所在的位置 (取決於本機 DNS 解析程式層級資料粒度)。
- 檢視源自於這些區域的流量 (當作 Azure Traffic Manager 所處理的 DNS 觀察)。
- 洞悉這些使用者體驗的代表延遲是什麼。
- 深入探討從使用者群體之每個個體到您具有端點之 Azure 區域的特定流量模式。 

例如，您可以使用「流量查看」來瞭解哪些區域有大量的流量，但會受到較高的延遲。 然後，您可以使用此資訊來規劃新 Azure 區域的使用量擴充。 如此一來，您的使用者將會有較低的延遲體驗。

## <a name="how-traffic-view-works"></a>流量檢視的運作方式

流量視圖的運作方式是查看過去七天針對設定檔所收到的傳入查詢。 從傳入的查詢資訊，流量視圖會將用來代表使用者位置之 DNS 解析程式的來源 IP 解壓縮。 此資訊會在 DNS 解析程式層級群組在一起，以建立使用者基底區域。 流量管理員會維護 IP 位址的地理資訊。 然後，流量管理員會查看查詢所路由傳送的 Azure 區域，並為這些區域的使用者建立流量流程對應。
 
在下一個步驟中，流量管理員會將使用者基底區域與網路智慧延遲資料表相互關聯，以對應至 Azure 區域。 針對不同的終端使用者網路維護此表格，以瞭解當連接到 Azure 區域時，這些區域的使用者所遇到的平均延遲。 所有這些計算接著會在呈現給您之前，在每個本機 DNS 解析程式 IP 層級上合併。 您可以用各種方式使用該資訊。

流量視圖資料更新的頻率取決於多個內部服務變數。 不過，資料會每隔24小時更新一次。

>[!NOTE]
>流量檢視中所述的延遲是使用者與已連線 Azure 區域間的代表性延遲，不是 DNS 查詢延遲。 流量檢視對本機 DNS 解析程式與查詢路由前往的 Azure 區域之間的延遲進行最佳投入量估算，如果資料不足，則傳回的延遲將為 Null。 

## <a name="visual-overview"></a>視覺效果概觀

當您流覽至 [流量管理員] 頁面中的 [ **流量視圖** ] 區段時，您會看到地圖中有流量查看見解的重迭。 地圖會提供流量管理員設定檔的使用者群體和端點相關資訊。

![流量管理員流量視圖地理位置視圖][1]

### <a name="user-base-information"></a>使用者群體資訊

針對可使用位置資訊的本機 DNS 解析程式，這些解析程式會顯示在對應中。 DNS 解析程式的顏色表示使用者所經歷的延遲，使用者使用該 DNS 解析程式進行流量管理員查詢。

如果您將滑鼠停留在地圖中的 DNS 解析程式位置，則會顯示：
- DNS 解析程式的 IP 位址
- 流量管理員從此處看到的 DNS 查詢流量數量
- 路由 DNS 解析程式流量的端點，如同端點及 DNS 解析程式之間的路線 
- 從該位置至端點的平均延遲，以連接這兩者的線條顏色表示

### <a name="endpoint-information"></a>端點資訊

端點所在的 Azure 區域會顯示為地圖中的藍色點。 如果您的端點是外部的，且沒有 Azure 區域對應，則會顯示在地圖的頂端。 選取任何端點，以根據) 從流量導向至該端點的 DNS 解析程式， (查看不同的位置。 連接會顯示為端點與 DNS 解析程式位置之間的線條。 它們是根據該配對之間的代表性延遲來著色。 您可以看到端點的名稱，以及其執行所在的 Azure 區域。 此外，也會顯示此流量管理員設定檔所導向的要求總數量。


## <a name="tabular-listing-and-raw-data-download"></a>表格檢視和原始資料下載

您可以在 Azure 入口網站中以表格形式檢視流量檢視資料。 每個 DNS 解析程式 IP/端點配對都有一個專案，顯示：

* DNS 解析程式的 IP 位址。
* 名稱。
* 端點所在 Azure 區域的地理位置 (（如果有的話）) 。
* 與該端點的 DNS 解析程式相關聯的要求數量。
* 使用該 DNS (的代表性延遲（如果可用) ）。 

您也可以將流量檢視資料下載為 CSV 檔案，這可作為所選分析流程的一部分使用。

## <a name="billing"></a>計費

當您使用交通視圖時，系統會根據用來建立所顯示見解的資料點數目來向您收費。 目前，唯一使用的資料點類型是流量管理員設定檔收到的查詢。 如需價格的詳細資訊，請瀏覽[流量管理員定價分頁](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常見問題集

* [「流量檢視」有哪些功能？](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [我如何從使用「流量檢視」受益？](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [「流量檢視」與透過 Azure 監視器提供的「流量管理員」計量有何不同？](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [「流量檢視」是否使用「EDNS 用戶端子網路」資訊？](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [「流量檢視」會使用多少天的資料？](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [「流量檢視」如何處理外部端點？](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [我是否必須為訂用帳戶中的每個設定檔啟用「流量檢視」？](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [我是否可以關閉「流量檢視」？](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [「流量檢視」如何計費？](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>後續步驟

- 了解 [流量管理員的運作方式](traffic-manager-overview.md)
- 深入了解流量管理員支援的 [流量路由方法](traffic-manager-routing-methods.md)
- 了解如何 [建立流量管理員設定檔](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png