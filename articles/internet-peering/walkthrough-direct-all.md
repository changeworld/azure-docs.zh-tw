---
title: 直接對等互連逐步解說
titleSuffix: Azure
description: 直接對等互連逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e27fa26514d27d68aecdf9e28b36e2747dc8ffe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710757"
---
# <a name="direct-peering-walkthrough"></a>直接對等互連逐步解說

本節說明設定和管理直接對等互連所需遵循的步驟。

## <a name="create-a-direct-peering"></a>建立直接對等互連
> [!div class="mx-imgBorder"]
> ![直接對等工作流程和連接狀態](./media/direct-peering.png)

必須遵循下列步驟，才能布建直接對等互連：
1. 請參閱 Microsoft 對[等互連原則](https://peering.azurewebsites.net/peering)，以瞭解直接對等的需求。
1. 依照[建立或修改直接對等互連](howto-direct-powershell.md)中的指示提交對等互連要求。
1. 提交對等互連要求之後，Microsoft 會使用您註冊的電子郵件地址來提供 LOA （授權的信件）或其他資訊。
1. 對等互連要求一經核准，線上狀態就會變更為 ProvisioningStarted。
1. 您必須：
    1. 根據 LOA 完成佈線
    1. （選擇性）使用 [169.254.0.0/16] 執行連結測試
    1. 設定 BGP 會話，然後通知我們。
1. Microsoft 會使用「拒絕所有原則」和「端對端驗證」來布建 BGP 會話。
1. 如果成功，您會收到通知，指出對等互連線上狀態為作用中。
1. 接著會允許流量通過新的對等互連。

請注意，連接狀態不會與標準[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)會話狀態混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>將舊版直接對等互連轉換為 Azure 資源
必須遵循下列步驟，才能將舊版直接對等互連轉換成 Azure 資源：
1. 遵循[將傳統直接對等互連轉換成 Azure 資源](howto-legacy-direct-powershell.md)中的指示
1. 提交轉換要求之後，Microsoft 將會檢查要求，並視需要與您聯絡。
1. 一經核准，您就會看到線上狀態為 [作用中] 的直接對等互連。

## <a name="deprovision-direct-peering"></a>取消布建直接對等
請洽詢[Microsoft 對等互連](mailto:peering@microsoft.com)小組，以取消布建直接對等互連。

將直接對等互連設定為解除布建時，您會看到線上狀態為**PendingRemove**

> [!NOTE]
> 如果您在 ConnectionState 為 ProvisioningStarted 或 ProvisioningCompleted 時執行 PowerShell Cmdlet 來刪除直接對等互連，此作業將會失敗。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[設定與 Microsoft 的對等互連的必要條件](prerequisites.md)。
