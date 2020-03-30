---
title: 通過 Windows PowerShell 介面連接到和管理 Microsoft Azure 資料盒邊緣設備 |微軟文檔
description: 描述如何通過 Windows PowerShell 介面連接到資料框邊緣並管理資料框邊緣。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265476"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>通過 Windows PowerShell 管理 Azure 資料框邊緣設備

Azure 資料框邊緣解決方案允許您處理資料，並將其通過網路發送到 Azure。 本文介紹了資料盒邊緣設備的一些配置和管理工作。 您可以使用 Azure 門戶、本地 Web UI 或 Windows PowerShell 介面來管理設備。

本文重點介紹使用 PowerShell 介面執行的任務。

本文包括以下過程：

- 連接到 PowerShell 介面
- 建立支援封裝
- Upload certificate
- 重置設備
- 查看設備資訊
- 獲取計算日誌
- 監控和排除計算模組故障

## <a name="connect-to-the-powershell-interface"></a>連接到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

您還可以上載 IoT Edge 證書，以便在 IoT Edge 設備與可能連接到它的下游設備之間啟用安全連線。 您需要安裝三個 IoT 邊緣證書 *（.pem*格式）：

- 根 CA 憑證或擁有者 CA
- 裝置 CA 憑證
- 設備金鑰委付

下面的示例顯示了此 Cmdlet 安裝 IoT Edge 證書的用法：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
運行此 Cmdlet 時，系統將提示您提供網路共用的密碼。

有關證書的詳細資訊，請轉到[Azure IoT 邊緣證書](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)或在[閘道上安裝證書](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看設備資訊
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重設裝置

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>獲取計算日誌

如果在設備上配置了計算角色，您還可以通過 PowerShell 介面獲取計算日誌。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-AzureDataBoxEdgeComputeRoleLogs`獲取設備的計算日誌。

    下面的示例顯示了此 Cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    以下是用於 Cmdlet 的參數的說明：
    - `Path`：提供要創建計算日誌包的共用的網路路徑。
    - `Credential`：提供網路共用的使用者名。 運行此 Cmdlet 時，需要提供共用密碼。
    - `FullLogCollection`：此參數可確保日誌包包含所有計算日誌。 預設情況下，日誌包僅包含日誌的子集。

## <a name="monitor-and-troubleshoot-compute-modules"></a>監控和排除計算模組故障

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>退出遠端會話

要退出遠端 PowerShell 會話，關閉 PowerShell 視窗。

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。
