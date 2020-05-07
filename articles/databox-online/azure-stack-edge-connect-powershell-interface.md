---
title: 透過 Windows PowerShell 介面連接到 Microsoft Azure Stack 邊緣裝置並加以管理 |Microsoft Docs
description: 說明如何透過 Windows PowerShell 介面連接到 Azure Stack Edge 並加以管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: ee69015b52024ba4edaaa414a0d9dda753510b3e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570092"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>透過 Windows PowerShell 管理 Azure Stack Edge 裝置

Azure Stack Edge 解決方案可讓您處理資料，並透過網路傳送至 Azure。 本文說明 Azure Stack Edge 裝置的一些設定和管理工作。 您可以使用 Azure 入口網站、本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

本文著重于您使用 PowerShell 介面進行的工作。 

本文包含下列程式：

- 連接到 PowerShell 介面
- 建立支援封裝
- Upload certificate
- 重設裝置
- 查看裝置資訊
- 取得計算記錄
- 監視和疑難排解計算模組

## <a name="connect-to-the-powershell-interface"></a>連接到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

您也可以上傳 IoT Edge 憑證，以在您的 IoT Edge 裝置與可連線的下游裝置之間啟用安全連線。 您需要安裝三個 IoT Edge 憑證（*pem*格式）：

- 根 CA 憑證或擁有者 CA
- 裝置 CA 憑證
- 裝置金鑰憑證

下列範例示範如何使用此 Cmdlet 來安裝 IoT Edge 憑證：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
當您執行此 Cmdlet 時，系統會提示您提供網路共用的密碼。

如需憑證的詳細資訊，請移至[Azure IoT Edge 憑證](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)或[在閘道上安裝憑證](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看裝置資訊
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重設裝置

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>取得計算記錄

如果您的裝置上已設定計算角色，您也可以透過 PowerShell 介面取得計算記錄。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-AzureDataBoxEdgeComputeRoleLogs`來取得裝置的計算記錄。

    下列範例顯示此 Cmdlet 的使用方式：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    以下是用於 Cmdlet 的參數說明：
    - `Path`：提供您要在其中建立計算記錄檔封裝之共用的網路路徑。
    - `Credential`：提供網路共用的使用者名稱。 當您執行此 Cmdlet 時，您必須提供共用密碼。
    - `FullLogCollection`：此參數可確保記錄檔封裝會包含所有計算記錄。 根據預設，記錄封裝只會包含記錄的子集。

## <a name="monitor-and-troubleshoot-compute-modules"></a>監視和疑難排解計算模組

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>結束遠端會話

若要結束遠端 PowerShell 會話，請關閉 PowerShell 視窗。

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署[Azure Stack Edge](azure-stack-edge-deploy-prep.md) 。
