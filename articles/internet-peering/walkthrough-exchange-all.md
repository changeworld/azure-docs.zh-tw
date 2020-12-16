---
title: Exchange 對等互連逐步解說
titleSuffix: Azure
description: Exchange 對等互連逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586817"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 對等互連逐步解說

本節說明設定和管理交換對等互連所需遵循的步驟。

## <a name="create-an-exchange-peering"></a>建立 Exchange 對等互連
> [!div class="mx-imgBorder"]
> ![Exchange 對等互連工作流程和連接狀態](./media/exchange-peering.png)

您必須遵循下列步驟，才能布建交換對等互連：
1. 請參閱 Microsoft 對 [等互連原則](https://peering.azurewebsites.net/peering) 以瞭解 Exchange 對等互連的需求。
1. 在[PeeringDB](https://www.peeringdb.com/net/694)中尋找 Microsoft 對等互連位置和對等互連設備識別碼
1. 使用 [使用 PowerShell 建立和修改 exchange 對等互連](howto-exchange-powershell.md) 中的指示，針對對等互連位置要求交換對等互連，以取得更多詳細資料。
1. 提交對等互連要求之後，Microsoft 將會檢查要求，並在需要時與您聯繫。
1. 核准之後，連接狀態會變更為 [已核准]
1. 在您的一端設定 BGP 會話，並通知 Microsoft
1. 我們將會使用「拒絕所有原則」布建 BGP 會話，並驗證端對端。
1. 如果成功，您會收到通知，表示對等互連線上狀態為作用中。
1. 然後會透過新的對等互連來允許流量。

請注意，連接狀態不會與標準 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 會話狀態混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換為 Azure 資源
必須遵循下列步驟，才能將舊版 Exchange 對等互連轉換為 Azure 資源：
1. 遵循[將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)的指示
1. 提交轉換要求之後，Microsoft 將會檢查要求，並在需要時與您聯繫。
1. 核准之後，您將會看到您的 Exchange 對等互連的線上狀態為 [作用中]。

## <a name="deprovision-exchange-peering"></a>解除布建交換對等互連
請聯絡 [Microsoft 對等互連](mailto:peering@microsoft.com) 以取消布建 Exchange 對等互連。

針對取消布建設定 Exchange 對等互連時，您會看到線上狀態為 **PendingRemove**

> [!NOTE]
> 如果您執行 PowerShell Cmdlet 來刪除線上狀態為 ProvisioningStarted 或 ProvisioningCompleted 時的 Exchange 對等互連，則作業將會失敗。

## <a name="next-steps"></a>後續步驟

* 瞭解 [設定與 Microsoft 的對等互連的必要條件](prerequisites.md)。
