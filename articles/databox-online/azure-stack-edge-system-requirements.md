---
title: Microsoft Azure Stack 邊緣系統需求 |Microsoft Docs
description: 瞭解 Azure Stack 邊緣的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 63225f6e6daa6b3cdde7aa505c117b43305a4867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82569078"
---
# <a name="azure-stack-edge-system-requirements"></a>Azure Stack Edge 系統需求

本文說明 Microsoft Azure Stack 邊緣解決方案的重要系統需求，以及連接到 Azure Stack Edge 的用戶端。 我們建議您先仔細閱讀資訊，然後再部署 Azure Stack Edge。 您可以在部署和後續作業期間，視需要回來參考此資訊。

Azure Stack Edge 的系統需求包括：

- **主機的軟體需求**：說明支援的平台、本機設定 UI 的瀏覽器、SMB 用戶端，以及可存取裝置的用戶端其他需求。
- **裝置的網路需求**：提供實體裝置作業的任何網路需求相關資訊。

## <a name="supported-os-for-clients-connected-to-device"></a>支援的 OS (適用於連線至裝置的用戶端)

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>支援的通訊協定 (適用於存取裝置的用戶端)

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本機 Web UI 的受支援瀏覽器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>網路連接埠需求

### <a name="port-requirements-for-azure-stack-edge"></a>Azure Stack 邊緣的埠需求

下表列出必須在您的防火牆中開啟的連接埠，以允許 SMB、雲端或管理流量。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 [ *Out* ] 或 [*輸出*] 是指您的 Azure Stack Edge 裝置在外部將資料傳送到部署之外的方向，例如，輸出到網際網路。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的連接埠需求

Azure IoT Edge 允許使用支援的 IoT Hub 通訊協定，進行從內部部署 Edge 裝置到 Azure 雲端的輸出通訊。 只有針對 Azure IoT 中樞需要將訊息向下推送到 Azure IoT Edge 裝置的特定案例，才需要輸入通訊。

針對裝載 Azure IoT Edge 執行階段的伺服器，使用下表來進行連接埠設定：

| 連接埠號碼 | 內或外 | 連接埠範圍 | 必要 | 指引 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 外       | WAN        | 是      | 開啟來佈建 IoT Edge 的輸出。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。|

如需完整資訊，請參閱[適用於 IoT Edge 部署的防火牆和連接埠設定規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。 您的 Azure Stack Edge 裝置和服務取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶及 Microsoft Update 伺服器。 與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。 請務必了解與這些應用程式相關聯的 URL 模式可以變更。 這些變更需要網路系統管理員視需要監視和更新 Azure Stack 邊緣的防火牆規則。

我們建議您根據 Azure Stack 邊緣固定 IP 位址（在大部分情況下為壓縮），設定輸出流量的防火牆規則。 不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> - 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> - 目的地 IP 應該設為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>閘道功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>計算功能的 URL 模式

| URL 模式                      | 元件或功能                     |   
|----------------------------------|---------------------------------------------|
| HTTPs： \/ /mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器登錄 (必要)               |
| https://\*.azurecr.io                     | 個人和協力廠商容器登錄 (選擇性) | 
| https://\*.azure-devices.net              | IoT 中樞存取權 (必要)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government 閘道的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 計算的 URL 模式

| URL 模式                      | 元件或功能                     |  
|----------------------------------|---------------------------------------------|
| HTTPs： \/ /mcr.microsoft.com<br></br>HTTPs:// \* . cdn.mscr.com | Microsoft 容器登錄 (必要)               |
| HTTPs:// \* . azure-devices.us              | IoT 中樞存取權 (必要)           |
| HTTPs:// \* . azurecr.us                    | 個人和協力廠商容器登錄 (選擇性) | 

## <a name="internet-bandwidth"></a>網際網路頻寬

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>計算大小考慮

在開發和測試您的解決方案時使用您的體驗，以確保 Azure Stack Edge 裝置上有足夠的容量，並從您的裝置獲得最佳效能。

您應該考慮的因素包括：

- **容器細節**-請考慮下列各項。

    - 您的工作負載中有多少容器？ 您可以有很多輕量的容器，而不是需要大量資源的容器。
    - 配置給這些容器的資源為何，以及它們耗用的資源為何？
    - 您的容器共用多少層級？
    - 是否有未使用的容器？ 已停止的容器仍會佔用磁碟空間。
    - 您的容器會在哪種語言中撰寫？
- 已**處理的資料大小**-您的容器會處理多少資料？ 這項資料會耗用磁碟空間，還是會在記憶體中處理資料？
- **預期的效能**-您的解決方案所需的效能特性為何？ 

若要瞭解並精簡解決方案的效能，您可以使用：

- Azure 入口網站中可用的計算計量。 移至您的 Azure Stack Edge 資源，然後移至 [監視] [ **> 計量**]。 查看 [ **edge 計算-記憶體使用量**] 和 [ **EDGE 計算-CPU 百分比**] 以瞭解可用的資源，以及資源的使用方式。
- 透過裝置的 PowerShell 介面提供的監視命令，例如：

    - `dkr`用來取得容器資源使用量統計資料即時資料流的統計資料。 命令支援 CPU、記憶體使用量、記憶體限制和網路 IO 計量。
    - `dkr system df`取得所用磁碟空間量的相關資訊。 
    - `dkr image [prune]`清除未使用的映射並釋出空間。
    - `dkr ps --size`以查看執行中容器的大約大小。 

    如需可用命令的詳細資訊，請移至[監視和疑難排解計算模組](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)。

最後，請確定您在資料集上驗證您的解決方案，並在生產環境中部署之前，先量化 Azure Stack 邊緣的效能。


## <a name="next-step"></a>下一步

- [部署您的 Azure Stack Edge](azure-stack-edge-deploy-prep.md)
