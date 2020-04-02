---
title: 為 IoT 邊緣模組部署 Azure 安全中心*微軟文件
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
ms.openlocfilehash: 3aee30e8ad82f9657c3bc9e97a7657a1e8c7989d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548918"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT 邊緣裝置上部署安全模組


**IoT Azure 安全中心**模組為IoT邊緣設備提供了全面的安全解決方案。
安全模組收集、聚合和分析來自操作系統和容器系統的原始安全數據,並將其轉化為可操作的安全建議和警報。
要瞭解更多資訊,請參閱[IoT 邊緣的安全模組](security-edge-architecture.md)。

在本文中,您將瞭解如何在 IoT Edge 設備上部署安全模組。

## <a name="deploy-security-module"></a>部署安全模組

使用以下步驟為 IoT 邊緣部署 IoT 安全模組的 Azure 安全中心。

### <a name="prerequisites"></a>Prerequisites

1. 在 IoT 中,請確保您的裝置[已註冊為 IoT 邊緣裝置](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

1. IoT 邊緣模組的 Azure 安全中心要求在 IoT 邊緣裝置上安裝[AuditD 框架](https://linux.die.net/man/8/auditd)。

    - 透過 IoT Edge 裝置執行以下指令來安裝框架:
   
    `sudo apt-get install auditd audispd-plugins`

    - 以執行以下指令驗證 AuditD 系統狀態: 
   
    `sudo systemctl status auditd`<br>
    - 預期回應是:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>使用 Azure 門戶進行部署

1. 從 Azure 門戶開啟**應用商店**。

1. 選擇**物聯網**,然後搜索 IoT 的**Azure 安全中心**並選擇它。

   ![為 IoT 選擇 Azure 安全中心](media/howto/edge-onboarding-8.png)

1. 按下 **「創建**」以配置部署。 

1. 選擇 IoT 中心的 Azure**訂閱**,然後選擇**IoT 中心**。<br>選擇 **「部署到裝置**」以單個裝置為目標,或選擇「**在縮放中部署**」以定位多個裝置,然後單擊「**創建**」 。 有關大規模部署的詳細資訊,請參閱[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。 

    >[!Note] 
    >如果選擇 **「在縮放中部署」 請**先新增裝置名稱和詳細資訊,然後再繼續在以下說明中的「**添加模組」** 選項卡。     

完成每個步驟,完成適用於 IoT 的 Azure 安全中心的 IoT 邊緣部署。 

#### <a name="step-1-modules"></a>第一步:模組

1. 選擇**Azure 安全中心forIoT**模組。
1. 在模組**設定「 選項**卡上」,將**名稱**變更為**azureaiotsecurity**。
1. 在「**環境變數」** 選項卡上,根據需要添加變數(例如,除錯等級)。
1. 在 **'容器建立選項**'選項卡上,新增以下設定:

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
    
1. 在 **「模組孿生設置」** 選項卡上,添加以下設定:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. 選擇 **"更新**"。

#### <a name="step-2-runtime-settings"></a>第二步:執行時設定

1. 選擇**執行時設定**。
1. 在 **「邊中心**」下,將**影像**更改為**mcr.microsoft.com/azureiotedge-hub:1.0.8.3**。
1. 認證**建立選項**設定為以下設定: 
         
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
    
1. 選取 [儲存]  。
   
1. 選取 [下一步]  。

#### <a name="step-3-specify-routes"></a>第三步:指定路由 

1. 在 **「指定路由」** 選項卡上,請確保具有路由(顯式或隱式),該路由將根據以下範例將來自**azureiotaa)** 模組的消息轉發到 **$upstream。** 僅當路徑就位時,才選擇 **「下一步**」。。

   路由範例:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. 選取 [下一步]  。

#### <a name="step-4-review-deployment"></a>第四步:檢視部署

- 在「**檢視部署」** 選項卡上,查看部署資訊,然後選擇 **「創建**」以完成部署。

## <a name="diagnostic-steps"></a>診斷步驟

如果遇到問題,容器日誌是瞭解 IoT Edge 安全模組設備狀態的最佳方式。 可使用這一節中的命令與工具來收集資訊。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>驗證所需的容器已安裝並按預期執行

1. 在 IoT 邊緣裝置執行以下指令:
    
    `sudo docker ps`
   
1. 驗證以下容器是否正在執行:
   
   | 名稱 | IMAGE |
   | --- | --- |
   | azureiot 安全性 | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | 邊緣Hub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | 邊緣代理 | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   如果不存在所需的最小容器,請檢查 IoT Edge 部署清單是否與建議的設置一致。 有關詳細資訊,請參閱部署[IoT 邊緣模組](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>檢查模組紀錄是否存在錯誤
   
1. 在 IoT 邊緣裝置執行以下指令:

   `sudo docker logs azureiotsecurity`
   
1. 有關更詳細的紀錄,將以下環境變數加入**到 azureiotasecurity**模組部署: `logLevel=Debug`。

## <a name="next-steps"></a>後續步驟

要瞭解有關配置選項的詳細資訊,請繼續訪問模組配置的操作指南。 
> [!div class="nextstepaction"]
> [模組設定操作指南](./how-to-agent-configuration.md)
