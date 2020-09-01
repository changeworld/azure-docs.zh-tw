---
title: 在 Azure Stack Edge 上管理計算網路以存取模組 |Microsoft Docs
description: 說明如何在您的 Azure Stack Edge 上擴充計算網路，以透過外部 IP 存取模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 8de3dbd754cd0cbef947d2cde44542f9f3f77a08
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083445"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-device"></a>使用 IoT Edge 模組在 Azure Stack Edge 裝置上執行 Kubernetes 無狀態應用程式

本文說明如何使用 IoT Edge 模組，在 Azure Stack Edge 裝置上部署無狀態應用程式。

若要部署無狀態應用程式，請執行下列步驟：

- 部署 IoT Edge 模組之前，請先確定已完成必要條件。
- 新增 IoT Edge 模組，以存取您 Azure Stack Edge 上的計算網路。
- 確認模組可以存取已啟用的網路介面。

在此操作說明文章中，您將使用 web 伺服器應用程式模組來示範此案例。

## <a name="prerequisites"></a>必要條件

開始之前，您需要：

- Azure Stack Edge 裝置。 請確認：

    - 系統會在裝置上設定計算網路設定。
    - 根據 [教學課程：啟用您的裝置](azure-stack-edge-gpu-deploy-activate.md)中的步驟來啟用裝置。
- 您已根據教學課程完成 **設定計算** 步驟：在裝置上的 [Azure Stack Edge 裝置上設定計算](azure-stack-edge-gpu-deploy-configure-compute.md) 。 您的裝置應該有相關聯的 IoT 中樞資源、IoT 裝置和 IoT Edge 裝置。


## <a name="add-webserver-app-module"></a>新增 web 伺服器應用程式模組

請採取下列步驟，在您的 Azure Stack Edge 裝置上新增 web 伺服器應用程式模組。

1. 在與您的裝置相關聯的 IoT 中樞資源中，移至 [ **自動裝置管理 > IoT Edge**。
1. 選取並按一下與您 Azure Stack Edge 裝置相關聯的 IoT Edge 裝置。 

    ![選取 IoT Edge 裝置](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. 選取 [設定模組]。 在 [ **設定裝置上的模組**] 上，選取 [ **+ 新增** ]，然後選取 [ **IoT Edge 模組**]。

    ![選取 IoT Edge 模組](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. 在 [ **新增 IoT Edge 模組**中：

    1. 指定您要部署之 web 伺服器應用程式模組的 **名稱** 。
    2. 在 [ **模組設定** ] 索引標籤下，為您的模組映射提供 **映射 URI** 。 會抓取符合所提供名稱和標記的模組。 在此情況下， `nginx:stable` 將會提取穩定的 nginx 映射 (從公用 [Docker 存放庫](https://hub.docker.com/_/nginx/)標記為穩定) 。

        ![新增 IoT Edge 模組](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. 在 [ **容器建立選項** ] 索引標籤中，貼上下列範例程式碼：  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        此設定可讓您透過 *TCP 通訊埠 8080 (上的* 計算網路 IP 來存取此模組，並使用預設的 web 伺服器埠 80) 。 選取 [新增]。

        ![在 IoT Edge 自訂模組分頁中指定埠資訊](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. 選取 [檢閱 + 建立]。 檢查模組詳細資料，然後選取 [ **建立**]。

## <a name="verify-module-access"></a>確認模組存取

1. 確認模組已成功部署且正在執行。 在 [**模組**] 索引標籤上，模組的執行時間狀態應該是 [**執行中]。**  

    ![確認模組狀態正在執行](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. 若要取得 web 伺服器應用程式的外部端點，請 [存取 Kubernetes 儀表板](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 
1. 在 [儀表板] 的左窗格中，依 **iotedge** 命名空間篩選。 移至 **> 服務的探索和負載平衡**。 在列出的服務清單下，找出 web 伺服器應用程式模組的外部端點。 

    ![連接到外部端點的 web 伺服器應用程式](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 選取外部端點以開啟新的瀏覽器視窗。

    您應該會看到 web 伺服器應用程式正在執行。

    ![確認透過指定的埠連接到模組](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何透過 IoT Edge 模組公開具狀態應用程式<!--insert link-->.
