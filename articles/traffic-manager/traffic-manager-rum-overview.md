---
title: Azure 流量管理員中的實際使用者度量
description: 在此簡介中，您將瞭解 Azure 流量管理員實際使用者度量的工作方式。
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938440"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>流量管理員實際使用者度量概觀

當您設定流量管理員設定檔使用效能路由方式時，服務會尋找 DNS 查詢要求的來源位置，進行路由決策以將這些要求者導向至給予他們最低延遲的 Azure 區域。 這個作業會透過利用流量管理員為不同使用者網路維護的網路延遲智慧來完成。

實際使用者度量可讓您從使用者使用的用戶端應用程式測量 Azure 區域的網路延遲度量，並且讓流量管理員在進行路由決策時一併考量該資訊。 藉由選擇使用實際使用者度量，您可以增加路由要求的精確度，這些要求是來自您的使用者所在的網路。 

## <a name="how-real-user-measurements-work"></a>實際使用者度量的運作方式

實際使用者度量的運作方式是從使用所在的使用者網路中看到時，給予 Azure 區域的用戶端應用程式測量延遲。 舉例來說，如果您有讓不同位置之使用者存取的網頁 (例如，北美區域)，您可以將實際使用者度量與效能路由方法結合使用，以使其到達裝載伺服器應用程式的最佳 Azure 區域。

一開始會將 Azure 提供的 JavaScript (其中具有獨特索引鍵) 內嵌至您的網頁。 完成之後，每當使用者造訪該網頁，JavaScript 就會查詢流量管理員來取得它應該測量之 Azure 區域的相關資訊。 服務會傳回一組端點給指令碼，然後藉由下載裝載在這些 Azure 區域的單一像素映像，並且註明要求傳送的時間與收到第一個位元組的時間之間的延遲，來連續測量這些區域。 這些度量會接著回報給「流量管理員」服務。

經過一段時間，這個作業會在許多網路之間發生許多次，讓流量管理員取得更多使用者所在之網路的延遲特性相關精確資訊。 這項資訊開始納入流量管理員進行的路由決策中。 如此一來，就會根據傳送的實際使用者度量，增加這些決策的精確度。

當您使用實際使用者度量時，您會根據傳送給流量管理員的度量數目收到帳單。 如需價格的詳細資訊，請瀏覽[流量管理員定價分頁](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常見問題集

* [使用「實際使用者度量」有哪些優點？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [我是否可以將「實際使用者度量」與非 Azure 區域搭配使用？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [哪個路由方法可從「實際使用者度量」受益？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [我是否需要為每個設定檔個別啟用「實際使用者度量」？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [如何關閉我訂用帳戶的「實際使用者度量」？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [我是否可以將「實際使用者度量」與網頁以外的用戶端應用程式搭配使用？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [每次轉譯已啟用「實際使用者度量」功能的網頁時，會執行多少個度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [「實際使用者度量」在我的網頁中執行前是否會有延遲？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [是否可以只搭配我想要測量的 Azure 區域來使用實際使用者度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [我是否可以將度量數目限制為特定數目？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [我是否可以查看用戶端應用程式在「實際使用者度量」過程中所進行的度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [我是否可以修改「流量管理員」所提供的度量指令碼？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [其他人是否可能看到我搭配「實際使用者度量」使用的金鑰？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [其他人是否可能濫用我的 RUM 金鑰？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [我是否需要將度量 JavaScript 放在所有網頁中？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [如果我使用「實際使用者度量」，「流量管理員」是否可以識別出使用者的相關資訊？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [測量「實際使用者度量」的網頁是否必須使用「流量管理員」來進行路由？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [我是否必須在 Azure 區域裝載任何服務，才能搭配使用「實際使用者度量」？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [使用「實際使用者度量」時，我的 Azure 頻寬使用量是否會增加？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>後續步驟
- 了解如何使用[具有網頁的實際使用者度量](traffic-manager-create-rum-web-pages.md)
- 了解 [流量管理員的運作方式](traffic-manager-overview.md)
- 深入了解 [Mobile Center](https://docs.microsoft.com/mobile-center/)
- 深入了解流量管理員支援的 [流量路由方法](traffic-manager-routing-methods.md)
- 了解如何 [建立流量管理員設定檔](traffic-manager-create-profile.md)

