---
title: 在 Ubuntu 虛擬機器上執行 Azure IoT Edge | Microsoft Docs
description: 適用于 Ubuntu 18.04 LTS 虛擬機器的安裝指示 Azure IoT Edge
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: 95fd10ab7de4885d3630b5defe4080fe0203b62f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296972"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虛擬機器上執行 Azure IoT Edge

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出使用已安裝的 Azure IoT Edge 執行時間來部署 Ubuntu 18.04 LTS 虛擬機器的步驟，並使用預先提供的裝置連接字串進行設定。 您可以使用[iotedge-vm-部署](https://github.com/Azure/iotedge-vm-deploy)專案存放庫中維護的[雲端初始](../virtual-machines/linux/using-cloud-init.md
) [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)來完成部署。

第一次開機時，Ubuntu 18.04 LTS 虛擬機器會透過 [雲端初始安裝最新版的 Azure IoT Edge 運行](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)時間。 它也會在執行時間啟動之前設定提供的連接字串，讓您輕鬆地設定和連接 IoT Edge 裝置，而不需要啟動 SSH 或遠端桌面會話。 

## <a name="deploy-using-deploy-to-azure-button"></a>使用 [部署至 Azure] 按鈕進行部署

[ [部署至 Azure] 按鈕](../azure-resource-manager/templates/deploy-to-azure-button.md) 可簡化在 GitHub 上維護的 [Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md) 部署。  本節將示範 [iotedge-vm-部署](https://github.com/Azure/iotedge-vm-deploy) 專案存放庫中包含的 [部署至 Azure] 按鈕的使用方式。  


1. 我們會使用 iotedge-VM-部署 Azure Resource Manager 範本，部署已啟用 Azure IoT Edge 的 Linux VM。  若要開始，請按一下以下按鈕：

    [![iotedge-vm-deploy 的 [部署至 Azure] 按鈕](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 在新啟動的視窗中，填寫可用的表單欄位：

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge-vm-deploy 範本的螢幕擷取畫面](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **訂**用帳戶：要在其中部署虛擬機器的作用中 Azure 訂用帳戶。

    **資源群組**：現有或新建立的資源群組，以包含虛擬機器和其相關聯的資源。

    **DNS 標籤首碼**：您選擇的必要值，用來將虛擬機器的主機名稱設為首碼。

    系統**管理員使用者名稱**：使用者名稱，將會在部署時提供根許可權。

    **裝置連接字串**：在您預定的[IoT 中樞](../iot-hub/about-iot-hub.md)內建立之裝置的[裝置連接字串](how-to-register-device.md)。

    **VM 大小**：要部署的虛擬機器[大小](../cloud-services/cloud-services-sizes-specs.md)

    **UBUNTU Os 版本**：要安裝在基底虛擬機器上的 ubuntu os 版本。

    **位置**：要部署虛擬機器的 [地理區域](https://azure.microsoft.com/global-infrastructure/locations/) ，此值預設為所選資源群組的位置。

    **驗證類型**：根據您的喜好設定選擇 **sshPublicKey** 或 **密碼** 。

    **管理員密碼或金鑰**： SSH 公開金鑰的值，或密碼的值（視選擇的驗證類型而定）。

    填入所有欄位之後，請選取頁面底部的核取方塊以接受條款，然後選取 [ **購買** ] 以開始部署。

1. 確認部署已成功完成。  虛擬機器資源應該已部署到選取的資源群組中。  記下電腦名稱稱，格式應為 `vm-0000000000000` 。 此外，請記下相關聯的 **DNS 名稱**，其格式應該是 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com。

    **DNS 名稱**可從 Azure 入口網站內新部署虛擬機器的 [概觀] 區段取得。

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge vm dns 名稱的螢幕擷取畫面](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果您想要在安裝後透過 SSH 連線到此 VM，請使用相關聯的 **DNS 名稱** 與下列命令：  `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>從 Azure CLI 進行部署

1. 確定您已使用下列方式安裝 Azure CLI iot 擴充功能：
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 接下來，如果您是在桌面上使用 Azure CLI，請從登入開始：

   ```azurecli-interactive
   az login
   ```

1. 如果您有多個訂用帳戶，請選取您想要使用的訂用帳戶：
   1. 列出您的訂用帳戶：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 複製您要使用之訂用帳戶的 SubscriptionID 欄位。

   1. 使用您複製的識別碼來設定您的工作訂用帳戶：

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 建立新的資源群組 (或在下一個步驟中指定現有的資源群組)：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 建立新的虛擬機器：

    若要使用**authenticationType**的 authenticationType `password` ，請參閱下列範例：

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    若要使用 SSH 金鑰進行驗證，您可以藉由指定的 **authenticationType** `sshPublicKey` ，然後在 **ADMINPASSWORDORKEY** 參數中提供 SSH 金鑰的值來進行驗證。  範例如下所示。

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. 確認部署已成功完成。  虛擬機器資源應該已部署到選取的資源群組中。  記下電腦名稱稱，格式應為 `vm-0000000000000` 。 此外，請記下相關聯的 **DNS 名稱**，其格式應該是 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com。

    您可以從上一個步驟的 JSON 格式輸出取得 **DNS 名稱** ，在 **輸出** 區段內以 **公開 SSH** 專案的一部分取得。  此專案的值可以用來透過 SSH 連線到新部署的電腦。

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    您也可以從 Azure 入口網站中新部署虛擬機器的 [**總覽**] 區段取得**DNS 名稱**。

    > [!div class="mx-imgBorder"]
    > [![顯示 iotedge vm dns 名稱的螢幕擷取畫面](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果您想要在安裝後透過 SSH 連線到此 VM，請使用相關聯的 **DNS 名稱** 與下列命令：  `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您在 IoT Edge 執行時間安裝正常時遇到問題，請參閱 [疑難排解](troubleshoot.md) 頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

如果您想要開啟埠以透過 SSH 或其他輸入連線來存取 VM，請參閱 Azure 虛擬機器檔，以瞭解 [如何對 LINUX VM 開啟埠和端點](../virtual-machines/linux/nsg-quickstart.md)
