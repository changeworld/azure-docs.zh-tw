---
title: 直接對等互連逐步解說
titleSuffix: Azure
description: 直接對等互連逐步解說
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775507"
---
# <a name="direct-peering-walkthrough"></a>直接對等互連逐步解說

本節介紹設置和管理直接對等互連所需的步驟。

## <a name="create-a-direct-peering"></a>創建直接對等互連
> [!div class="mx-imgBorder"]
> ![直接對等工作流和連接狀態](./media/direct-peering.png)

必須遵循以下步驟才能預配直接對等互連：
1. 查看 Microsoft[對等互連策略](https://peering.azurewebsites.net/peering)以瞭解直接對等互連的要求。
1. 按照["創建"或修改 Direct 對等互連](howto-direct-powershell.md)中的說明來提交對等請求。
1. 提交對等請求後，Microsoft 將使用您註冊的電子郵件地址聯繫，以提供 LOA（授權書）或其他資訊。
1. 審核對等請求後，連接狀態將更改為預配啟動。
1. 您必須：
    1. 根據 LOA 完成佈線
    1. （可選）使用 169.254.0/16 執行鏈路測試
    1. 配置 BGP 會話，然後通知我們。
1. Microsoft 提供 BGP 會話與 DENY 所有策略並驗證端到端。
1. 如果成功，您將收到一條通知，指出對等互連連接狀態為"活動"。
1. 然後，將允許流量通過新的對等互連。

請注意，連接狀態不應與標準[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)會話狀態混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>將舊版直接對等互連轉換為 Azure 資源
要將舊版直接對等互連轉換為 Azure 資源，必須遵循以下步驟：
1. 按照[將舊版直接對等互連](howto-legacy-direct-powershell.md)轉換為 Azure 資源的說明進行操作
1. 提交轉化請求後，Microsoft 將審核該請求，並在需要時與您聯繫。
1. 獲得批准後，您將看到連接狀態為"直接對等互連"為"活動"。

## <a name="deprovision-direct-peering"></a>取消預配 直接對等互連
請與[Microsoft 對等互連](mailto:peering@microsoft.com)團隊聯繫以取消預配直接對等互連。

當為取消預配設置直接對等互連時，您將看到連接狀態為 **"掛起刪除"**

> [!NOTE]
> 如果運行 PowerShell Cmdlet 以在連接狀態預配啟動或預配完成時刪除直接對等互連，則操作將失敗。

## <a name="next-steps"></a>後續步驟

* 瞭解[與 Microsoft 建立對等互連的先決條件](prerequisites.md)。
