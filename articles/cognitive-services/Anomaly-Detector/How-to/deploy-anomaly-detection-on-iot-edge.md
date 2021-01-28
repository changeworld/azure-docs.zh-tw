---
title: 在 IoT Edge 上執行異常偵測器
titleSuffix: Azure Cognitive Services
description: 將異常偵測器模組部署至 IoT Edge。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936281"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>將異常偵測器模組部署到 IoT Edge

瞭解如何將認知服務 [異常](../anomaly-detector-container-howto.md) 偵測器模組部署到 IoT Edge 裝置。 一旦部署到 IoT Edge 中，模組會與其他模組一起在 IoT Edge 中執行，作為容器實例。 它會公開與在標準 docker 容器環境中執行的異常偵測器容器實例完全相同的 Api。 

## <a name="prerequisites"></a>先決條件

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。
* 安裝 [Azure CLI](/cli/azure/install-azure-cli)。
* [IoT 中樞](../../../iot-hub/iot-hub-create-through-portal.md)和[IoT Edge](../../../iot-edge/quickstart-linux.md)裝置。

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>將異常偵測模組部署到邊緣

1. 在 Azure 入口網站中， **于 IoT Edge 上輸入異常** 偵測器，並開啟 Azure Marketplace 結果。
2. 它會帶您前往 [IoT Edge 模組頁面的 Azure 入口網站目標裝置](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)。 提供下列必要資訊。

    1. 選取您的訂用帳戶。

    1. 選取您的 IoT 中樞。

    1. 選取 [ **尋找裝置** ] 和 [尋找 IoT Edge 裝置]。

3. 選取 [建立] 按鈕。

4. 選取 [ **AnomalyDetectoronIoTEdge** ] 模組。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="IoT Edge 模組使用者介面的影像，並以紅色方塊反白顯示 [AnomalyDetectoronIoTEdge] 連結，表示這是要選取的專案。":::

5. 巡覽至 [環境變數]，並提供下列資訊。

    1.  保留 **Eula** 的值接受。

    1. 在 [帳單] 處填入認知服務端點。

    1. 在 [Apikey] 中填入認知服務 API 金鑰。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="在需要針對端點和 API 金鑰填入值的區域周圍使用紅色方塊的環境變數":::

6. 選取 [更新]

7. 選取 **[下一步：路由]** 以定義您的路由。 請將來自所有模組的全部訊息定義為移至 Azure IoT 中樞。

8. 選取 [下一步：檢閱 + 建立]。 您可以預覽 JSON 檔案，該檔案會定義所有部署至 IoT Edge 裝置的模組。
    
9. 選取 [建立] 以開始模組部署。

10. 完成模組部署後，請返回 IoT 中樞的 [IoT Edge] 頁面。 從 IoT Edge 裝置清單中選取您的裝置，可查看其詳細資料。

11. 向下捲動並查看列出的模組。 檢查新模組的執行時間狀態是否正在執行。 

若要疑難排解 IoT Edge 裝置的執行時間狀態，請參閱 [疑難排解指南](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>測試 IoT Edge 裝置上的異常偵測器

您要對具有執行中 Azure 認知服務容器的 Azure IoT Edge 裝置進行 HTTP 呼叫。 容器會提供以 REST 為基礎的端點 Api。 `http://<your-edge-device-ipaddress>:5000`針對模組 api 使用主機（）。

如果您的 edge 裝置尚未允許埠5000上的輸入通訊，您將需要建立新的 **輸入連接埠規則**。 

針對 Azure VM，這可以在 [**虛擬機器**  >  **設定**  >  **網路**  >  **輸入連接埠規則**  >  **新增輸入連接埠規則**] 下設定。

有數種方式可驗證模組是否正在執行。 找出有問題的邊緣裝置的 *外部 IP* 位址和公開端口，然後開啟您最愛的網頁瀏覽器。 使用下列各種要求 Url 來驗證容器是否正在執行。 以下列出的範例要求 Url 為 `http://<your-edge-device-ipaddress:5000` ，但您的特定容器可能會有所不同。 請記住，您需要使用 edge 裝置的 *外部 IP* 位址。

| 要求 URL | 用途 |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | 容器會提供首頁。 |
| `http://<your-edge-device-ipaddress>:5000/status` | 此外，也會向 GET 要求，這會確認用來啟動容器的 api 金鑰是否有效，而不會導致端點查詢。 此要求可用來進行 Kubernetes [活躍度和整備度探查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) \(英文\)。 |
| `http://<your-edge-device-ipaddress>:5000/swagger` | 容器會為端點提供一組完整的文件和 [立即試用] 功能。 使用此功能，您可以將自己的設定輸入至以 Web 為基礎的 HTML 表單並進行查詢，而無須撰寫任何程式碼。 當查詢傳回時，會提供範例 CURL 命令來示範所需的 HTTP 標頭和本文格式。 |

![容器的首頁](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>後續步驟

* 查看 [安裝和執行](../anomaly-detector-container-configuration.md) 容器以提取容器映射並執行容器
* 檢閱[設定容器](../anomaly-detector-container-configuration.md)以了解組態設定
* [深入瞭解異常偵測器 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
