---
title: 在 Ubuntu 虛擬機器上執行 Azure IoT Edge | Microsoft Docs
description: Ubuntu 16.04 Azure Marketplace 虛擬機器上的 Azure IoT Edge 安裝指示
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: philmea
ms.openlocfilehash: 49a783e1360aeddc8eeaadba442acf578d9d6f7f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046037"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虛擬機器上執行 Azure IoT Edge

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文會列出使用預先設定的 [Ubuntu Azure Marketplace 上的 Azure IoT Edge 供應項目](https://aka.ms/azure-iot-edge-ubuntuvm)，在 Ubuntu 16.04 虛擬機器上執行 Azure IoT Edge 執行階段的步驟。

在第一次開機時，Ubuntu VM 上的 Azure IoT Edge 會預先安裝最新版本的 Azure IoT Edge 執行階段。 它也包含指令碼，用以設定連接字串然後重新啟動執行階段，可以透過 Azure VM 入口網站或 Azure 命令列遠端觸發，讓您輕鬆地設定及連線 IoT Edge 裝置，不需要啟動 SSH 或遠端桌面工作階段。 此指令碼會等候 IoT Edge 用戶端完整安裝，然後才會設定連接字串，讓您不需要將其建置到您的自動化。

## <a name="deploy-from-the-azure-marketplace"></a>從 Azure Marketplace 進行部署

1. 瀏覽至 [Ubuntu 上的 Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace 供應項目，或在 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 上搜尋 “Azure IoT Edge on Ubuntu”
2. 選取 [立即取得]，然後在下一個對話方塊中選取 [繼續]。
3. 一旦進入 Azure 入口網站，選取 [建立] 並且遵循精靈以部署 VM。
    * 如果這是您第一次試用 VM，最簡單的方式是使用密碼，並且在公用輸入連接埠功能表中啟用 SSH。
    * 如果您有資源密集工作負載，您應該藉由新增更多的 CPU 和/或記憶體來升級虛擬機器大小。
4. 一旦部署虛擬機器，將它設定為連線到您的 IoT 中樞：
    1. 從您在 IoT 中樞中建立的 IoT Edge 裝置複製您的裝置連接字串（如果您不熟悉此程式，可以遵循在[Azure 入口網站程式中取出連接字串](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)）
    1. 從 Azure 入口網站選取您新建立的虛擬機器資源，然後開啟**執行命令**選項
    1. 選取 **RunShellScript** 選項
    1. 透過具有您的裝置連接字串的命令視窗執行下列指令碼：`/etc/iotedge/configedge.sh "{device_connection_string}"`
    1. 選取 [執行]
    1. 請稍候片刻，畫面應該會提供成功訊息，指出連接字串已成功設定。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 入口網站部署

從 Azure 入口網站搜尋 “Azure IoT Edge”，然後選取 **Ubuntu Server 16.04 LTS + Azure IoT Edge 執行階段**以開始 VM 建立工作流程。 從該處完成上述「從 Azure Marketplace 部署」指示的步驟 3 和 4。

## <a name="deploy-from-azure-cli"></a>從 Azure CLI 進行部署

1. 如果您在桌面上使用 Azure CLI，從登入下列位置開始：

   ```azurecli-interactive
   az login
   ```

1. 如果您有多個訂用帳戶，請選取想要使用的訂用帳戶：
   1. 列出您的訂用帳戶：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 複製您想要使用之訂用帳戶的 [SubscriptionID] 欄位。

   1. 使用您剛才複製的識別碼來設定您的工作訂用帳戶：

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 建立新的資源群組 (或在下一個步驟中指定現有的資源群組)：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 接受虛擬機器的使用規定。 如果您想要先檢查這些條款，請遵循[從 Azure Marketplace 部署](#deploy-from-the-azure-marketplace)中的步驟進行。

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. 建立新的虛擬機器：

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. 設定裝置連接字串（如果您不熟悉此程式，您可以遵循[使用 Azure CLI 程式抓取連接字串](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli)）：

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

如果您想要在設定之後使用 SSH 連線到這個 VM，請使用 publicIpAddress 與命令：`ssh azureuser@{publicIpAddress}`

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您有 IoT Edge 執行時間正確安裝的問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

如果您想要開啟埠以透過 SSH 或其他輸入連線來存取 VM，請參閱 Azure 虛擬機器檔，以瞭解[如何對 LINUX VM 開啟埠和端點](../virtual-machines/linux/nsg-quickstart.md)
