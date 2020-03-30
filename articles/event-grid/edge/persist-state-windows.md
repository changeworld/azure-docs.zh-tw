---
title: Windows 中的持久狀態 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 在 Windows 中保持狀態
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086620"
---
# <a name="persist-state-in-windows"></a>在 Windows 中保持狀態

預設情況下，在事件網格模組中創建的主題和訂閱存儲在容器檔案系統中。 如果沒有持久性，如果重新部署模組，則創建的所有中繼資料都將丟失。 要跨部署和重新開機保留資料，您需要將資料保留在容器檔案系統之外。 

預設情況下，僅保留中繼資料，並且事件仍存儲在記憶體中以提高性能。 請按照持久化事件部分啟用事件持久性。

本文提供了在 Windows 部署中具有持久性的部署事件網格模組所需的步驟。

> [!NOTE]
>事件網格模組在 Windows 中作為低特權使用者**容器使用者**運行。

## <a name="persistence-via-volume-mount"></a>通過卷裝載的持久性

[Docker 卷](https://docs.docker.com/storage/volumes/)用於跨部署保留資料。 要裝載卷，您需要使用 docker 命令創建卷，授予許可權，以便容器可以讀取、寫入該卷，然後部署模組。

1. 通過運行以下命令創建卷：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 通過運行以下命令獲取卷映射到的主機目錄

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   樣品輸出：-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 將 **"使用者"** 組添加到 **"安裝點**"指向的值，如下所示：
    1. 開機檔案資源管理器。
    1. 導航到**Mountpoint**指向的資料夾。
    1. 按右鍵，然後選擇 **"屬性**"。
    1. 選擇 **"安全**"。
    1. 在 "組"或"使用者名"下，選擇 **"編輯**"。
    1. 選擇 **"添加**"，輸入`Users`"**選中"名稱**"，然後選擇 **"確定**"。
    1. 在 *"使用者許可權*"下，選擇 **"修改**"，然後選擇 **"確定**"。
1. 使用**綁定**裝載此卷並從 Azure 門戶重新部署事件網格模組

   例如，

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >不要更改綁定值的第二部分。 它指向模組中的特定位置。 對於視窗上的事件網格模組，它必須是**C：\\\\應用中繼資料Db**。


    例如，

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>通過主機目錄裝載的持久性

您可以在主機系統上創建目錄並裝載該目錄，而不是安裝卷。

1. 通過運行以下命令在主機檔案系統上創建目錄。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用 **"綁定**"裝載目錄並從 Azure 門戶重新部署事件網格模組。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >不要更改綁定值的第二部分。 它指向模組中的特定位置。 對於視窗上的事件網格模組，它必須是**C：\\\\應用中繼資料Db**。

    例如，

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
## <a name="persist-events"></a>持久事件

要啟用事件持久性，必須首先使用上述部分通過卷裝載或主機目錄裝載啟用事件持久性。

關於持久事件需要注意的重要事項：

* 堅持事件根據每個事件訂閱啟用，並在裝載卷或目錄後加入宣告。
* 事件持久性在創建時配置在事件訂閱上，創建事件訂閱後無法修改。 要切換事件持久性，必須刪除並重新創建事件訂閱。
* 持久化事件幾乎總是比記憶體操作慢，但是速度差異在很大程度上取決於磁碟機的特徵。 速度和可靠性之間的權衡是所有郵件系統固有的，但只是大規模變得明顯。

要在事件訂閱上啟用事件持久性，應`persistencePolicy`設置為`true`：

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```