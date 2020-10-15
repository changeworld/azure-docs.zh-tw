---
title: 在適用于 IoT 的 Azure Defender 中部署管理主控台
description: 瞭解如何在適用于 IoT 的 Azure Defender 中部署管理主控台。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094318"
---
# <a name="deploy-the-management-console"></a>部署管理主控台
本文說明如何部署適用于 IoT 的 Azure Defender 內部部署管理主控台。

## <a name="the-on-premises-management-console"></a>內部部署管理主控台

內部部署管理主控台提供所有網路資產的匯總視圖，並提供重要 IoT 的即時觀點，以及所有設備的風險指標和警示。 與 SOC 工作流程和執行手冊緊密整合，可讓您輕鬆地排定風險降低的風險和跨網站的相互關聯。

- 透過單一整合的平臺進行資產管理、風險和弱點管理，以及使用事件回應進行威脅監視，來全面降低複雜度。

- 匯總和相互關聯–顯示、匯總及分析從所有網站收集的資料和警示。

- 控制所有感應器–設定和監視單一位置的所有感應器。

   ![適用于 IoT 的 Azure Defender 網站管理檢視](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>部署內部部署管理主控台設備

此程式需要取得您自己的硬體和安裝軟體。 解決方案會在認證的裝置上執行。 購買您的認證設備時，請參閱 [Azure Defender For IoT 硬體規格指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 作為參考。

如需下載 ISO 映像和安裝感應器軟體的詳細資訊，請參閱 [Azure Defender For IoT 安裝指南](https://aka.ms/AzureDefenderforIoTInstallSensorISO) 。

**部署內部部署管理主控台：**

1. 流覽至適用于 IoT 的 Microsoft Azure Defender。

2. 選取 [內部 **部署] 管理主控台**。

   ![適用于 IoT 的 Azure Defender 內部部署管理主控台視圖](media/updates/image15.png)

3. 從 [ **選取版本** ] 功能表選取3.1 版。

4. 選取 [ **下載** 並儲存檔案]。

5. 安裝軟體之後，請執行網路設定工作。 如需詳細資訊，請參閱適用于 [IoT 的 Azure Defender 網路設定指南](https://aka.ms/AzureDefenderForIoTNetworkSetup) 。

## <a name="next-steps"></a>後續步驟

若要深入瞭解設定選項，請繼續進行模組設定的操作指南。
> [!div class="nextstepaction"]
> [模組設定操作指南](./how-to-agent-configuration.md)
