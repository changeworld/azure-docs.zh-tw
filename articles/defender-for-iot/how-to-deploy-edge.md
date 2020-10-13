---
title: 部署 IoT Edge 安全性模組
description: 瞭解如何在 IoT Edge 上部署適用于 IoT 的 Defender 安全性代理程式。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 06836673f1a567f64217d49cf8e0ca82e132111f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934075"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在您的 IoT Edge 裝置上部署安全性模組

**Defender For IoT** 模組為您的 IoT Edge 裝置提供了全方位的安全性解決方案。
安全性模組會收集、匯總及分析您的作業系統和容器系統中的原始安全性資料，以採取動作的安全性建議和警示。
若要深入瞭解，請參閱 [IoT Edge 的安全性模組](security-edge-architecture.md)。

在本文中，您將瞭解如何在 IoT Edge 裝置上部署安全性模組。

## <a name="deploy-security-module"></a>部署安全性模組

使用下列步驟來為 IoT Edge 部署適用于 IoT 的 Defender 安全性模組。

### <a name="prerequisites"></a>必要條件

1. 在 IoT 中樞中，確定您的裝置已 [註冊為 IoT Edge 裝置](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

1. IoT Edge 模組的 Defender 需要在 IoT Edge 裝置上安裝 [AuditD framework](https://linux.die.net/man/8/auditd) 。

    - 在 IoT Edge 裝置上執行下列命令，以安裝架構：

    `sudo apt-get install auditd audispd-plugins`

    - 藉由執行下列命令來確認 AuditD 為作用中：

    `sudo systemctl status auditd`<br>
    - 預期的回應為： `active (running)`

### <a name="deployment-using-azure-portal"></a>使用 Azure 入口網站部署

1. 從 Azure 入口網站開啟 **Marketplace**。

1. 選取 **物聯網**，然後搜尋適用 **于 IoT 的 Defender** ，然後選取它。

   ![為 IoT 選取 Defender](media/howto/edge-onboarding-8.png)

1. 按一下 [ **建立** ] 以設定部署。

1. 選擇 IoT 中樞的 Azure **訂** 用帳戶，然後選取您的 **iot 中樞**。<br>選取 [ **部署至裝置** 以單一裝置為目標]，或選取 [ **大規模部署** 至目標多個裝置]，然後按一下 [ **建立**]。 如需大規模部署的詳細資訊，請參閱 [如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。

    >[!Note]
    >如果您選取 [ **大規模部署**]，請先新增裝置名稱和詳細資料，然後再繼續進行下列指示中的 [ **新增模組** ] 索引標籤。

完成每個步驟，以完成適用于 Defender for IoT 的 IoT Edge 部署。

#### <a name="step-1-modules"></a>步驟1：模組

1. 選取 [ **AzureSecurityCenterforIoT** ] 模組。
1. 在 [ **模組設定** ] 索引標籤上，將 **名稱** 變更為 **azureiotsecurity**。
1. 在 [ **環境變數** ] 索引標籤上，視需要新增變數 (例如，debug 層級) 。
1. 在 [ **容器建立選項** ] 索引標籤上，新增下列設定：

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

1. 在 [ **模組** 對應項設定] 索引標籤上，新增下列設定：

   模組對應項屬性：
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   模組對應項屬性內容： 

   ```json
     {

     }
   ```
    
   如需設定代理程式的詳細資訊，請參閱 [設定安全性代理](https://docs.microsoft.com/azure/defender-for-iot/how-to-agent-configuration)程式。

1. 選取 [更新]。

#### <a name="step-2-runtime-settings"></a>步驟2：執行時間設定

1. 選取 [ **執行時間設定**]。
1. 在 [ **Edge 中樞**] 下，將 **映射** 變更為 **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**。
1. 確認 **建立選項** 設定為下列設定：

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

1. 選取 [下一步]  。

#### <a name="step-3-specify-routes"></a>步驟3：指定路由

1. 在 [ **指定路由** ] 索引標籤上，確定您有 (明確或隱含) 的路由，會根據下列範例將訊息從 **azureiotsecurity** 模組轉送至 **$upstream** 。 只有在路由已就緒時，再選取 **[下一步]**。

   範例路由：

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. 選取 [下一步]  。

#### <a name="step-4-review-deployment"></a>步驟4：審查部署

- 在 [ **審核部署** ] 索引標籤上，檢查您的部署資訊，然後選取 [ **建立** ] 以完成部署。

## <a name="diagnostic-steps"></a>診斷步驟

如果您遇到問題，容器記錄是瞭解 IoT Edge 安全性模組裝置狀態的最佳方式。 可使用這一節中的命令與工具來收集資訊。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>確認所需的容器已安裝且如預期般運作

1. 在您的 IoT Edge 裝置上執行下列命令：

    `sudo docker ps`

1. 確認下列容器正在執行：

   | 名稱 | 影像 |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   如果不存在所需的最小容器，請檢查您的 IoT Edge 部署資訊清單是否與建議的設定一致。 如需詳細資訊，請參閱 [部署 IoT Edge 模組](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>檢查模組記錄檔中的錯誤

1. 在您的 IoT Edge 裝置上執行下列命令：

   `sudo docker logs azureiotsecurity`

1. 如需更詳細的記錄，請將下列環境變數新增至 **azureiotsecurity** 模組部署： `logLevel=Debug` 。

## <a name="next-steps"></a>後續步驟

若要深入瞭解設定選項，請繼續進行模組設定的操作指南。
> [!div class="nextstepaction"]
> [模組設定操作指南](./how-to-agent-configuration.md)
