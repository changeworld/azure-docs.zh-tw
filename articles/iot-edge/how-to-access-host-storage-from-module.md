---
title: 使用模組中的 IoT 邊緣設備本機存放區 - Azure IoT 邊緣 |微軟文檔
description: 使用環境變數並創建選項以啟用模組對 IoT Edge 設備本機存放區的訪問。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434532"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>提供模組存取裝置本機儲存體的權限

除了使用 Azure 存儲服務或設備的容器存儲存儲資料外，您還可以將存儲專用於主機 IoT Edge 設備本身，以提高可靠性，尤其是在離線操作時。

## <a name="link-module-storage-to-device-storage"></a>將模組存儲連結到設備存儲

要啟用從模組存儲到主機系統上存儲的連結，請為模組創建一個環境變數，指向容器中的存儲資料夾。 然後，使用建立選項，將該儲存體資料夾與主機電腦上的資料夾繫結。

例如，如果要啟用 IoT Edge 中心將消息存儲在設備的本機存放區中並在以後檢索它們，則可以在**運行時設置**部分中的 Azure 門戶中配置環境變數和創建選項。

1. 對於 IoT Edge 中心和 IoT Edge 代理，添加一個稱為**存儲資料夾**的環境變數，該變數指向模組中的目錄。
1. 對於 IoT Edge 中心和 IoT Edge 代理，添加綁定以將主機上的本地目錄連接到模組中的目錄。 例如：

   ![為本機存放區添加創建選項和環境變數](./media/how-to-access-host-storage-from-module/offline-storage.png)

或者，可以直接在部署清單中配置本機存放區。 例如：

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

替換`<HostStoragePath>`主機`<ModuleStoragePath>`和模組存儲路徑;這兩個值都必須是絕對路徑。

例如，在 Linux 系統上，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]`表示主機系統上的目錄 **/etc/iotedge/存儲**映射到容器中的目錄 **/iotedge/存儲。** 在 Windows 系統上，作為另一`"Binds":["C:\\temp:C:\\contemp"]`個示例，表示主機系統上的目錄**C：temp\\**映射到容器中的目錄**C：\\同一點**。

此外，在 Linux 設備上，請確保模組的使用者設定檔具有對主機系統目錄所需的讀取、寫入和執行許可權。 返回到啟用 IoT Edge 中心在設備的本機存放區中存儲消息的較早示例，您需要為其使用者設定檔 UID 1000 授予許可權。 （IoT Edge 代理作為根操作，因此不需要其他許可權。有幾種方法可以管理 Linux 系統上的目錄許可權，包括使用`chown`更改目錄擁有者，然後`chmod`更改許可權，例如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

你可以從[docker 文檔](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)找到有關創建選項的更多詳細資訊。

## <a name="next-steps"></a>後續步驟

有關從模組訪問主機存儲的其他示例，請參閱[在 IoT 邊緣上使用 Azure Blob 存儲在邊緣存儲資料](how-to-store-data-blob.md)。
