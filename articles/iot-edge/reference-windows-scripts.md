---
title: 適用于 Azure IoT Edge 的 Windows 腳本 |Microsoft Docs
description: 在 Windows 裝置上安裝、卸載或更新 IoT Edge PowerShell 腳本的參考資訊
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2878d682d0f2025a50f26baf87476f66aa236e2c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630611"
---
# <a name="powershell-scripts-for-iot-edge-on-windows"></a>Windows 上適用于 IoT Edge 的 PowerShell 腳本

瞭解在 Windows 裝置上安裝、更新或卸載 IoT Edge 的 PowerShell 腳本。

本文所述的命令來自于 `IoTEdgeSecurityDaemon.ps1` 每個 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)發行的檔案。 腳本的最新版本一律可在 aka.ms/iotedge-win 取得。

您可以使用 Cmdlet 來執行任何命令 `Invoke-WebRequest` ，以存取最新的腳本版本。 例如：

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

您也可以下載此腳本或特定版本的腳本版本，以執行命令。 例如：

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

提供的腳本已簽署來提高安全性。 您可以藉由將腳本下載到您的裝置，然後執行下列 PowerShell 命令來驗證簽章：

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

如果簽章經過驗證，輸出狀態就會是 **有效** 的。

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 命令會下載並部署 IoT Edge 安全性 Daemon 及其相依性。 部署命令會接受這些一般參數，以及其他參數。 如需完整清單，請使用命令 `Get-Help Deploy-IoTEdge -full` 。  

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器作業系統，Windows 會是預設值。<br><br>針對 Windows 容器，IoT Edge 使用安裝所包含的 Moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓部署腳本重新開機電腦，而不需要提示。 |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 命令會使用您的裝置連接字串和操作詳細資料來設定 IoT Edge。 此命令所產生的大部分資訊都會儲存在 iotedge\config.yaml 檔案中。 初始化命令會接受這些一般參數，以及其他參數。 如需完整清單，請使用命令 `Get-Help Initialize-IoTEdge -full` 。

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ManualConnectionString** | 無 | **切換參數**。 **預設值**。 如果未指定任何布建類型，則使用連接字串進行手動布建是預設值。<br><br>宣告您將提供裝置連接字串以手動布建裝置。 |
| **ManualX509** | 無 | **切換參數**。 如果未指定任何布建類型，則使用連接字串進行手動布建是預設值。<br><br>宣告您將提供身分識別憑證和私密金鑰，以手動方式布建裝置。
| **DpsTpm** | 無 | **切換參數**。 如果未指定任何布建類型，則使用連接字串進行手動布建是預設值。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **DpsSymmetricKey** | 無 | **切換參數**。 如果未指定任何布建類型，則使用連接字串進行手動布建是預設值。<br><br>宣告您將提供裝置布建服務 (DPS) 範圍識別碼和裝置的註冊識別碼，以透過 DPS 布建，以及用於證明的對稱金鑰。 |
| **DpsX509** | 無 | **切換參數**。 如果未指定任何布建類型，則使用連接字串進行手動布建是預設值。<br><br>宣告您將提供裝置布建服務 (DPS) 範圍識別碼和裝置的註冊識別碼，以透過 DPS 布建，以及用於證明的 x.509 身分識別憑證和私密金鑰。  |
| **DeviceConnectionString** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | **需要** 使用連接字串進行手動布建。 如果您未在腳本參數中提供連接字串，系統會提示您輸入一個。 |
| **IotHubHostName** | 裝置連接的 IoT 中樞主機名稱。 | **需要** 使用 x.509 憑證進行手動布建。 採用格式 *{hub name}. azure-devices.net*。 |
| **DeviceId** | IoT 中樞內已註冊裝置身分識別的裝置識別碼。 | **需要** 使用 x.509 憑證進行手動布建。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | DPS 布建 **所需**。 如果您未在腳本參數中提供範圍識別碼，系統會提示您輸入一個。 |
| **RegistrationId** | 由您裝置產生的註冊識別碼 | 如果使用 TPM 或對稱金鑰證明，則為 DPS 布建 **所需**。 如果使用 x.509 憑證證明，則為 **選擇性**。 |
| **X509IdentityCertificate** | 裝置上 x.509 裝置身分識別憑證的 URI 路徑。 | 如果使用 x.509 憑證證明，手動或 DPS 布建都 **需要**。 |
| **X509IdentityPrivateKey** | 裝置上 x.509 裝置身分識別憑證金鑰的 URI 路徑。 | 如果使用 x.509 憑證證明，手動或 DPS 布建都 **需要**。 |
| **SymmetricKey** | 使用 DPS 時，用來布建 IoT Edge 裝置身分識別的對稱金鑰 | 如果使用對稱金鑰證明，則為 DPS 布建 **所需**。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器作業系統，Windows 會是預設值。<br><br>針對 Windows 容器，IoT Edge 使用安裝所包含的 Moby 容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **DeviceCACertificate** | 裝置上 x.509 裝置 CA 憑證的 URI 路徑。 | 也可以在檔案中設定 `C:\ProgramData\iotedge\config.yaml` 。 如需詳細資訊，請參閱 [管理 IoT Edge 裝置上的憑證](how-to-manage-device-certificates.md)。 |
| **DeviceCAPrivateKey** | 裝置上 x.509 裝置 CA 私密金鑰的 URI 路徑。 | 也可以在檔案中設定 `C:\ProgramData\iotedge\config.yaml` 。 如需詳細資訊，請參閱 [管理 IoT Edge 裝置上的憑證](how-to-manage-device-certificates.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者名稱** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 |

## <a name="update-iotedge"></a>Update-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定任何容器 OS，則 Windows 會是預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數，安裝程式會檢查列出的目錄中是否有安裝所需的 IoT Edge cab 和 VC Runtime MSI 檔案。 在目錄中找不到的任何檔案都會下載。 如果這兩個檔案都在目錄中，您可以在沒有網際網路連線的情況下安裝 IoT Edge。 您也可以使用此參數來使用特定版本。 |
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓部署腳本重新開機電腦，而不需要提示。 |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **力** | 無 | 此旗標會強制卸載，以防先前嘗試卸載失敗。
| **RestartIfNeeded** | 無 | 必要時，此旗標可讓卸載腳本重新開機電腦，而不需要提示。 |

## <a name="next-steps"></a>下一步

在下列文章中瞭解如何使用這些命令：

* [安裝或卸載 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)
