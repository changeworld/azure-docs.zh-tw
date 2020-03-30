---
title: 在 Windows 伺服器虛擬機器上運行 Azure IoT 邊緣 |微軟文檔
description: Windows 伺服器應用商店虛擬機器上的 Azure IoT 邊緣設置說明
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045916"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>在 Windows 伺服器虛擬機器上運行 Azure IoT 邊緣

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出了使用[Windows 伺服器](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)Azure 應用商店產品/服務在 Windows 伺服器 2019 虛擬機器上運行 Azure IoT 邊緣運行時的步驟。 按照在 Windows 上安裝[Azure IoT 邊緣運行時](how-to-install-iot-edge-windows.md)的說明進行操作，以便與其他版本一起使用。

## <a name="deploy-from-the-azure-marketplace"></a>從 Azure Marketplace 進行部署

1. 導航到[Windows 伺服器](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)Azure 應用商店產品/服務或在[Azure 應用商店](https://azuremarketplace.microsoft.com/)上搜索"Windows 伺服器"
2. 選擇**立即獲取它**
3. 在**軟體計畫中**，找到"Windows 伺服器 2019 資料中心伺服器核心與容器"，然後在下一個對話方塊中選擇"**繼續**"。
    * 您還可以使用這些說明的其他版本的 Windows 伺服器與容器
4. 一旦進入 Azure 入口網站，選取 [建立]**** 並且遵循精靈以部署 VM。
    * 如果這是您第一次嘗試 VM，則最容易使用密碼並在公共入站埠功能表中啟用 RDP 和 SSH。
    * 如果您有資源密集工作負載，您應該藉由新增更多的 CPU 和/或記憶體來升級虛擬機器大小。
5. 一旦部署虛擬機器，將它設定為連線到您的 IoT 中樞：
    1. 從在 IoT 中心創建的 IoT 邊緣設備複製設備連接字串。 請參閱在[Azure 門戶中檢索連接字串](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)的過程。
    1. 從 Azure 入口網站選取您新建立的虛擬機器資源，然後開啟**執行命令**選項
    1. 選擇**運行電源外殼腳本**選項
    1. 使用設備連接字串將此腳本複製到命令視窗中：

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. 執行腳本以安裝 IoT Edge 運行時，並通過選擇 **"運行"** 設置連接字串
    1. 一兩分鐘後，您應該會看到一條消息，指出邊緣運行時已成功安裝和預配。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 門戶部署

1. 從 Azure 門戶中搜索"Windows 伺服器"並選擇**Windows 伺服器 2019 資料中心**以開始 VM 創建工作流。
2. 從**選擇軟體計畫**選擇"Windows 伺服器 2019 資料中心伺服器核心與容器"，然後選擇**創建**
3. 完成上面"從 Azure 應用商店部署"說明中的步驟 5。

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
   1. 使用複製的 ID 運行此命令：

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

   * 此命令將提示您輸入密碼，但您可以添加選項`--admin-password`以更輕鬆地在腳本中設置密碼
   * Windows Server Core 映射僅支援遠端桌面，因此，如果您希望獲得完整的桌面體驗，請指定`MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest`為映射

1. 設置設備連接字串（如果您不熟悉此過程，可以按照[Azure CLI 過程檢索連接字串](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli)）：

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果邊緣運行時安裝正確出現問題，請查看[故障排除](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

詳細瞭解在[Windows 虛擬機器文檔中](https://docs.microsoft.com/azure/virtual-machines/windows/)使用 Windows 虛擬機器。

如果要在設置後將 SSH 引入此 VM，請按照使用遠端桌面或遠端電源外殼[安裝 Windows 伺服器的 OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell)指南進行操作。
