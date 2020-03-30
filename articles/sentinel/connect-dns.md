---
title: 在 Azure 哨兵中連接 DNS 資料*微軟文檔
description: 瞭解如何在 Azure Sentinel 中連接 DNS 資料。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588309"
---
# <a name="connect-your-domain-name-server"></a>連接功能變數名稱伺服器

> [!IMPORTANT]
> Azure Sentinel 中的 DNS 資料連線器當前處於公共預覽狀態。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以將在 Windows 上運行的任何功能變數名稱伺服器 （DNS） 連接到 Azure 哨兵。 這是通過在 DNS 電腦上安裝代理來完成的。 使用 DNS 日誌，您可以通過從 DNS 伺服器收集、分析和關聯分析和稽核記錄和其他相關資料，獲得組織 DNS 基礎結構的安全、性能和操作相關見解。

啟用 DNS 日誌連接時，您可以：
- 指出嘗試解析惡意網域名稱的用戶端
- 指出過時的資源記錄
- 指出經常查詢的網域名稱和 Talkative DNS 用戶端
- 檢視 DNS 伺服器上的要求負載
- 檢視動態 DNS 註冊失敗

## <a name="connected-sources"></a>連接的來源

下表描述此方案支援的連線來源：

| **連接源** | **支援** | **描述** |
| --- | --- | --- |
| [Windows 代理程式](../azure-monitor/platform/agent-windows.md) | 是 | 此解決方案會收集來自 Windows 代理程式的 DNS 資訊。 |
| [Linux 代理程式](../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 此解決方案不會收集來自直接 Linux 代理程式的 DNS 資訊。 |
| [System Center Operations Manager 管理群組](../azure-monitor/platform/om-agents.md) | 是 | 此解決方案會收集來自連線 Operations Manager 管理群組的代理程式之中的 DNS 資訊。 Operations Manager 代理程式不需要直接連線到 Azure 監視器。 資料會從管理群組轉送至 Log Analytics 工作區。 |
| [Azure 存儲帳戶](../azure-monitor/platform/collect-azure-metrics-logs.md) | 否 | 此解決方案沒有使用 Azure 儲存體。 |

### <a name="data-collection-details"></a>資料收集詳細資料

此解決方案會從已安裝 Log Analytics 代理程式的 DNS 伺服器收集 DNS 清查和 DNS 事件相關資料。 清查相關資料 (例如 DNS 伺服器數目、區域和資源記錄) 的收集方式是執行 DNS PowerShell Cmdlet。 此資料每兩天會更新一次。 事件相關資料是以接近即時的方式，從 Windows Server 2012 R2 增強的 DNS 記錄與診斷功能所提供的[分析和稽核記錄](https://technet.microsoft.com/library/dn800669.aspx#enhanc)進行收集。


## <a name="connect-your-dns-appliance"></a>連接 DNS 設備

1. 在 Azure Sentinel 門戶中，選擇 **"資料"連接子**並選擇**DNS（預覽）** 磁貼。
1. 如果您的 DNS 電腦位於 Azure 中：
    1. 按一下**Azure Windows 虛擬機器上的安裝代理**。
    1. 在 **"虛擬機器**"清單中，選擇要資料流到 Azure Sentinel 的 DNS 電腦。 請確保這是 Windows VM。
    1. 在為該 VM 打開的視窗中，按一下"**連接**"。  
    1. 在 DNS**連接器**視窗中按一下 **"啟用**"。 

2. 如果您的 DNS 電腦不是 Azure VM：
    1. 按一下**非 Azure 電腦上安裝代理**。
    1. 在 **"直接代理"** 視窗中，選擇 **"下載 Windows 代理（64 位）"** 或 **"下載 Windows 代理"（32 位）。**
    1. 在 DNS 電腦上安裝代理。 複製**工作區 ID、****主鍵**和**輔助鍵**，並在安裝過程中提示時使用它們。

3. 要在日誌分析中使用 DNS 日誌中的相關架構，請搜索**DnsEvents**。

## <a name="validate"></a>Validate 

在日誌分析中，搜索架構**DnsEvents**並確保存在事件。

## <a name="troubleshooting"></a>疑難排解

如果"查找查詢"未顯示在 Azure Sentinel 中，請按照以下步驟操作，以便正確顯示查詢：
1. 打開伺服器上的[DNS 分析日誌](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))。
2. 確保 DNS 事件顯示在日誌分析集合清單中。
3. 打開[Azure DNS 分析](../azure-monitor/insights/dns-analytics.md)。
4. 在 Azure DNS 分析中，在**配置**下 ，更改任何設置，將其保存，然後在需要時將其更改回，然後再次保存它。
5. 檢查 Azure DNS 分析以確保當前顯示查詢。

## <a name="next-steps"></a>後續步驟

在本文檔中，您學習了如何將 DNS 本地設備連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
