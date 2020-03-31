---
title: 如何在庫伯奈斯上安裝物聯網邊緣 |微軟文檔
description: 瞭解如何使用本地開發群集環境在 Kubernetes 上安裝 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471280"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在庫伯奈斯安裝 IoT 邊緣（預覽版）

IoT Edge 可以與 Kubernetes 集成，將其用作彈性、高度可用的基礎結構層。 以下是此支援適用于高級 IoT Edge 解決方案的位置：

![k8s 簡介](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>此集成的一個很好的心理模型是認為 Kubernetes 是 IoT Edge 應用程式除了 Linux 和 Windows 之外可以運行的另一個操作環境。

## <a name="architecture"></a>架構 
在庫伯奈斯，IoT 邊緣為邊緣工作負載部署提供*自訂資源定義*（CRD）。 IoT 邊緣代理承擔*CRD 控制器*的角色，該控制器將雲託管的所需狀態與本地群集狀態協調。

模組存留期由 Kubernetes 調度程式管理，該調度程式維護模組可用性並選擇其放置位置。 IoT Edge 管理頂部運行的邊緣應用程式平臺，持續協調 IoT 中心中指定的所需狀態與邊緣群集上的狀態。 應用模型仍然是基於 IoT Edge 模組和路由的熟悉模型。 IoT 邊緣代理控制器將 IoT Edge 的應用程式模型*自動*翻譯到 Kubernetes 本機構造，如 pod、部署、服務等。

下面是一個高級體系結構圖：

![庫伯內斯拱門](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

邊緣部署的每個元件都限定到特定于設備的 Kubernetes 命名空間，從而可以在多個邊緣設備及其部署之間共用相同的群集資源。

>[!NOTE]
>庫伯奈斯上的物聯網邊緣處於[公共預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="tutorials-and-references"></a>教程和參考資料 

有關詳細資訊，請參閱[Kubernetes 上的 IoT 邊緣預覽文檔迷你網站](https://aka.ms/edgek8sdoc)，包括深入教程和參考。
