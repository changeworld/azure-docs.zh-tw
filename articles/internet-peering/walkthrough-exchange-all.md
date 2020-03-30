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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775169"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 對等互連逐步解說

本節介紹設置和管理 Exchange 對等互連所需的步驟。

## <a name="create-an-exchange-peering"></a>創建 Exchange 對等互連
> [!div class="mx-imgBorder"]
> ![交換對等工作流和連接狀態](./media/exchange-peering.png)

為了預配 Exchange 對等互連，必須遵循以下步驟：
1. 查看 Microsoft[對等互連策略](https://peering.azurewebsites.net/peering)以瞭解 Exchange 對等互連的要求。
1. 在[對等互連資料庫中](https://www.peeringdb.com/net/694)查找 Microsoft 對等位置和對等工具 ID
1. 使用創建中的說明請求 Exchange 對等位置對等，[並使用 PowerShell 文章修改 Exchange 對等互連](howto-exchange-powershell.md)，瞭解更多詳細資訊。
1. 提交對等請求後，Microsoft 將審核該請求，並在需要時與您聯繫。
1. 批准後，連接狀態將更改為"已批准"
1. 在您結束時配置 BGP 會話並通知 Microsoft
1. 我們將使用 DENY 所有策略預配 BGP 會話，並驗證端到端。
1. 如果成功，您將收到一條通知，指出對等互連連接狀態為"活動"。
1. 然後，將允許流量通過新的對等互連。

請注意，連接狀態不應與標準[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)會話狀態混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>將舊版 Exchange 對等互連轉換為 Azure 資源
要將舊版 Exchange 對等互連轉換為 Azure 資源，必須遵循以下步驟：
1. 按照[將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)的說明進行操作
1. 提交轉化請求後，Microsoft 將審核該請求，並在需要時與您聯繫。
1. 獲得批准後，您將看到連接狀態的 Exchange 對等互連為"活動"。

## <a name="deprovision-exchange-peering"></a>取消預配交換對等互連
聯繫[Microsoft 對等互連](mailto:peering@microsoft.com)以取消預配 Exchange 對等互連。

當為取消預配設置 Exchange 對等互連時，您將看到連接狀態為 **"掛起刪除"**

> [!NOTE]
> 如果運行 PowerShell Cmdlet 以在連接狀態為"預配啟動"或"預配完成"時刪除 Exchange 對等對等互連，則操作將失敗。

## <a name="next-steps"></a>後續步驟

* 瞭解[與 Microsoft 建立對等互連的先決條件](prerequisites.md)。
