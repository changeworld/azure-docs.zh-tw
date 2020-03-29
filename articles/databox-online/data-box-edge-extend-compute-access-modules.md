---
title: 在 Azure 資料框邊緣管理計算網路以訪問模組*微軟文檔
description: 描述如何擴展資料框邊緣的計算網路，以便通過外部 IP 訪問模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917231"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>在 Azure 資料框邊緣啟用計算網路

本文介紹在 Azure 資料框邊緣上運行的模組如何訪問設備上啟用的計算網路。

要配置網路，您需要執行以下步驟：

- 在資料盒邊緣設備上啟用網路介面進行計算
- 添加模組以訪問資料框邊緣的計算網路
- 驗證模組可以訪問啟用的網路介面

在本教程中，您將使用 Webserver 應用模組來演示該方案。

## <a name="prerequisites"></a>Prerequisites

開始之前，您需要：

- 已完成設備設置的資料框邊緣設備。
- 您已完成按照教程**配置計算**步驟[：在設備上使用 Azure 資料框邊緣轉換資料](data-box-edge-deploy-configure-compute-advanced.md#configure-compute)。 您的設備應具有關聯的 IoT 中心資源、IoT 設備和 IoT 邊緣設備。

## <a name="enable-network-interface-for-compute"></a>啟用用於計算的網路介面

要通過外部網路訪問設備上運行的模組，您需要將 IP 位址分配給設備上的網路介面。 您可以從本機 Web UI 管理這些計算設定。

請在本機 Web UI 上採取下列步驟來設定計算的設定值。

1. 在本機 Web UI 中，移至 [組態] > [計算設定]****。  

2. **啟用**要用於連接到要在設備上運行的計算模組的網路介面。

    - 如果使用靜態 IP 位址，請輸入網路介面的 IP 位址。
    - 如果使用 DHCP，則會自動分配 IP 位址。 此示例使用 DHCP。

    ![啟用計算設置 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 選取 [套用]**** 來套用設定。 如果使用 DHCP，請注意分配給網路介面的 IP 位址。

    ![啟用計算設定](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>添加 Web 服務器應用模組

執行以下步驟，在資料盒邊緣設備上添加 Web 服務器應用模組。

1. 轉到與資料框邊緣設備關聯的 IoT 中心資源，然後選擇**IoT 邊緣設備**。
2. 選擇與資料框邊緣設備關聯的 IoT 邊緣設備。 在**設備詳細資訊**上，選擇 **"設置模組**"。 在**添加模組**時，選擇 **+添加**，然後選擇**IoT 邊緣模組**。
3. 在**IoT 邊緣自訂模組**邊邊邊邊邊邊選項卡中：

    1. 為要部署的 Web 服務器應用模組指定**名稱**。
    2. 為您的模組映射提供**圖像 URI。** 檢索與提供的名稱和標記匹配的模組。 在這種情況下，`nginx:stable`將從公共[Docker 存儲庫](https://hub.docker.com/_/nginx/)中提取一個穩定的 nginx 映射（標記為穩定）。
    3. 在**容器創建選項**中，粘貼以下示例代碼：  

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

        此配置允許您在 TCP 埠 8080 上通過*HTTP（* 預設 Web 服務器埠為 80）使用計算網路 IP 訪問模組。

        ![在 IoT 邊緣自訂模組邊選項卡中指定埠資訊](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. 選取 [儲存]****。

## <a name="verify-module-access"></a>驗證模組訪問

1. 驗證模組已成功部署且正在運行。 在"**設備詳細資訊**"頁上，在 **"模組"** 選項卡上，模組的運行時狀態應**運行**。  
2. 連接到 Web 服務器應用模組。 打開瀏覽器視窗並鍵入：

    `http://<compute-network-IP-address>:8080`

    您應該看到 Webserver 應用正在運行。

    ![通過指定埠驗證與模組的連接](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[透過 Azure 入口網站管理使用者](data-box-edge-manage-users.md)。
