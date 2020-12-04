---
title: Microsoft Azure 資料箱閘道的系統需求 | Microsoft Docs
description: 了解 Azure 資料箱閘道的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: aadaedfd2c9ecf544d142e42a0fbeb410324b7d8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581923"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure 資料箱閘道系統需求

本文會針對 Microsoft Azure 資料箱閘道解決方案以及連線至 Azure 資料箱閘道的用戶端，說明其各自的重要系統需求。 建議您先仔細檢閱此資訊再部署資料箱閘道，之後在進行部署和後續作業期間若有必要，也請回頭查閱。 

資料箱閘道虛擬裝置的系統需求包括：

- **主機的軟體需求** - 說明支援的平台、本機組態 UI 的瀏覽器、SMB 用戶端，以及可以連線到裝置的主機其他需求。
- **裝置的網路需求** - 提供虛擬裝置作業的任何網路需求相關資訊。


## <a name="specifications-for-the-virtual-device"></a>虛擬裝置的規格

「資料箱閘道」的基礎主機系統能夠將下列資源專門用來佈建您的虛擬裝置：

| 規格                                          | 描述              |
|---------------------------------------------------------|--------------------------|
| 虛擬處理器 (核心)   | 最少 4 個 |
| 記憶體  | 最少 8 GB|
| 可用性|單一節點|
| 磁碟| OS 磁碟：250 GB <br> 資料磁碟：最少 2 TB、精簡佈建且必須以 SSD 為後盾|
| 網路介面|1 或多個虛擬網路介面|


## <a name="supported-os-for-clients-connected-to-device"></a>支援的 OS (適用於連線至裝置的用戶端)

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>支援的通訊協定 (適用於存取裝置的用戶端)

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>裝置的受支援虛擬化平台

| **作業系統/平台**  |**版本**   |**注意事項**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |不支援 VMware 工具。         |


## <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本機 Web UI 的受支援瀏覽器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>網路連接埠需求

下表列出必須在您的防火牆中開啟的連接埠，以允許 SMB、雲端或管理流量。 在這個資料表中，in 或 inbound 指的是輸入用戶端要求存取裝置的方向。 Out 或 outbound 指的是資料箱閘道裝置於外部傳送資料至部署之上的方向：例如，輸出到網際網路。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。 您的資料箱閘道裝置和資料箱閘道服務取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶和 Microsoft Update 伺服器。 與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。 請務必了解與這些應用程式相關聯的 URL 模式可以變更。 接著，您將需要網路系統管理員監控資料箱閘道的防火牆規則，並在需要時更新。

我們建議您在大部分情況下，根據資料箱閘道固定 IP 位址為輸出流量設定防火牆規則。 不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> - 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> - 目的地 Ip 應設定為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Azure Government 的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>網際網路頻寬

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>後續步驟

* [部署 Azure 資料箱閘道](data-box-gateway-deploy-prep.md)

