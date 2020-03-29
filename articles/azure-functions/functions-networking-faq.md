---
title: 有關 Azure 函數中網路的常見問題
description: 回答使用 Azure 函數進行聯網的一些最常見問題和方案。
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409535"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>有關 Azure 函數中網路的常見問題

本文列出了有關 Azure 函數中網路的常見問題。 有關更全面的概述，請參閱[函數網路選項](functions-networking-options.md)。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何在函數中設置靜態 IP？

在應用服務環境中部署函數是當前為函數設置靜態入站和出站 IP 的唯一方法。 有關使用應用服務環境的詳細資訊，請從"創建"一文開始[，並使用具有應用服務環境的內部負載等化器](../app-service/environment/create-ilb-ase.md)。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何限制互聯網訪問我的功能？

您可以通過多種方式限制互聯網接入：

* [IP 限制](../app-service/app-service-ip-restrictions.md)：按 IP 範圍限制函數應用的入站流量。
    * 在 IP 限制下，您還可以配置[服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)，這將限制您的功能僅接受來自特定虛擬網路的入站流量。
* 刪除所有 HTTP 觸發器。 對於某些應用程式，只需避免 HTTP 觸發器並使用任何其他事件源來觸發函數就足夠了。

請記住，Azure 門戶編輯器需要直接存取正在運行的功能。 通過 Azure 門戶的任何代碼更改都需要用於流覽門戶的設備將其 IP 列入白名單。 但是，您仍然可以在平臺功能選項卡下使用任何具有網路限制的資訊。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>如何將功能應用限制為虛擬網路？

您可以使用[服務終結點](./functions-networking-options.md#private-site-access)將函數應用的**入站**流量限制為虛擬網路。 此配置仍然允許函數應用對 Internet 進行出站調用。

完全限制功能的唯一方法是使用內部負載均衡的應用服務環境，以便所有流量流經虛擬網路。 此選項將網站部署在虛擬網路內的專用基礎架構上，並通過虛擬網路發送所有觸發器和流量。 

有關使用應用服務環境的詳細資訊，請從"創建"一文開始[，並使用具有應用服務環境的內部負載等化器](../app-service/environment/create-ilb-ase.md)。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>如何從函數應用訪問虛擬網路中的資源？

您可以使用虛擬網路集成從正在運行的函數訪問虛擬網路中的資源。 有關詳細資訊，請參閱[虛擬網路集成](functions-networking-options.md#virtual-network-integration)。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何訪問受服務終結點保護的資源？

通過使用虛擬網路集成，可以從正在運行的函數訪問服務終結點安全的資源。 有關詳細資訊，請參閱[虛擬網路集成](functions-networking-options.md#virtual-network-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>如何從虛擬網路中的資源觸發函數？

您可以使用[服務終結點](./functions-networking-options.md#private-site-access)從虛擬網路調用 HTTP 觸發器。 

還可以通過將函數應用部署到高級計畫、應用服務方案或應用服務環境，從虛擬網路中的所有其他資源觸發函數。 有關詳細資訊[，請參閱非 HTTP 虛擬網路觸發器](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>如何在虛擬網路中部署函數應用？

部署到應用服務環境是創建完全位於虛擬網路中的函數應用的唯一方法。 有關將內部負載等化器與應用服務環境一起使用的詳細資訊，請從"創建"一文開始[，並使用具有應用服務環境的內部負載等化器](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)。

對於僅需要單向訪問虛擬網路資源或不太全面的網路隔離的情況，請參閱[功能網路概述](functions-networking-options.md)。

## <a name="next-steps"></a>後續步驟

要瞭解有關網路和功能的更多資訊，我們： 

* [按照有關開始虛擬網路集成的教程進行操作](./functions-create-vnet.md)
* [瞭解有關 Azure 函數中的網路選項的詳細資訊](./functions-networking-options.md)
* [瞭解有關虛擬網路集成應用服務和功能的更多資訊](../app-service/web-sites-integrate-with-vnet.md)
* [瞭解有關 Azure 中虛擬網路詳細資訊](../virtual-network/virtual-networks-overview.md)
* [使用應用服務環境啟用更多網路功能和控制](../app-service/environment/intro.md)
