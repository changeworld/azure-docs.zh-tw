---
title: 從模組使用 IoT Edge 裝置本機儲存體-Azure IoT Edge |Microsoft Docs
description: 使用環境變數和建立選項，以啟用 IoT Edge 裝置本機儲存體的模組存取。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4af63421e831318e6250825cffd1abad415b85bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447829"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>提供模組存取裝置本機儲存體的權限

除了使用 Azure 儲存體服務或裝置的容器存放裝置儲存資料，您也可以將主機上的專用儲存體 IoT Edge 裝置本身，以提升可靠性，特別是在離線操作時。

## <a name="link-module-storage-to-device-storage"></a>將模組儲存體連結至裝置存放裝置

若要啟用從模組儲存體到主機系統上存放裝置的連結，請為您的模組建立環境變數，以指向容器中的儲存體資料夾。 然後，使用建立選項，將該儲存體資料夾與主機電腦上的資料夾繫結。

例如，如果您想要讓 IoT Edge 中樞將訊息儲存在裝置的本機儲存體中，並于稍後取出它們，您可以在 [ **執行時間設定** ] 區段的 [Azure 入口網站] 中設定環境變數和建立選項。

1. 針對 IoT Edge hub 和 IoT Edge 代理程式，新增名為 **storageFolder** 的環境變數，指向模組中的目錄。
1. 針對 IoT Edge hub 和 IoT Edge 代理程式，新增系結以將主機電腦上的本機目錄連接至模組中的目錄。 例如：

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

請將和取代為 `<HostStoragePath>` `<ModuleStoragePath>` 您的主機和模組儲存體路徑; 這兩個值都必須是絕對路徑。

例如，在 Linux 系統上， `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主機系統上的目錄 **/etc/iotedge/storage** 會對應到容器中的目錄 **/iotedge/storage/** 。 在 Windows 系統上，另一個範例 `"Binds":["C:\\temp:C:\\contemp"]` 是表示主機系統上的目錄 **c： \\ temp** 對應到容器中的目錄 **c： \\ contemp** 。

此外，在 Linux 裝置上，請確定您模組的使用者設定檔具有主機系統目錄的必要讀取、寫入和執行許可權。 回到先前的範例，啟用 IoT Edge hub 以將訊息儲存在裝置的本機儲存體中，您必須將許可權授與其使用者設定檔（UID 1000）。  (IoT Edge 代理程式以 root 的身分運作，因此不需要額外的許可權。 ) 有數種方式可管理 Linux 系統上的目錄許可權，包括使用 `chown` 來變更目錄擁有者，然後 `chmod` 變更許可權，例如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

您可以從 [docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)檔找到更多有關建立選項的詳細資料。

## <a name="encrypted-data-in-module-storage"></a>模組儲存體中的加密資料

當模組叫用 IoT Edge daemon 的工作負載 API 來加密資料時，加密金鑰會使用模組識別碼和模組的世代識別碼來衍生。 如果模組已從部署中移除，而另一個具有相同模組識別碼的模組稍後部署到相同的裝置，則會使用世代識別碼來保護秘密。 您可以使用 Azure CLI 命令 [az iot hub module-identity show](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-identity#ext-azure-cli-iot-ext-az-iot-hub-module-identity-show)來查看模組的世代識別碼。

如果您想要在各層代間共用模組之間的檔案，它們不得包含任何秘密，否則將無法解密。

## <a name="next-steps"></a>後續步驟

如需從模組存取主機存放裝置的其他範例，請參閱 [IoT Edge 上的 Azure Blob 儲存體將資料儲存在邊緣](how-to-store-data-blob.md)。
