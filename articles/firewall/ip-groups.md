---
title: Azure 防火牆中的 IP 組
description: IP 組允許您對 Azure 防火牆規則的 IP 位址進行分組和管理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444481"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Azure 防火牆中的 IP 組（預覽）

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IP 組允許您通過以下方式對 Azure 防火牆規則的 IP 位址進行分組和管理：

- 作為 DNAT 規則中的源位址
- 作為網路規則中的源位址或目標位址
- 作為應用程式規則中的源位址


IP 組可以具有單個 IP 位址、多個 IP 位址或一個或多個 IP 位址範圍。

IP 組可以在 Azure 防火牆 DNAT、網路和應用程式規則中重複使用，用於跨 Azure 區域和訂閱的多個防火牆。 組名稱必須是唯一的。 您可以在 Azure 門戶、Azure CLI 或 REST API 中配置 IP 組。 提供了一個示例範本，以説明您入門。

## <a name="sample-format"></a>樣本格式

以下 IPv4 位址格式示例在 IP 組中有效：

- 單位址： 10.0.0.0
- CIDR 標記法： 10.1.0.0/32
- 位址範圍： 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>創建 IP 組

可以使用 Azure 門戶、Azure CLI 或 REST API 創建 IP 組。 有關詳細資訊，請參閱創建[IP 組（預覽）。](create-ip-group.md)

## <a name="browse-ip-groups"></a>流覽 IP 組
1. 在 Azure 門戶搜索欄中，鍵入**IP 組**並選擇它。 您可以看到 IP 組的清單，也可以選擇 **"添加"** 以創建新的 IP 組。
2. 選擇 IP 組以打開概覽頁面。 您可以編輯、添加或刪除 IP 位址或 IP 組。

   ![IP 組概述](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>管理 IP 組

您可以看到 IP 組中的所有 IP 位址及其關聯的規則或資源。 要刪除 IP 組，必須首先將 IP 組與使用它的資源分離。

1. 要查看或編輯 IP 位址，請在左側窗格的 **"設置"** 下選擇**IP 位址**。
2. 要添加單個或多個 IP 位址，請選擇 **"添加 IP 位址**"。 這將打開"**拖動"或"流覽"** 頁面進行上傳，或者您可以手動輸入位址。
3.  選擇編輯或刪除 IP 位址右側的省略號 （**...** 要編輯或刪除多個 IP 位址，請選擇框，然後選擇頂部的 **"編輯**"或 **"刪除**"。
4. 最後，可以匯出 CSV 檔案格式的檔。

> [!NOTE]
> 如果在 IP 組中仍在使用規則時刪除 IP 組中的所有 IP 位址，則跳過該規則。


## <a name="use-an-ip-group"></a>使用 IP 組

現在，在創建 Azure 防火牆 DNAT、應用程式或網路規則時，可以選擇**IP 組**作為 IP 位址的**源類型**或**目標型別**。

> [!NOTE]
> 防火牆策略中不支援 IP 組。 它目前僅受傳統防火牆規則的支援。

![防火牆中的 IP 組](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>區域可用性

IP 組目前在以下區域可用：

- 美國西部
- 美國西部 2
- 美國東部
- 美國東部 2
- 美國中部
- 美國中北部
- 美國中西部
- 美國中南部
- 加拿大中部
- 北歐
- 西歐
- 法國中部
- 英國南部
- 澳大利亞東部
- 澳大利亞中部
- 澳大利亞東南部

## <a name="related-azure-powershell-cmdlets"></a>相關 Azure 電源外殼 Cmdlet

以下 Azure PowerShell Cmdlet 可用於創建和管理 IP 組：

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [刪除-AzIP 組](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [新-Az防火牆網路規則](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [新-Aa防火牆](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。