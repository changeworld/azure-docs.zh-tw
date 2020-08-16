---
title: 從模組 Azure IoT Edge 使用 IoT Edge 裝置本機儲存體 |Microsoft Docs
description: 使用環境變數並建立選項，以啟用 IoT Edge 裝置本機儲存體的模組存取。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe24cc79d749761b697a8d1a162ec2867da9a649
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257471"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>提供模組存取裝置本機儲存體的權限

除了使用 Azure 儲存體服務或裝置的容器儲存體來儲存資料以外，您也可以將主機上的存放裝置專用 IoT Edge 裝置本身，以提高可靠性，特別是在離線操作時。

## <a name="link-module-storage-to-device-storage"></a>將模組儲存體連結至裝置儲存體

若要啟用從模組存放裝置到主機系統上存放裝置的連結，請建立模組的環境變數，指向容器中的儲存體資料夾。 然後，使用建立選項，將該儲存體資料夾與主機電腦上的資料夾繫結。

例如，如果您想要讓 IoT Edge 中樞將訊息儲存在您裝置的本機儲存體並于稍後取出，您可以在 [ **執行時間設定** ] 區段的 [Azure 入口網站中設定環境變數和 [建立] 選項。

1. 針對 IoT Edge hub 和 IoT Edge 代理程式，新增名為 **storageFolder** 的環境變數，以指向模組中的目錄。
1. 針對 IoT Edge hub 和 IoT Edge 代理程式，新增系結以將主機電腦上的本機目錄連接到模組中的目錄。 例如：

   ![新增本機儲存體的建立選項和環境變數](./media/how-to-access-host-storage-from-module/offline-storage.png)

或者，您可以直接在部署資訊清單中設定本機儲存體。 例如：

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

`<HostStoragePath>`將和取代 `<ModuleStoragePath>` 為您的主機和模組儲存體路徑，這兩個值都必須是絕對路徑。

例如，在 Linux 系統上， `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示您主機系統上的目錄 **/etc/iotedge/storage** 會對應至容器中的目錄 **/iotedge/storage/** 。 在 Windows 系統上，如另一個範例， `"Binds":["C:\\temp:C:\\contemp"]` 表示主機系統上的目錄 **c： \\ temp** 對應至容器中的目錄 **c： \\ contemp** 。

此外，在 Linux 裝置上，請確定您模組的使用者設定檔具有主機系統目錄的必要 [讀取]、[寫入] 和 [執行] 許可權。 回到先前的範例，讓 IoT Edge hub 將訊息儲存在您裝置的本機儲存體中，您必須將許可權授與其使用者設定檔（UID 1000）。  (IoT Edge 代理程式以 root 身分運作，因此不需要額外的許可權。 ) 在 Linux 系統上管理目錄許可權的方法有好幾種，包括使用 `chown` 來變更目錄擁有者，然後 `chmod` 變更許可權，例如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

您可以從 [docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)檔中找到更多有關建立選項的詳細資料。

## <a name="encrypted-data-in-module-storage"></a>模組儲存體中的加密資料

當模組叫用 IoT Edge daemon 的工作負載 API 來加密資料時，加密金鑰會使用模組識別碼和模組的世代識別碼來衍生。 如果模組已從部署中移除，則會使用世代識別碼來保護秘密，然後將具有相同模組識別碼的另一個模組部署到相同的裝置。 您可以使用 Azure CLI 命令 [az iot hub 模組-identity show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-module-identity-show)來查看模組的世代識別碼。

如果您想要跨層代在模組之間共用檔案，它們不能包含任何秘密，否則將無法解密。

## <a name="next-steps"></a>後續步驟

如需從模組存取主機存放裝置的其他範例，請參閱在 [IoT Edge 上使用 Azure Blob 儲存體將資料儲存在邊緣](how-to-store-data-blob.md)。
