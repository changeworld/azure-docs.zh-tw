---
title: 管理 Azure Stack Edge Pro 上的計算網路以存取模組 |Microsoft Docs
description: 說明如何擴充 Azure Stack Edge Pro 上的計算網路，以透過外部 IP 存取模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894123"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>在您的 Azure Stack Edge Pro 上啟用計算網路

本文說明 Azure Stack Edge Pro 上執行的模組如何存取裝置上啟用的計算網路。

若要設定網路，您將採取下列步驟：

- 在您的 Azure Stack Edge Pro 裝置上啟用網路介面以進行計算
- 新增模組以存取您 Azure Stack Edge Pro 上的計算網路
- 確認模組可以存取已啟用的網路介面

在本教學課程中，您將使用 web 伺服器應用程式模組來示範此案例。

## <a name="prerequisites"></a>必要條件

開始之前，您需要：

- 已完成裝置設定的 Azure Stack Edge Pro 裝置。
- 您已根據教學課程完成 **設定計算** 步驟：在您的裝置上 [使用 Azure Stack Edge Pro 來轉換資料](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) 。 您的裝置應該有相關聯的 IoT 中樞資源、IoT 裝置和 IoT Edge 裝置。

## <a name="enable-network-interface-for-compute"></a>啟用網路介面以進行計算

若要透過外部網路存取在您的裝置上執行的模組，您必須將 IP 位址指派給裝置上的網路介面。 您可以從本機 Web UI 管理這些計算設定。

請在本機 Web UI 上採取下列步驟來設定計算的設定值。

1. 在本機 Web UI 中，移至 [組態] > [計算設定]****。  

2. **啟用** 您想要用來連線到您將在裝置上執行之計算模組的網路介面。

    - 如果使用靜態 IP 位址，請輸入網路介面的 IP 位址。
    - 如果使用 DHCP，系統會自動指派 IP 位址。 此範例會使用 DHCP。

    ![啟用計算設定1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 選取 [套用]**** 來套用設定。 請記下指派給網路介面的 IP 位址（如果使用 DHCP）。

    ![啟用計算設定](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>新增 web 伺服器應用程式模組

請採取下列步驟，在您的 Azure Stack Edge Pro 裝置上新增 web 伺服器應用程式模組。

1. 移至與您的 Azure Stack Edge Pro 裝置相關聯的 IoT 中樞資源，然後選取 [ **IoT Edge 裝置**]。
2. 選取與您 Azure Stack Edge Pro 裝置相關聯的 IoT Edge 裝置。 在 [ **裝置詳細資料**] 上，選取 [ **設定模組**]。 在 [ **新增模組**] 中，選取 [ **+ 新增** ]，然後選取 [ **IoT Edge 模組**]。
3. 在 [ **IoT Edge 自訂模組** ] 分頁中：

    1. 指定您要部署之 web 伺服器應用程式模組的 **名稱** 。
    2. 為您的模組映射提供 **映射 URI** 。 會抓取符合所提供名稱和標記的模組。 在此情況下， `nginx:stable` 將會提取穩定的 nginx 映射 (從公用 [Docker 存放庫](https://hub.docker.com/_/nginx/)標記為穩定) 。
    3. 在 [ **容器建立選項**] 中，貼上下列範例程式碼：  

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

        此設定可讓您透過 *TCP 通訊埠 8080 (上的* 計算網路 IP 來存取此模組，並使用預設的 web 伺服器埠 80) 。

        ![在 IoT Edge 自訂模組分頁中指定埠資訊](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. 選取 [儲存]****。

## <a name="verify-module-access"></a>確認模組存取

1. 確認模組已成功部署且正在執行。 在 [**裝置詳細資料**] 頁面的 [**模組**] 索引標籤上，模組的執行時間狀態應該是 [**執行中]。**  
2. 連接至 web 伺服器應用程式模組。 開啟瀏覽器視窗，然後輸入：

    `http://<compute-network-IP-address>:8080`

    您應該會看到 web 伺服器應用程式正在執行。

    ![確認透過指定的埠連接到模組](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[透過 Azure 入口網站管理使用者](azure-stack-edge-manage-users.md)。
