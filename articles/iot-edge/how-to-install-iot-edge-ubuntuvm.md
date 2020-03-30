---
title: 在 Ubuntu 虛擬機器上執行 Azure IoT Edge | Microsoft Docs
description: Ubuntu 18.04 LTS 虛擬機器的 Azure IoT 邊緣設置說明
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349617"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虛擬機器上執行 Azure IoT Edge

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出了部署 Ubuntu 18.04 LTS 虛擬機器的步驟，該虛擬機器安裝了 Azure IoT Edge 運行時，並使用預提供的設備連接字串進行了配置。 部署使用在[iotedge-vm 部署](https://github.com/Azure/iotedge-vm-deploy)專案存儲庫中維護[的基於雲的](../virtual-machines/linux/using-cloud-init.md
) [Azure 資源管理器範本](../azure-resource-manager/templates/overview.md)完成。

在第一次啟動時，Ubuntu 18.04 LTS 虛擬機器[通過雲 init 安裝最新版本的 Azure IoT 邊緣運行時](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)。 它還在運行時開始之前設置提供的連接字串，使您能夠輕鬆配置和連接 IoT Edge 設備，而無需啟動 SSH 或遠端桌面會話。 

## <a name="deploy-using-deploy-to-azure-button"></a>使用部署到 Azure 按鈕進行部署

["部署到 Azure"按鈕](../azure-resource-manager/templates/deploy-to-azure-button.md)允許簡化在 GitHub 上維護[的 Azure 資源管理器範本的部署](../azure-resource-manager/templates/overview.md)。  本節將演示[iotedge-vm 部署](https://github.com/Azure/iotedge-vm-deploy)專案存儲庫中包含的"部署到 Azure 按鈕"的使用方式。  


1. 我們將使用 iotedge-vm 部署 Azure 資源管理器範本部署啟用 Azure IoT Edge 的 Linux VM。  首先，按一下下面的按鈕：

    [![部署到 Azure 按鈕以進行 iotedge-vm 部署](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 在新啟動的視窗中，填寫可用的表單欄位：

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge-vm 部署範本的螢幕截圖](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **訂閱**：要將虛擬機器部署到的活動 Azure 訂閱。

    **資源組**：包含虛擬機器及其關聯資源的現有或新創建的資源組。

    **DNS 標籤首碼**：用於首碼虛擬機器主機名稱的您選擇的必需值。

    **管理員使用者名**：使用者名，將在部署時提供根許可權。

    **設備連接字串**：在預期[IoT 中心](../iot-hub/about-iot-hub.md)中創建的[設備的設備連接字串](how-to-register-device.md)。

    **VM 大小**：要部署的虛擬機器[的大小](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu OS 版本**：要安裝在基本虛擬機器上的 Ubuntu 作業系統的版本。

    **位置**：要將虛擬機器部署到的[地理區域](https://azure.microsoft.com/global-infrastructure/locations/)，此值預設為所選資源組的位置。

    **身份驗證類型**：根據您的偏好選擇**sshPublicKey**或**密碼**。

    **管理員密碼或金鑰**：SSH 公開金鑰的值或密碼的值，具體取決於身份驗證類型的選擇。

    填寫所有欄位後，選擇頁面底部的核取方塊以接受條款，然後選擇 **"購買"** 以開始部署。

1. 驗證部署是否成功完成。  虛擬機器資源應已部署到選定的資源組中。  請注意機器名稱，這應該是格式`vm-0000000000000`。 此外，請注意關聯的 DNS**名稱**，該名稱應採用 格式`<dnsLabelPrefix>`。`<location>`.cloudapp.azure.com.

    **DNS 名稱**可以從 Azure 門戶中新部署的虛擬機器的 **"概述"** 部分獲取。

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge vm 的 dns 名稱的螢幕截圖](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果要在設置後將 SSH 放入此 VM，請使用具有以下命令的關聯**DNS 名稱**：`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>從 Azure CLI 進行部署

1. 確保已安裝 Azure CLI iot 擴展：
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 接下來，如果您在桌面上使用 Azure CLI，請從登錄開始：

   ```azurecli-interactive
   az login
   ```

1. 如果您有多個訂閱，請選擇要使用的訂閱：
   1. 列出您的訂用帳戶：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 複製要使用的訂閱的訂閱 ID 欄位。

   1. 使用複製的 ID 設置工作訂閱：

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 建立新的資源群組 (或在下一個步驟中指定現有的資源群組)：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 建立新的虛擬機器：

    要使用 的`password`**身份驗證類型**，請參閱下面的示例：

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    要使用 SSH 金鑰進行身份驗證，可以通過指定 的`sshPublicKey`**身份驗證類型**來執行此操作，然後在**adminPasswordOrKey**參數中提供 SSH 金鑰的值。  範例如下所示。

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. 驗證部署是否成功完成。  虛擬機器資源應已部署到選定的資源組中。  請注意機器名稱，這應該是格式`vm-0000000000000`。 此外，請注意關聯的 DNS**名稱**，該名稱應採用 格式`<dnsLabelPrefix>`。`<location>`.cloudapp.azure.com.

    **DNS 名稱**可以從上一步的 JSON 格式輸出中獲取，**該輸出部分**作為**公共 SSH**條目的一部分。  此條目的值可用於 SSH 到新部署的電腦。

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS 名稱**也可以從 Azure 門戶中新部署的虛擬機器的 **"概述"** 部分獲取。

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge vm 的 dns 名稱的螢幕截圖](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果要在設置後將 SSH 放入此 VM，請使用具有以下命令的關聯**DNS 名稱**：`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果 IoT Edge 運行時安裝正確出現問題，請查看[故障排除](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

如果要打開端口以通過 SSH 或其他入站連接訪問 VM，請參閱 Azure 虛擬機器文檔，瞭解[打開 Linux VM 的埠和終結點](../virtual-machines/linux/nsg-quickstart.md)
