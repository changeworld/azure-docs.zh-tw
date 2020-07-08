---
title: 在 Linux 中保存狀態-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 Linux 中保存中繼資料
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77086664"
---
# <a name="persist-state-in-linux"></a>在 Linux 中保存狀態

在「事件方格」模組中建立的主題和訂用帳戶預設會儲存在容器檔案系統中。 若無持續性，如果重新部署模組，則所有建立的中繼資料都會遺失。 若要在部署和重新開機時保留資料，您需要將資料保存在容器檔案系統之外。

根據預設，只會保存中繼資料，而事件仍會儲存在記憶體中，以提升效能。 請遵循保存事件一節來啟用事件持續性。

本文提供在 Linux 部署中部署具有持續性之事件方格模組的步驟。

> [!NOTE]
>事件方格模組會以具有 UID 和 name 的低許可權使用者身分執行 `2000` `eventgriduser` 。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接進行持續性

 [Docker 磁片](https://docs.docker.com/storage/volumes/)區是用來保留跨部署的資料。 您可以讓 docker 自動建立已命名的磁片區，作為部署事件方格模組的一部分。 此選項是最簡單的選項。 您可以在 [系結] 區段中指定要**建立的磁片**區名稱，如下所示：

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
>請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 Linux 上的事件方格模組，它必須是 **/app/metadataDb**。

例如，下列設定會導致建立會保存中繼資料的磁片區**egmetadataDbVol** 。

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

您可以在主機系統上建立目錄並掛接該目錄，而不是裝載磁片區。

## <a name="persistence-via-host-directory-mount"></a>透過主機目錄掛接的持續性

除了 docker 磁片區以外，您也可以選擇掛接主機資料夾。

1. 首先，執行下列命令，在主機電腦上建立名為**eventgriduser**且識別碼為**2000**的使用者：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   md <your-directory-name-here>
   ```

    例如，執行下列命令將會建立名為**myhostdir**的目錄。

    ```sh
    md /myhostdir
    ```
1. 接下來，執行下列命令來建立此資料夾的**eventgriduser**擁有者。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用系結來掛接**目錄，並**從 Azure 入口網站重新部署事件方格模組。

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
    >請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 linux 上的事件方格模組，它必須是 **/app/metadataDb**和 **/app/eventsDb**


## <a name="persist-events"></a>保存事件

若要啟用事件持續性，您必須先使用上述各節，透過磁片區掛接或主機目錄掛接來啟用中繼資料持續性。

保存事件的重要注意事項：

* 保存事件是根據每個事件訂用帳戶來啟用，並且會在裝載磁片區或目錄後加入宣告。
* 事件持續性會在建立時于事件訂用帳戶上設定，而且在建立事件訂閱之後就無法修改。 若要切換事件持續性，您必須刪除並重新建立事件訂閱。
* 保存事件幾乎總是比記憶體中的作業慢，不過速度的差異會高度依賴磁片磁碟機的特性。 速度和可靠性之間的取捨是所有訊息系統固有的，但通常只有大規模才會明顯。

若要在事件訂用帳戶上啟用事件持續性，請將設定 `persistencePolicy` 為 `true` ：

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
