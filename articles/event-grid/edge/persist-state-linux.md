---
title: Linux 中的持久狀態 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: Linux 中持久性中繼資料
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086664"
---
# <a name="persist-state-in-linux"></a>Linux 中的持久狀態

預設情況下，在事件網格模組中創建的主題和訂閱存儲在容器檔案系統中。 如果沒有持久性，如果重新部署模組，則創建的所有中繼資料都將丟失。 要跨部署和重新開機保留資料，您需要將資料保留在容器檔案系統之外。

預設情況下，僅保留中繼資料，並且事件仍存儲在記憶體中以提高性能。 請按照持久化事件部分啟用事件持久性。

本文提供了在 Linux 部署中具有持久性的事件網格模組部署的步驟。

> [!NOTE]
>事件網格模組以具有 UID`2000`和名稱`eventgriduser`的低特權使用者運行。

## <a name="persistence-via-volume-mount"></a>通過卷裝載的持久性

 [Docker 卷](https://docs.docker.com/storage/volumes/)用於跨部署保留資料。 您可以讓 Docker 自動創建命名卷，作為部署事件網格模組的一部分。 此選項是最簡單的選項。 您可以指定要在 **"綁定"** 部分中創建的卷名稱，如下所示：

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>不要更改綁定值的第二部分。 它指向模組中的特定位置。 對於 Linux 上的事件網格模組，它必須是 **/app/中繼資料Db**。

例如，以下配置將導致創建將保留中繼資料的卷 **（中繼資料DbVol）。**

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
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

您可以在主機系統上創建目錄並裝載該目錄，而不是安裝卷。

## <a name="persistence-via-host-directory-mount"></a>通過主機目錄裝載的持久性

您還可以選擇裝載主機資料夾，而不是 Docker 卷。

1. 首先通過運行以下命令在主機上創建名稱**事件網格使用者**和 ID **2000**的使用者：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 通過運行以下命令在主機檔案系統上創建目錄。

   ```sh
   md <your-directory-name-here>
   ```

    例如，運行以下命令將創建一個名為**myhostdir 的**目錄。

    ```sh
    md /myhostdir
    ```
1. 接下來，通過運行以下命令使**事件網格使用者**擁有此資料夾。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用 **"綁定"** 裝載目錄並從 Azure 門戶重新部署事件網格模組。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >不要更改綁定值的第二部分。 它指向模組中的特定位置。 對於 linux 上的事件網格模組，它必須是 **/app/中繼資料Db**和 **/app/eventDb**


## <a name="persist-events"></a>持久事件

要啟用事件持久性，必須首先使用上述部分通過卷裝載或主機目錄裝載啟用中繼資料持久性。

關於持久事件需要注意的重要事項：

* 堅持事件根據每個事件訂閱啟用，並在裝載卷或目錄後加入宣告。
* 事件持久性在創建時配置在事件訂閱上，創建事件訂閱後無法修改。 要切換事件持久性，必須刪除並重新創建事件訂閱。
* 持久化事件幾乎總是比記憶體操作慢，但是速度差異在很大程度上取決於磁碟機的特徵。 速度和可靠性之間的權衡是所有郵件系統固有的，但通常只會在大規模時變得明顯。

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
