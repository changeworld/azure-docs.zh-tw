---
title: 部署 Azure IoT Edge 工作負載 (預覽)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 部署 Azure IoT Edge 工作負載
keywords: Kubernetes、Arc、Azure、K8s、容器
ms.openlocfilehash: 2a688a221b2f4865d51bca2ebf4aaa0b1f714290
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193782"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>部署 Azure IoT Edge 工作負載 (預覽)

## <a name="overview"></a>概觀

Azure Arc 和 Azure IoT Edge 之間的互補性很好。 Azure Arc 為叢集操作員提供設定叢集基礎元件的機制，以及套用和強制執行叢集原則。 而 IoT Edge 可讓應用程式操作員利用方便的雲端擷取和雙向的通訊基元，遠端部署及管理大規模的工作負載。 下圖說明這一點：

![IoT Arc 設定](./media/edge-arc.png)

## <a name="pre-requisites"></a>必要條件

* [註冊 IoT Edge 裝置](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#register-an-iot-edge-device)和[部署模擬的溫度感應器模組](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#deploy-a-module)。 請務必記下裝置的連接字串。

* 使用 [[適用於 Kubernetes 的 IoT Edge 支援]](https://aka.ms/edgek8sdoc)，透過 Azure Arc 的 Flux 運算子以進行部署。

* 針對 IoT Edge Helm 圖表下載 [**values.yaml**](https://github.com/Azure/iotedge/blob/master/kubernetes/charts/edge-kubernetes/values.yaml) 檔案，並使用步驟 1 記錄的預留位置取代檔案結尾的 **deviceConnectionString** 預留位置。 您可以視需要設定任何其他支援的圖表安裝選項。 建立 IoT Edge 工作負載的命名空間，並在其中建立密碼：

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    您也可以使用[叢集設定範例](./use-gitops-connected-cluster.md)。

## <a name="connect-a-cluster"></a>連線叢集

使用 `az` CLI `connectedk8s` 擴充，將 Kubernetes 叢集連線至 Azure Arc：

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>為 IoT Edge 建立設定

範例存放庫： https://github.com/veyalla/edgearc

此存放庫會指向 IoT Edge Helm 圖表，並參考在必要條件區段中建立的秘密。

1. 使用 `az` CLI `k8sconfiguration` 擴充來建立設定，以將連線的叢集連結至 git 存放庫：

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    在一兩分鐘內，您應該會看見 IoT Edge 工作負載模組部署到您叢集中的 `iotedge` 命名空間。 您可以在該命名空間中檢視 `SimulatedTemperatureSensor` Pod 的記錄，以查看產生的範例值。 您也可以使用[適用於 Visual Studio Code 的 Azure IoT 中樞工具組擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)，查看送達 IoT 中樞的訊息。

## <a name="cleanup"></a>清理

您可以使用下列項目來移除設定：

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>後續步驟

[使用 Azure 原則來控管叢集設定](./use-azure-policy.md)
