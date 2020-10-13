---
title: 使用 DPS 自動布建 Windows 裝置-Azure IoT Edge |Microsoft Docs
description: 在您的 Windows 機器上使用模擬裝置，以透過裝置佈建服務測試 Azure IoT Edge 的自動裝置佈建
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c010fa4ea0289ed91f439a250f0b63703517f5bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447781"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>使用虛擬 TPM 在 Windows 上建立及布建模擬的 IoT Edge 裝置

Azure IoT Edge 裝置可以使用裝置布建 [服務](../iot-dps/index.yml) 來自動布建，就像未啟用 Edge 的裝置一樣。 如果您不熟悉自動佈建程序，請先檢閱[佈建](../iot-dps/about-iot-dps.md#provisioning-process)概觀，再繼續作業。

DPS 支援個別註冊和群組註冊中 IoT Edge 裝置的對稱金鑰證明。 針對群組註冊，如果您在對稱金鑰證明中核取 [在 IoT Edge 裝置] 選項為 true，則在該註冊群組下註冊的所有裝置都會標示為 IoT Edge 裝置。

本文說明如何使用下列步驟，在模擬的 IoT Edge 裝置上測試自動布建：

* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 在 Windows 機器上建立模擬裝置與保護硬體的模擬信任平台模組 (TPM)。
* 建立裝置的個別註冊。
* 安裝 IoT Edge 執行階段，並將裝置連線到 IoT 中樞。

> [!TIP]
> 本文說明如何在虛擬裝置上使用 TPM 證明測試自動布建，但大部分的情況下也適用于使用實體 TPM 硬體。

## <a name="prerequisites"></a>必要條件

* Windows 開發機器。 本文使用 Windows 10。
* 使用中的 IoT 中樞。

> [!NOTE]
> 使用 TPM 證明搭配 DPS 時需要 TPM 2.0，而且只能用來建立個別的（而非群組）註冊。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

> [!TIP]
> 如果您使用的是實體 TPM 裝置，您需要判斷 **簽署金鑰**，這對每個 tpm 晶片而言是唯一的，而且是從與其相關聯的 tpm 晶片製造商取得。 例如，您可以建立 TPM 裝置的唯一 **註冊識別碼** ，例如建立簽署金鑰的 256 sha-1 雜湊。
>
> 遵循本文中的指示， [瞭解如何使用 Azure 入口網站管理裝置](../iot-dps/how-to-manage-enrollments.md) 註冊，以在 DPS 中建立註冊，然後繼續進行本文中的 [安裝 IoT Edge 運行](#install-the-iot-edge-runtime) 時間一節，以繼續進行。

## <a name="simulate-a-tpm-device"></a>模擬 TPM 裝置

在 Windows 開發機器上建立模擬 TPM 裝置。 取得裝置的 **註冊識別碼** 和 **簽署金鑰** ，並使用它們在 DPS 中建立個別的註冊專案。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

選擇您想要用來建立模擬裝置的 SDK 語言並遵循步驟，直到您建立個別註冊。

當您建立個別註冊時，請選取 [ **True** ] 以宣告 Windows 開發電腦上的模擬 TPM 裝置是 **IoT Edge 裝置**。

> [!TIP]
> 在 Azure CLI 中，您可以建立 [註冊](/cli/azure/ext/azure-iot/iot/dps/enrollment) 或 [註冊群組](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) ，並使用 **啟用 edge** 的旗標來指定裝置或裝置群組是 IoT Edge 裝置。

模擬裝置和個別註冊指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

建立個別註冊之後，儲存 [註冊識別碼]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

布建您的裝置時，您將需要下列資訊：

* DPS **識別碼範圍** 值
* 您建立的裝置**註冊識別碼**

在正在執行模擬 TPM 的裝置上安裝 IoT Edge 執行時間。 您將設定 IoT Edge 執行時間進行自動、非手動、布建。

> [!TIP]
> 在安裝和測試期間，請將執行 TPM 模擬器的視窗保持開啟。

如需有關在 Windows 上安裝 IoT Edge 的詳細資訊，包括管理容器和更新 IoT Edge 等工作的必要條件和指示，請參閱 [在 windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝 IoT Edge 時，請務必使用 PowerShell 的 AMD64 會話，而不是 PowerShell (x86) 。

1. **IoTEdge**命令會檢查您的 Windows 電腦是否位於支援的版本、開啟容器功能，然後下載 moby 執行時間和 IoT Edge 執行時間。 命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時，IoT 核心裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 當您的裝置準備就緒之後，請再次以系統管理員身分執行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令預設為 Windows 容器的手動佈建。 使用 `-Dps` 旗標來使用裝置布建服務，而不是手動布建。

   `{scope_id}` `{registration_id}` 使用您稍早收集到的資料來取代和的預留位置值。

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

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 了解如何[使用 Azure 入口網站大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 執行相同作業
