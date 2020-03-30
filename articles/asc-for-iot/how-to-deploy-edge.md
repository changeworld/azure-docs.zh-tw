---
title: 為 IoT 邊緣模組部署 Azure 安全中心*微軟文檔
description: 瞭解如何在 IoT 邊緣部署 IoT 安全代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964034"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT 邊緣設備上部署安全模組


**IoT Azure 安全中心**模組為 IoT 邊緣設備提供了全面的安全解決方案。
安全模組收集、聚合和分析來自作業系統和容器系統的原始安全資料，並將其轉化為可操作的安全建議和警報。
要瞭解更多資訊，請參閱[IoT 邊緣的安全模組](security-edge-architecture.md)。

在本文中，您將瞭解如何在 IoT Edge 設備上部署安全模組。

## <a name="deploy-security-module"></a>部署安全模組

使用以下步驟為 IoT 邊緣部署 IoT 安全模組的 Azure 安全中心。

### <a name="prerequisites"></a>Prerequisites

1. 在 IoT 中心中，請確保您的設備[已註冊為 IoT 邊緣設備](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

1. IoT 邊緣模組的 Azure 安全中心要求在 IoT 邊緣設備上安裝[AuditD 框架](https://linux.die.net/man/8/auditd)。

    - 通過在 IoT Edge 設備上運行以下命令來安裝框架：
   
    `sudo apt-get install auditd audispd-plugins`

    - 通過運行以下命令驗證 AuditD 處於活動狀態： 
   
    `sudo systemctl status auditd`<br>
    - 預期回應是：`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>使用 Azure 門戶進行部署

1. 從 Azure 門戶打開**應用商店**。

1. 選擇**物聯網**，然後搜索 IoT 的**Azure 安全中心**並選擇它。

   ![為 IoT 選擇 Azure 安全中心](media/howto/edge-onboarding-8.png)

1. 按一下 **"創建**"以配置部署。 

1. 選擇 IoT 中心的 Azure**訂閱**，然後選擇**IoT 中心**。<br>選擇 **"部署到設備**"以單個設備為目標，或選擇"**在縮放中部署**"以定位多個設備，然後按一下"**創建**"。 有關大規模部署的詳細資訊，請參閱[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。 

    >[!Note] 
    >如果選擇 **"在縮放中部署"，** 請先添加設備名稱和詳細資訊，然後再繼續在以下說明中的"**添加模組"** 選項卡。     

完成每個步驟，完成適用于 IoT 的 Azure 安全中心的 IoT 邊緣部署。 

#### <a name="step-1-modules"></a>第 1 步：模組

1. 選擇**Azure 安全中心forIoT**模組。
1. 在"**模組設置"** 選項卡上，將**名稱**更改為**azureaiotsecurity**。
1. 在"**環境變數"** 選項卡上，根據需要添加變數（例如，調試級別）。
1. 在 **"容器創建選項**"選項卡上，添加以下配置：

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. 在 **"模組孿生設置"** 選項卡上，添加以下配置：
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. 選擇 **"更新**"。

#### <a name="step-2-runtime-settings"></a>第 2 步：運行時設置

1. 選擇**運行時設置**。
1. 在 **"邊中心**"下，將**圖像**更改為**mcr.microsoft.com/azureiotedge-hub:1.0.8.3**。
1. 驗證**創建選項**設置為以下配置： 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. 選取 [儲存]****。
   
1. 選取 [下一步]****。

#### <a name="step-3-specify-routes"></a>第 3 步：指定路由 

1. 在 **"指定路由"** 選項卡上，請確保具有路由（顯式或隱式），該路由將根據以下示例將來自**azureiotaa）** 模組的消息轉發到 **$upstream。** 僅當路徑就位時，才選擇 **"下一步**"。

   路由示例：

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. 選取 [下一步]****。

#### <a name="step-4-review-deployment"></a>第 4 步：查看部署

- 在"**查看部署"** 選項卡上，查看部署資訊，然後選擇 **"創建**"以完成部署。

## <a name="diagnostic-steps"></a>診斷步驟

如果遇到問題，容器日誌是瞭解 IoT Edge 安全模組設備狀態的最佳方式。 可使用這一節中的命令與工具來收集資訊。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>驗證所需的容器已安裝並按預期運行

1. 在 IoT 邊緣設備上運行以下命令：
    
    `sudo docker ps`
   
1. 驗證以下容器是否正在運行：
   
   | 名稱 | IMAGE |
   | --- | --- |
   | azureiot 安全 | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | 邊緣Hub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | 邊緣代理 | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   如果不存在所需的最小容器，請檢查 IoT Edge 部署清單是否與建議的設置一致。 有關詳細資訊，請參閱部署[IoT 邊緣模組](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>檢查模組日誌是否存在錯誤
   
1. 在 IoT 邊緣設備上運行以下命令：

   `sudo docker logs azureiotsecurity`
   
1. 有關更詳細的日誌，將以下環境變數添加到**azureiotasecurity**模組部署： `logLevel=Debug`。

## <a name="next-steps"></a>後續步驟

要瞭解有關配置選項的詳細資訊，請繼續訪問模組配置的操作指南。 
> [!div class="nextstepaction"]
> [模組配置操作指南](./how-to-agent-configuration.md)
