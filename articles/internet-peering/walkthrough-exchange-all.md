---
title: Exchange 對等互連逐步解說
titleSuffix: Azure
description: Exchange 對等互連逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75775169"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 對等互連逐步解說

本節說明設定和管理 Exchange 對等互連所需遵循的步驟。

## <a name="create-an-exchange-peering"></a>建立 Exchange 對等互連
> [!div class="mx-imgBorder"]
> ![Exchange 對等工作流程和連接狀態](./media/exchange-peering.png)

必須遵循下列步驟，才能布建 Exchange 對等互連：
1. 請參閱 Microsoft 對[等互連原則](https://peering.azurewebsites.net/peering)，以瞭解 Exchange 對等互連的需求。
1. 在[PeeringDB](https://www.peeringdb.com/net/694)中尋找 Microsoft 對等互連位置和對等互連設施識別碼
1. 如需詳細資訊，請參閱使用[PowerShell 建立和修改 exchange 對等](howto-exchange-powershell.md)互連一文中的指示，要求對等互連位置的交換對等互連。
1. 提交對等互連要求之後，Microsoft 將會檢查要求，並視需要與您聯絡。
1. 一旦核准，線上狀態會變更為 [已核准]
1. 在您的端點設定 BGP 會話並通知 Microsoft
1. 我們將會布建具有「拒絕所有原則」和「端對端驗證」的 BGP 會話。
1. 如果成功，您會收到通知，指出對等互連線上狀態為作用中。
1. 接著會允許流量通過新的對等互連。

請注意，連接狀態不會與標準[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)會話狀態混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換為 Azure 資源
必須遵循下列步驟，才能將舊版 Exchange 對等互連轉換成 Azure 資源：
1. 遵循[將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-powershell.md)中的指示
1. 提交轉換要求之後，Microsoft 將會檢查要求，並視需要與您聯絡。
1. 一經核准，您就會看到 Exchange 對等互連的線上狀態為 [作用中]。

## <a name="deprovision-exchange-peering"></a>取消布建 Exchange 對等互連
請洽詢[Microsoft 對等互連](mailto:peering@microsoft.com)以取消布建 Exchange 對等互連。

當 Exchange 對等互連設定為解除布建時，您會看到線上狀態為**PendingRemove**

> [!NOTE]
> 如果您在線上狀態為 [ProvisioningStarted] 或 [ProvisioningCompleted] 時執行 PowerShell Cmdlet 來刪除 Exchange 對等互連，此作業將會失敗。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[設定與 Microsoft 的對等互連的必要條件](prerequisites.md)。
