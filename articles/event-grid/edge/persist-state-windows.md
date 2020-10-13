---
title: 在 Windows 中保存狀態-Azure 事件方格 IoT Edge |Microsoft Docs
description: 在 Windows 中保存狀態
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171460"
---
# <a name="persist-state-in-windows"></a>在 Windows 中保存狀態

依預設，在事件方格模組中建立的主題和訂用帳戶會儲存在容器檔案系統中。 如果沒有持續性，則如果重新部署模組，所有建立的中繼資料都會遺失。 若要跨部署保留資料並重新啟動，您需要將資料保存在容器檔案系統之外。 

依預設，只會保存中繼資料，而事件仍會儲存在記憶體中，以改善效能。 遵循「保存事件」區段，也可以啟用事件持續性。

本文提供在 Windows 部署中部署具有持續性的事件方格模組所需的步驟。

> [!NOTE]
>事件方格模組會以 Windows 中低許可權的使用者 **ContainerUser** 來執行。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接的持續性

[Docker 磁片](https://docs.docker.com/storage/volumes/) 區是用來保留跨部署的資料。 若要掛接磁片區，您必須使用 docker 命令來建立它、提供許可權，讓容器可以讀取、寫入它，然後部署模組。

1. 執行下列命令以建立磁片區：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 執行下列命令，以取得磁片區所對應的主機目錄

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   範例輸出：

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
1. 將 **Users** 群組新增至 **裝入** 點指向的值，如下所示：
    1. 啟動檔案總管。
    1. 流覽至 **裝入**點指向的資料夾。
    1. 以滑鼠右鍵按一下，然後選取 [ **屬性**]。
    1. 選取 [安全性]。
    1. 在 [群組或使用者名稱] 底下，選取 [ **編輯**]。
    1. 選取 [ **新增**]，輸入 `Users` ，選取 [ **檢查名稱**]，然後選取 **[確定]**。
    1. 在 [ *使用者的許可權*] 底下，選取 [ **修改**]，然後選取 **[確定]**。
1. 使用 **系** 結來掛接此磁片區，並從 Azure 入口網站重新部署事件方格模組

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
   >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是 **C： \\ app \\ metadataDb**。


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

除了裝載磁片區之外，您還可以在主機系統上建立一個目錄，並掛接該目錄。

1. 藉由執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用系結來掛接您的 **目錄，並** 從 Azure 入口網站重新部署事件方格模組。

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
    >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是 **C： \\ app \\ metadataDb**。

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

若要啟用事件持續性，您必須先使用上述章節透過磁片區掛接或主機目錄掛接來啟用事件持續性。

保存事件的重要注意事項：

* 每個事件訂用帳戶會啟用持續性事件，並且會在裝載磁片區或目錄之後加入宣告。
* 事件持續性是在建立時于事件訂用帳戶上設定的，而且在建立事件訂用帳戶之後，就無法修改。 若要切換事件持續性，您必須刪除並重新建立事件訂閱。
* 保存事件幾乎一律會比記憶體中的作業慢，但是速度差異高度取決於磁片磁碟機的特性。 速度與可靠性之間的取捨是所有郵件系統的固有功能，但只會在大規模的情況中變得很明顯。

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