---
title: Microsoft Azure Stack Edge 系統需求 |Microsoft Docs
description: 瞭解 Microsoft Azure Stack Edge 解決方案的系統需求，以及連接到 Azure Stack Edge 的用戶端。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: d835507a17417f8b500c0fc13d0a662e606a37ff
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996409"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>使用 GPU Azure Stack Edge Pro 的系統需求 

本文說明 Microsoft Azure Stack Edge Pro 解決方案的重要系統需求，以及連接到 Azure Stack Edge Pro 的用戶端。 建議您在部署 Azure Stack Edge Pro 之前仔細檢查資訊。 您可以在部署和後續作業期間，視需要回來參考此資訊。

Azure Stack Edge Pro 的系統需求包括：

- **主機的軟體需求**：說明支援的平台、本機設定 UI 的瀏覽器、SMB 用戶端，以及可存取裝置的用戶端其他需求。
- **裝置的網路需求**：提供實體裝置作業的任何網路需求相關資訊。

## <a name="supported-os-for-clients-connected-to-device"></a>支援的 OS (適用於連線至裝置的用戶端)

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>支援的通訊協定 (適用於存取裝置的用戶端)

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>支援的 Azure 儲存體帳戶

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>支援的 Edge 儲存體帳戶

裝置的 REST 介面支援下列 Edge 儲存體帳戶。 Edge 儲存體帳戶會建立于裝置上。 如需詳細資訊，請參閱 [Edge 儲存體帳戶](azure-stack-edge-j-series-manage-storage-accounts.md#about-edge-storage-accounts)。

|類型  |儲存體帳戶  |評價  |
|---------|---------|---------|
|標準     |GPv1：區塊 Blob         |         |

* 目前不支援分頁 blob 和 Azure 檔案儲存體。

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>支援的本機 Azure Resource Manager 儲存體帳戶

當您連接到本機 Azure Resource Manager 時，會透過裝置本機 Api 建立這些儲存體帳戶。 以下是支援的儲存體帳戶：

|類型  |儲存體帳戶  |評價  |
|---------|---------|---------|
|標準     |GPv1：區塊 Blob、分頁 Blob        | SKU 類型為 Standard_LRS       |
|Premium     |GPv1：區塊 Blob、分頁 Blob        | SKU 類型為 Premium_LRS        |


## <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>本機 Web UI 的受支援瀏覽器

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>網路連接埠需求

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Azure Stack Edge Pro 的埠需求

下表列出必須在您的防火牆中開啟的連接埠，以允許 SMB、雲端或管理流量。 在這個資料表中，in** 或 inbound** 指的是輸入用戶端要求存取裝置的方向。 *輸出* 或 *輸出* 是指您的 Azure Stack Edge Pro 裝置在外部傳送資料的方向，例如，輸出到網際網路。

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的連接埠需求

Azure IoT Edge 允許使用支援的 IoT Hub 通訊協定，進行從內部部署 Edge 裝置到 Azure 雲端的輸出通訊。 只有針對 Azure IoT 中樞需要將訊息向下推送到 Azure IoT Edge 裝置的特定案例，才需要輸入通訊。

針對裝載 Azure IoT Edge 執行階段的伺服器，使用下表來進行連接埠設定：

| 連接埠號碼 | 內或外 | 連接埠範圍 | 必要 | 指引 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 外       | WAN        | 是      | 開啟來佈建 IoT Edge 的輸出。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。|

如需完整資訊，請參閱[適用於 IoT Edge 部署的防火牆和連接埠設定規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。 您 Azure Stack Edge Pro 裝置和服務取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶和 Microsoft Update 伺服器。 與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。 請務必了解與這些應用程式相關聯的 URL 模式可以變更。 這些變更需要網路系統管理員視需要監視和更新 Azure Stack Edge Pro 的防火牆規則。

建議您根據在大部分情況下壓縮的 Azure Stack Edge Pro 固定 IP 位址，設定輸出流量的防火牆規則。 不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> - 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> - 目的地 Ip 應設定為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>閘道功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>計算功能的 URL 模式

| URL 模式                      | 元件或功能                     |   
|----------------------------------|---------------------------------------------|
| HTTPs： \/ /mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器登錄 (必要)               |
| https://\*.azurecr.io                     | 個人和協力廠商容器登錄 (選擇性) | 
| https://\*.azure-devices.net              | IoT 中樞存取權 (必要)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure Government 閘道的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure Government 計算的 URL 模式

| URL 模式                      | 元件或功能                     |  
|----------------------------------|---------------------------------------------|
| HTTPs： \/ /mcr.microsoft.com<br></br>HTTPs:// \* . cdn.mscr.com | Microsoft 容器登錄 (必要)               |
| HTTPs:// \* . azure-devices.us              | IoT 中樞存取權 (必要)           |
| HTTPs:// \* . azurecr.us                    | 個人和協力廠商容器登錄 (選擇性) | 

## <a name="internet-bandwidth"></a>網際網路頻寬

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>計算大小考慮

在開發和測試您的解決方案時使用您的體驗，以確保您的 Azure Stack Edge Pro 裝置上有足夠的容量，而且您可以從裝置獲得最佳效能。

您應考慮的因素包括：

- **容器詳細** 資料-請考慮下列各項。

    - 您的容器使用量為何？ 您的容器耗用多少記憶體、儲存體和 CPU？
    - 您的工作負載中有多少個容器？ 您可以有大量的輕量容器，而不是一些耗用大量資源的容器。
    - 配置給這些容器的資源為何，以及它們耗用 (資源的資源量) ？
    - 您的容器共用多少層？ 容器映射是組織成一堆圖層的檔案組合。 針對您的容器映射，判斷要計算資源耗用量的層級和各自的大小。
    - 是否有未使用的容器？ 已停止的容器仍會佔用磁碟空間。
    - 您的容器在哪種語言中撰寫？
- **處理的資料大小** -您的容器要處理多少資料？ 這項資料會耗用磁碟空間，還是會在記憶體中處理資料？
- **預期的效能** -解決方案需要哪些效能特性？ 

若要瞭解並精簡您解決方案的效能，您可以使用：

- Azure 入口網站中提供的計算度量。 移至您的 Azure Stack Edge 資源，然後移至 [ **監視] > 計量**。 查看 **edge 計算-記憶體使用量** 和 **邊緣計算百分比 CPU** ，以瞭解可用的資源，以及如何使用資源。
- 若要監視計算模組並進行疑難排解，請移至 [Debug Kubernetes 問題](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)。

最後，請務必在您的資料集上驗證您的解決方案，並在生產環境中進行部署之前，先量化 Azure Stack Edge Pro 的效能。

## <a name="next-step"></a>後續步驟

- [部署 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
