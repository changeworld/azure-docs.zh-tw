---
title: 透過 Windows PowerShell 介面，連接到 Microsoft Azure Stack Edge Pro 裝置並加以管理 |Microsoft Docs
description: 說明如何透過 Windows PowerShell 介面連接到 Azure Stack Edge Pro，然後加以管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 93678735237c25b19d04b7d901583ba785d7f594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613538"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>透過 Windows PowerShell 管理 Azure Stack Edge Pro FPGA 裝置

Azure Stack Edge Pro 解決方案可讓您處理資料，並透過網路傳送至 Azure。 本文說明 Azure Stack Edge Pro 裝置的一些設定和管理工作。 您可以使用 Azure 入口網站、本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

本文著重于您使用 PowerShell 介面進行的工作。 

本文包含下列程式：

- 連線至 Powershell 介面
- 建立支援封裝
- Upload certificate
- 重設裝置
- 查看裝置資訊
- 取得計算記錄
- 監視計算模組並進行疑難排解

## <a name="connect-to-the-powershell-interface"></a>連線至 Powershell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

您也可以上傳 IoT Edge 憑證，以在您的 IoT Edge 裝置與可連線的下游裝置之間啟用安全連線。 有三個檔案 (*pem* 格式) 您需要安裝：

- 根 CA 憑證或擁有者 CA
- 裝置 CA 憑證
- 裝置私密金鑰 

下列範例顯示如何使用此 Cmdlet 來安裝 IoT Edge 憑證：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
當您執行這個 Cmdlet 時，系統會提示您提供網路共用的密碼。

如需憑證的詳細資訊，請移至 [Azure IoT Edge 憑證](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) 或 [在閘道上安裝憑證](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看裝置資訊
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重設裝置

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>取得計算記錄

如果您的裝置上已設定計算角色，您也可以透過 PowerShell 介面取得計算記錄。

1. [連接到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用 `Get-AzureDataBoxEdgeComputeRoleLogs` 來取得裝置的計算記錄。

    下列範例顯示此 Cmdlet 的使用方式：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    以下是用於 Cmdlet 的參數描述：
    - `Path`：提供您要在其中建立計算記錄封裝之共用的網路路徑。
    - `Credential`：提供網路共用的使用者名稱。 當您執行這個 Cmdlet 時，您將需要提供共用密碼。
    - `FullLogCollection`：此參數可確保記錄封裝將包含所有計算記錄檔。 根據預設，記錄封裝只會包含記錄的子集。

## <a name="monitor-and-troubleshoot-compute-modules"></a>監視計算模組並進行疑難排解

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>結束遠端會話

若要結束遠端 PowerShell 會話，請關閉 PowerShell 視窗。

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)。
