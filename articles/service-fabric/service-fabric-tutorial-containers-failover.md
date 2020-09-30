---
title: 容器應用程式的容錯移轉和調整
description: 在本教學課程中，您會了解如何在 Azure Service Fabric 容器應用程式中處理容錯移轉。  亦了解如何調整在叢集中執行的容器與服務。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f51a195f9ad43c7facb7dc413a4da83fc6b202b7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531389"
---
# <a name="tutorial-demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>教學課程：示範透過 Service Fabric 容錯移轉與調整容器服務

本教學課程是一個系列課程的第三部分。 在本教學課程中，您會了解如何在 Service Fabric 容器應用程式中處理容錯移轉。 此外，您會了解如何調整容器。 在本教學課程中，您：

> [!div class="checklist"]
> * 了解 Service Fabric 叢集中的容器容錯移轉
> * 在應用程式中調整 Web 前端容器

## <a name="prerequisites"></a>Prerequisites

[第 2 部分](service-fabric-tutorial-package-containers.md)的應用程式正在使用中的 Service Fabric 叢集中執行。

## <a name="fail-over-a-container-in-a-cluster"></a>容錯移轉叢集中的容器

Service Fabric 可確保如果發生失敗，容器執行個體會自動移至叢集中的其他節點。 您也可以手動清空容器的節點，並將它們依正常程序移至叢集中的其他節點。 您有多種方式可調整您的服務，在此範例中，我們會使用 Service Fabric Explorer。

若要容錯移轉前端容器，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中的 **fabric:/TestContainer/azurevotefront** 節點，並展開分割區節點 (以 GUID 表示)。 請注意樹狀檢視中的節點名稱，會顯示哪些節點上的容器目前正在執行 - 例如 `_nodetype_1`
3. 展開樹狀檢視中的 [節點]  節點。 按一下正在執行容器之節點旁的省略符號 (三個點)。
4. 選擇 [重新啟動]  以重新啟動節點並確認重新啟動動作。 重新啟動會造成容器容錯移轉至叢集中的其他節點。

![noderestart][noderestart]

注意節點名稱 (指出前端容器執行的位置) 如何變更為叢集中的其他節點。 數分鐘後，您應該能夠再次瀏覽至該應用程式，並看見應用程式現在正在不同的節點上執行。

## <a name="scale-containers-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務

Service Fabric 容器可調整擴充到整個叢集，以容納服務上的負載。 您可以藉由變更叢集中執行的執行個體數目來調整容器。

若要調整 Web 前端，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中 **fabric:/TestContainer/azurevotefront** 節點旁的省略符號 (三個點)，然後選擇 [調整服務]  。

![sfxscale][sfxscale]

您現在可以選擇調整 Web 前端的執行個體數目。

1. 將數字變更為 **2**，然後按一下 [調整服務]  。
1. 按一下樹狀檢視中的 **fabric:/TestContainer/azurevotefront** 節點，並展開分割區節點 (以 GUID 表示)。

![sfxscaledone][sfxscaledone]

您現在可以看到服務有兩個執行個體。 在樹狀檢視中，您會看到執行個體在哪些節點上執行。

藉由這項簡單的管理工作，我們會加倍前端服務可用來處理使用者負載的資源。 請務必了解，您不需要多個服務執行個體，就能夠可靠地執行。 如果服務失敗，Service Fabric 可確保新的服務執行個體在叢集中執行。

## <a name="next-steps"></a>後續步驟

本教學課程示範了容器容錯移轉及調整應用程式。 已完成下列步驟：

> [!div class="checklist"]
> * 了解 Service Fabric 叢集中的容器容錯移轉
> * 在應用程式中調整 Web 前端容器

在本系列教學課程中，您學到了如何：
> [!div class="checklist"]
> * 建立容器映像
> * 將容器映像推送到 Azure Container Registry
> * 使用 Yeoman 封裝 Service Fabric 的容器
> * 建置與執行含容器的 Service Fabric 應用程式
> * 如何在 Service Fabric 中處理容錯移轉和調整

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png