---
title: 有關 Azure Functions 網路功能的常見問題
description: 解決一些最常見的問題，以及 Azure Functions 的網路功能案例的解答。
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75409535"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>有關 Azure Functions 網路功能的常見問題

本文列出 Azure Functions 中網路功能的常見問題。 如需更完整的總覽，請參閱函式[網路功能選項](functions-networking-options.md)。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何? 在函數中設定靜態 IP 嗎？

在 App Service 環境中部署函式目前是為您的函式提供靜態輸入和輸出 IP 的唯一方法。 如需使用 App Service 環境的詳細資訊，請從[建立和使用內部負載平衡器和 App Service 環境一](../app-service/environment/create-ilb-ase.md)文開始。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何? 限制對我的函式的網際網路存取？

您可以用幾種方式限制網際網路存取：

* [Ip 限制](../app-service/app-service-ip-restrictions.md)：依 ip 範圍限制函數應用程式的輸入流量。
    * 在 [IP 限制] 下，您也可以設定[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，這會限制您的函式只接受來自特定虛擬網路的輸入流量。
* 移除所有 HTTP 觸發程式。 對於某些應用程式，只需避免 HTTP 觸發程式，並使用任何其他事件來源來觸發您的函式就夠了。

請記住，Azure 入口網站編輯器需要直接存取您的執行中函式。 透過 Azure 入口網站的任何程式碼變更都需要您用來流覽入口網站的裝置，以將其 IP 列入允許清單。 但是您仍然可以使用 [平臺功能] 索引標籤下的任何專案，並提供網路限制。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>如何? 將我的函數應用程式限制為虛擬網路嗎？

您可以使用[服務端點](./functions-networking-options.md#private-site-access)，將函式應用程式的**輸入**流量限制為虛擬網路。 此設定仍然允許函數應用程式對網際網路進行輸出呼叫。

完全限制函式的唯一方法，就是透過虛擬網路的所有流量都是使用內部負載平衡的 App Service 環境。 此選項會將您的網站部署在虛擬網路內的專用基礎結構上，並透過虛擬網路傳送所有觸發程式和流量。 

如需使用 App Service 環境的詳細資訊，請從[建立和使用內部負載平衡器和 App Service 環境一](../app-service/environment/create-ilb-ase.md)文開始。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>如何從函數應用程式存取虛擬網路中的資源？

您可以使用虛擬網路整合，從執行中的功能存取虛擬網路中的資源。 如需詳細資訊，請參閱[虛擬網路整合](functions-networking-options.md#virtual-network-integration)。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何? 存取由服務端點保護的資源嗎？

藉由使用虛擬網路整合，您可以從執行中的函式存取服務端點保護的資源。 如需詳細資訊，請參閱[虛擬網路整合](functions-networking-options.md#virtual-network-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>如何從虛擬網路中的資源觸發函式？

您可以使用[服務端點](./functions-networking-options.md#private-site-access)，允許從虛擬網路呼叫 HTTP 觸發程式。 

您也可以將函式應用程式部署至 Premium 方案、App Service 方案或 App Service 環境，以從虛擬網路中的所有其他資源觸發函式。 如需詳細資訊，請參閱[非 HTTP 虛擬網路觸發](./functions-networking-options.md#virtual-network-triggers-non-http)程式

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>如何在虛擬網路中部署函數應用程式？

部署到 App Service 環境是建立完全位於虛擬網路內之函式應用程式的唯一方法。 如需將內部負載平衡器與 App Service 環境搭配使用的詳細資訊，請從[建立和使用內部負載平衡器和 App Service 環境一](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)文開始。

對於只需要單向存取虛擬網路資源，或較不全面的網路隔離的情況，請參閱函式[網路功能總覽](functions-networking-options.md)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解網路和功能： 

* [遵循有關開始使用虛擬網路整合的教學課程](./functions-create-vnet.md)
* [深入瞭解 Azure Functions 中的網路功能選項](./functions-networking-options.md)
* [深入瞭解虛擬網路與 App Service 和功能的整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [透過 App Service 環境啟用更多網路功能與控制](../app-service/environment/intro.md)
