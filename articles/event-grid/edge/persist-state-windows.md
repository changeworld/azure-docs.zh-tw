---
title: 在 Windows 中保存狀態-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 Windows 中保存狀態
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086620"
---
# <a name="persist-state-in-windows"></a>在 Windows 中保存狀態

在「事件方格」模組中建立的主題和訂用帳戶預設會儲存在容器檔案系統中。 若無持續性，如果重新部署模組，則所有建立的中繼資料都會遺失。 若要在部署和重新開機時保留資料，您需要將資料保存在容器檔案系統之外。 

根據預設，只會保存中繼資料，而事件仍會儲存在記憶體中，以提升效能。 請遵循保存事件一節來啟用事件持續性。

本文提供在 Windows 部署中部署具有持續性的事件方格模組所需的步驟。

> [!NOTE]
>事件方格模組會在 Windows 中以低許可權的使用者**ContainerUser**來執行。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接進行持續性

[Docker 磁片](https://docs.docker.com/storage/volumes/)區是用來保留跨部署的資料。 若要掛接磁片區，您需要使用 docker 命令來建立它、提供許可權，讓容器可以讀取、寫入，然後部署模組。

1. 執行下列命令來建立磁片區：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 執行下列命令來取得磁片區對應的主機目錄

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   範例輸出：-

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
1. 將**使用者**群組新增至以**裝入**點指向的值，如下所示：
    1. 啟動 [檔案瀏覽器]。
    1. 流覽至 [**裝入**點] 所指向的資料夾。
    1. 以滑鼠右鍵按一下，然後選取 [**屬性**]。
    1. 選取 [**安全性**]。
    1. 在 [群組或使用者名稱] 底下，選取 [**編輯**]。
    1. 選取 [**新增**] `Users`，輸入，選取 [**檢查名稱**]，然後選取 **[確定]**。
    1. 在 [*使用者的許可權*] 底下，選取 [**修改**]，然後選取 **[確定]**。
1. 使用 [系結] 來掛接此磁片**區，並**從 Azure 入口網站重新部署事件方格模組

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
   >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是**C：\\app\\metadataDb**。


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

## <a name="persistence-via-host-directory-mount"></a>透過主機目錄掛接的持續性

您可以在主機系統上建立目錄並掛接該目錄，而不是裝載磁片區。

1. 執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用系結來掛接您的**目錄，並**從 Azure 入口網站重新部署事件方格模組。

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
    >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是**C：\\app\\metadataDb**。

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
## <a name="persist-events"></a>保存事件

若要啟用事件持續性，您必須先使用上述各節，透過磁片區掛接或主機目錄掛接來啟用事件持續性。

保存事件的重要注意事項：

* 保存事件是根據每個事件訂用帳戶來啟用，並且會在裝載磁片區或目錄後加入宣告。
* 事件持續性會在建立時于事件訂用帳戶上設定，而且在建立事件訂閱之後就無法修改。 若要切換事件持續性，您必須刪除並重新建立事件訂閱。
* 保存事件幾乎總是比記憶體中的作業慢，不過速度的差異會高度依賴磁片磁碟機的特性。 速度和可靠性之間的取捨是所有訊息系統固有的，但只有大規模才會明顯。

若要在事件訂用帳戶上啟用事件`persistencePolicy`持續`true`性，請將設定為：

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