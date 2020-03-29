---
title: 監視您的 Azure Data Box Edge 裝置 | Microsoft Docs
description: 描述如何使用 Azure 入口網站和本機 Web UI 來監視您的 Azure Data Box Edge。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756730"
---
# <a name="monitor-your-azure-data-box-edge"></a>監視您的 Azure Data Box Edge

本文說明如何監視您的 Azure Data Box Edge。 若要監視您的裝置，您可以使用 Azure 入口網站或本機 Web UI。 使用 Azure 入口網站來檢視裝置事件、設定及管理警示和檢視計量。 在您的實體裝置上使用本機 Web UI，來檢視各種裝置元件的硬體狀態。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 檢視裝置事件和對應的警示
> * 檢視裝置元件的硬體狀態
> * 檢視您裝置的容量和交易計量
> * 設定和管理警示

## <a name="view-device-events"></a>檢視裝置事件

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>檢視硬體狀態

在本機 Web UI 中採取下列步驟來檢視您裝置元件的硬體狀態。

1. 連線至您裝置的本機 Web UI 設定。
2. 移至 [維護] > [硬體狀態]****。 您可以檢視各種裝置元件的健康情況。

    ![檢視硬體狀態](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>檢視計量

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>管理警示

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>後續步驟 

了解如何[管理頻寬](data-box-edge-manage-bandwidth-schedules.md)。