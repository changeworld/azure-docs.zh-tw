---
title: 在 Linux 中保存狀態-Azure 事件方格 IoT Edge |Microsoft Docs
description: 在 Linux 中保存中繼資料
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171477"
---
# <a name="persist-state-in-linux"></a>在 Linux 中保存狀態

依預設，在事件方格模組中建立的主題和訂用帳戶會儲存在容器檔案系統中。 如果沒有持續性，則如果重新部署模組，所有建立的中繼資料都會遺失。 若要跨部署保留資料並重新啟動，您需要將資料保存在容器檔案系統之外。

依預設，只會保存中繼資料，而事件仍會儲存在記憶體中，以改善效能。 遵循「保存事件」區段，也可以啟用事件持續性。

本文提供的步驟可讓您在 Linux 部署中部署具有持續性的事件方格模組。

> [!NOTE]
>事件方格模組會以具有 UID 和名稱的低許可權使用者的形式執行 `2000` `eventgriduser` 。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接的持續性

 [Docker 磁片](https://docs.docker.com/storage/volumes/) 區是用來保留跨部署的資料。 您可以在部署事件方格模組時，讓 docker 自動建立命名磁片區。 此選項是最簡單的選項。 您可以在 [系結] 區段中指定要建立的磁片區名稱， **如下所示** ：

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
>請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 Linux 上的事件方格模組，必須 **/app/metadataDb**。

例如，下列設定會導致建立會保存中繼資料的磁片區 **egmetadataDbVol** 。

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

除了裝載磁片區之外，您還可以在主機系統上建立一個目錄，並掛接該目錄。

## <a name="persistence-via-host-directory-mount"></a>透過主機目錄掛接的持續性

您也可以選擇裝載主機資料夾，而不是 docker 磁片區。

1. 請先執行下列命令，在主機電腦上建立名為 **eventgriduser** 的使用者和識別碼 **2000** ：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 藉由執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   md <your-directory-name-here>
   ```

    例如，執行下列命令將會建立名為 **myhostdir**的目錄。

    ```sh
    md /myhostdir
    ```
1. 接下來，執行下列命令，以 **eventgriduser** 此資料夾的擁有者。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用系結來掛接 **目錄，並** 從 Azure 入口網站重新部署事件方格模組。

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
    >請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 linux 上的事件方格模組，它必須是 **/app/metadataDb** 和 **/app/eventsDb**


## <a name="persist-events"></a>保存事件

若要啟用事件持續性，您必須先使用上述各節來啟用透過磁片區掛接或主機目錄掛接的中繼資料持續性。

保存事件的重要注意事項：

* 每個事件訂用帳戶會啟用持續性事件，並且會在裝載磁片區或目錄之後加入宣告。
* 事件持續性是在建立時于事件訂用帳戶上設定的，而且在建立事件訂用帳戶之後，就無法修改。 若要切換事件持續性，您必須刪除並重新建立事件訂閱。
* 保存事件幾乎一律會比記憶體中的作業慢，但是速度差異高度取決於磁片磁碟機的特性。 速度與可靠性之間的取捨是所有郵件系統的固有功能，但通常只會在大規模的情況下變得很明顯。

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
