---
title: 使用 DPS 自動預配 Windows 設備 - Azure IoT 邊緣 |微軟文檔
description: 在您的 Windows 機器上使用模擬裝置，以透過裝置佈建服務測試 Azure IoT Edge 的自動裝置佈建
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4f01c3ec57b0cf9e3ecf47254b57be95ea051a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510935"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>在 Windows 上使用虛擬 TPM 創建和預配類比 IoT 邊緣設備

可以使用[設備預配服務](../iot-dps/index.yml)自動預配 Azure IoT 邊緣設備，就像未啟用邊緣的設備一樣。 如果您不熟悉自動預配過程，請查看[自動預配概念](../iot-dps/concepts-auto-provisioning.md)，然後再繼續。

DPS 支援在單個註冊和組註冊中對 IoT Edge 設備的對稱金鑰認證。 對於組註冊，如果選中"是 IoT 邊緣設備"選項在對稱金鑰證明中為 true，則在該註冊組下註冊的所有設備都將標記為 IoT Edge 設備。

本文演示如何在類比 IoT Edge 設備上測試自動設定，這些步驟包括：

* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 在 Windows 機器上建立模擬裝置與保護硬體的模擬信任平台模組 (TPM)。
* 建立裝置的個別註冊。
* 安裝 IoT Edge 執行階段，並將裝置連線到 IoT 中樞。

> [!TIP]
> 本文介紹了在虛擬裝置上使用 TPM 認證來測試自動設定，但其中的大部分也適用于使用物理 TPM 硬體時。

## <a name="prerequisites"></a>Prerequisites

* Windows 開發機器。 本文使用 Windows 10。
* 使用中的 IoT 中樞。

> [!NOTE]
> 在 DPS 中使用 TPM 認證時，需要 TPM 2.0，只能用於創建單個註冊，而不是組註冊。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

> [!TIP]
> 如果您使用的是物理 TPM 設備，則需要確定**背書金鑰**，這是每個 TPM 晶片的唯一金鑰，並且是從與其關聯的 TPM 晶片製造商獲得的。 例如，您可以通過創建背書金鑰的 SHA-256 雜湊來派生 TPM 設備的唯一**註冊 ID。**
>
> 按照"[如何使用 Azure 門戶管理設備註冊](../iot-dps/how-to-manage-enrollments.md)"中的說明在 DPS 中創建註冊，然後繼續執行本文中的["安裝 IoT Edge 運行時](#install-the-iot-edge-runtime)"部分以繼續。

## <a name="simulate-a-tpm-device"></a>模擬 TPM 裝置

在 Windows 開發機器上建立模擬 TPM 裝置。 檢索設備的**註冊 ID**和**背書金鑰**，並使用它們在 DPS 中創建單個註冊條目。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

選擇您想要用來建立模擬裝置的 SDK 語言並遵循步驟，直到您建立個別註冊。

創建單個註冊時，選擇**True**以聲明 Windows 開發電腦上的類比 TPM 設備是**IoT 邊緣設備**。

模擬裝置和個別註冊指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [JAVA](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

建立個別註冊之後，儲存 [註冊識別碼]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

預配設備時，您需要以下資訊：

* DPS **ID 範圍**值
* 您創建的設備**註冊 ID**

在運行類比 TPM 的設備上安裝 IoT 邊緣運行時。 您將為自動（而不是手動）預配配置 IoT Edge 運行時。

> [!TIP]
> 在安裝和測試期間，請將執行 TPM 模擬器的視窗保持開啟。

有關在 Windows 上安裝 IoT Edge 的更多詳細資訊，包括管理容器和更新 IoT Edge 等任務的先決條件和說明，請參閱[在 Windows 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝物聯網邊緣時，請務必使用 AMD64 電源外殼會話，而不是 PowerShell （x86）。

1. **Deploy-IoTEdge**命令檢查 Windows 電腦是否位於受支援的版本上，打開容器功能，然後下載 moby 運行時和 IoT Edge 運行時。 該命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時，IoT Core 設備可能會自動重新開機。 其他 Windows 10 或 Windows 伺服器設備可能會提示您重新開機。 如果是，請立即重新開機設備。 設備準備就緒後，再次以管理員身份運行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令預設為 Windows 容器的手動佈建。 使用標誌`-Dps`使用設備預配服務而不是手動預配。

   將 和 的`{scope_id}``{registration_id}`預留位置值替換為您之前收集的資料。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。 請在您的裝置上使用下列命令，確認執行階段已成功安裝並啟動。  

檢查 IoT Edge 服務的狀態。

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 了解如何[使用 Azure 入口網站大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 執行相同作業
