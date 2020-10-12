---
title: 在 Windows Server 虛擬機器上執行 Azure IoT Edge |Microsoft Docs
description: Windows Server Marketplace 虛擬機器上的 Azure IoT Edge 安裝指示
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494055"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>在 Windows Server 虛擬機器上執行 Azure IoT Edge

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出使用 [Windows server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace 供應專案在 windows server 2019 虛擬機器上執行 Azure IoT Edge 執行時間的步驟。 遵循在 Windows 上 [安裝 Azure IoT Edge 運行](how-to-install-iot-edge-windows.md) 時間以與其他版本搭配使用的指示。

## <a name="deploy-from-the-azure-marketplace"></a>從 Azure Marketplace 進行部署

1. 流覽至[Windows server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace 供應專案，或搜尋[Azure Marketplace](https://azuremarketplace.microsoft.com/)上的 [windows server]
2. 選取 **立即取得**
3. 在 [ **軟體方案**] 中，尋找 [具有容器的 Windows Server 2019 Datacenter server Core]，然後在下一個對話方塊中選取 [ **繼續** ]。
    * 您也可以將這些指示用於具有容器的其他 Windows Server 版本
4. 一旦進入 Azure 入口網站，選取 [建立]**** 並且遵循精靈以部署 VM。
    * 如果這是您第一次試用 VM，最簡單的方式是使用密碼，並在公用輸入埠功能表中啟用 RDP 和 SSH。
    * 如果您有資源密集工作負載，您應該藉由新增更多的 CPU 和/或記憶體來升級虛擬機器大小。
5. 一旦部署虛擬機器，將它設定為連線到您的 IoT 中樞：
    1. 從您在 IoT 中樞中建立的 IoT Edge 裝置複製您的裝置連接字串。 請參閱在 [Azure 入口網站中取出連接字串](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)的程式。
    1. 從 Azure 入口網站選取您新建立的虛擬機器資源，然後開啟**執行命令**選項
    1. 選取 **RunPowerShellScript** 選項
    1. 使用您的裝置連接字串，將此腳本複製到命令視窗中：

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. 執行腳本以安裝 IoT Edge 執行時間，並選取 [**執行**] 來設定您的連接字串
    1. 在一或兩分鐘之後，您應該會看到已成功安裝並布建 Edge 執行時間的訊息。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 入口網站部署

1. 從 Azure 入口網站搜尋「Windows Server」，然後選取 **Windows server 2019 Datacenter** 以開始建立 VM 工作流程。
2. 從 [**選取軟體方案**] 中，選擇 [具有容器的 Windows Server 2019 Datacenter server Core]，然後選取 [**建立**]
3. 完成上述「部署 Azure Marketplace」指示中的步驟5。

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

   1. 複製想要使用的訂用帳戶 SubscriptionID 欄位
   1. 使用您複製的識別碼來執行此命令：

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 建立新的資源群組 (或在下一個步驟中指定現有的資源群組)：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 建立新的虛擬機器：

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * 此命令會提示您輸入密碼，但您可以新增選項， `--admin-password` 以便更輕鬆地在腳本中進行設定
   * Windows Server Core 映射僅支援遠端桌面的命令列，所以如果您想要完整的桌面體驗，請指定 `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` 為映射

1. 如果您不熟悉此程式) ，請將裝置連接字串設定 (您可以遵循下列步驟來 [取得連接字串 Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) 程式：

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>接下來的步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您有 Edge 執行時間正確安裝的問題，請參閱 [疑難排解](troubleshoot.md) 頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

若要深入瞭解如何使用 Windows 虛擬機器，請 [參閱 Windows 虛擬機器檔](https://docs.microsoft.com/azure/virtual-machines/windows/)。

如果您想要在安裝後透過 SSH 連線到此 VM，請遵循使用遠端桌面或遠端 powershell [安裝適用于 Windows Server 的 OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) 指南。
