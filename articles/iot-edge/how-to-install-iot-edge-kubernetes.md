---
title: 如何在 Kubernetes 上安裝 IoT Edge |Microsoft Docs
description: 瞭解如何在 Kubernetes 上使用本機開發叢集環境安裝 IoT Edge
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79471280"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>如何在 Kubernetes (Preview) 上安裝 IoT Edge

IoT Edge 可以使用它來與 Kubernetes 整合，以作為具復原性、高可用性的基礎結構層。 以下是這項支援在高階 IoT Edge 解決方案中的適合位置：

![k8s 簡介](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>這項整合的好精神模型是將 Kubernetes 視為另一個作業環境，IoT Edge 應用程式除了 Linux 和 Windows 之外還可以執行。

## <a name="architecture"></a>架構 
在 Kubernetes 上，IoT Edge 為 Edge 工作負載部署 (.CRD) 提供 *自訂資源定義* 。 IoT Edge 代理程式會假設  *.crd 控制器* 的角色會協調受雲端管理的預期狀態與本機叢集狀態。

模組存留期是由 Kubernetes 排程器所管理，可維持模組可用性並選擇其放置。 IoT Edge 會管理在頂端執行的 Edge 應用程式平臺，並持續協調 IoT 中樞內所指定的所需狀態與 Edge 叢集上的狀態。 應用程式模型仍是以 IoT Edge 模組和路由為基礎的熟悉模型。 IoT Edge Agent 控制器會對 Kubernetes 原生結構（例如 pod、部署、服務等）執行 *自動* 轉譯 IoT Edge 的應用程式模型。

以下是高層級架構圖：

![kubernetes 架構](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Edge 部署的每個元件的範圍是裝置專屬的 Kubernetes 命名空間，因此可以在多個邊緣裝置和其部署之間共用相同的叢集資源。

>[!NOTE]
>Kubernetes 上的 IoT Edge 目前為 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

## <a name="tutorials-and-references"></a>教學課程和參考 

如需詳細資訊，請參閱 [Kubernetes preview 檔迷你網站上的 IoT Edge](https://aka.ms/edgek8sdoc) ，包括深入的教學課程和參考。
