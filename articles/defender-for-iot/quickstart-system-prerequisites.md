---
title: 系統必要條件
description: 取得執行適用於 IoT 的 Azure Defender 所需的系統必要條件。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8ee3afcae69ca6c082452e590eb8370bcc122af4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844454"
---
# <a name="system-prerequisites"></a>系統必要條件
本文列出執行適用於 IoT 的 Azure Defender 的系統必要條件。

## <a name="minimum-requirements"></a>最低要求條件

- 透過 SPAN 連接埠支援流量監視的網路交換器。
- 適用於 NTA 感應器的硬體設備。
- Azure 訂用帳戶參與者角色。 只有在上線期間，才需要定義已認可的裝置並連線至 Azure Sentinel。
- Azure IoT 中樞 (免費或標準層) **參與者** 角色，適用於雲端連線管理。 請確定已啟用 **適用於 IoT 的 Azure Defender** 功能。
- 針對裝置層級的安全性模組支援，適用於 IoT 的 Defender 代理程式支援不斷增加的裝置和平台清單。 請查看[支援的平台](how-to-deploy-agent.md)清單。

## <a name="supported-service-regions"></a>支援的服務區域

適用於 IoT 的 Defender 會將所有歐洲區域的所有流量路由傳送到西歐地區資料中心。 其會將來自所有剩餘區域的流量路由傳送到美國中部區域資料中心。

如需詳細資訊，請參閱 [IoT 中樞支援區域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。

## <a name="see-also"></a>請參閱

- [識別所需的設備](how-to-identify-required-appliances.md)
- [關於適用於 IoT 的 Azure Defender 網路設定](how-to-set-up-your-network.md)
