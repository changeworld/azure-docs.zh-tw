---
title: 直接對等互連逐步解說
titleSuffix: Azure
description: 直接對等互連逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592376"
---
# <a name="direct-peering-walkthrough"></a>直接對等互連逐步解說

本節說明設定及管理直接對等互連所需遵循的步驟。

## <a name="create-a-direct-peering"></a>建立直接對等互連
> [!div class="mx-imgBorder"]
> ![直接對等互連工作流程和連接狀態](./media/direct-peering.png)

您必須遵循下列步驟，才能布建直接對等互連：
1. 請參閱 Microsoft 對 [等互連原則](https://peering.azurewebsites.net/peering) ，以瞭解直接對等互連的需求。
1. 遵循 [建立或修改直接對等互連](howto-direct-powershell.md) 中的指示來提交對等互連要求。
1. 提交對等互連要求之後，Microsoft 會使用您註冊的電子郵件地址來連線，以提供 LOA (信件授權) 或其他資訊。
1. 當對等互連要求經過核准後，線上狀態就會變更為 ProvisioningStarted。
1. 您必須：
    1. 根據 LOA 完成佈線
    1.  (選擇性地) 使用 169.254.0.0/16 執行連結測試
    1. 設定 BGP 會話，然後通知我們。
1. Microsoft 會以拒絕所有原則來布建 BGP 會話，並驗證端對端。
1. 如果成功，您會收到通知，表示對等互連線上狀態為作用中。
1. 然後會透過新的對等互連來允許流量。

請注意，連接狀態不會與標準 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 會話狀態混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>將舊版直接對等互連轉換為 Azure 資源
您必須遵循下列步驟，才能將舊版直接對等互連轉換為 Azure 資源：
1. 遵循[將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)的指示
1. 提交轉換要求之後，Microsoft 將會檢查要求，並在需要時與您聯繫。
1. 經過核准後，您就會看到您的直接對等互連，且連接狀態為 [作用中]。

## <a name="deprovision-direct-peering"></a>取消布建直接對等互連
聯絡 [Microsoft 對等互連](mailto:peering@microsoft.com) 小組以取消布建直接對等互連。

針對取消布建設定直接對等互連時，您會看到線上狀態為 **PendingRemove**

> [!NOTE]
> 如果您執行 PowerShell Cmdlet，以在 ConnectionState 為 ProvisioningStarted 或 ProvisioningCompleted 時刪除直接對等互連，則作業將會失敗。

## <a name="next-steps"></a>後續步驟

* 瞭解 [設定與 Microsoft 的對等互連的必要條件](prerequisites.md)。
