---
title: 連接 Azure Sentinel 中的 DNS 資料 |Microsoft Docs
description: 瞭解如何在 DNS 機器上安裝代理程式，以將在 Windows 上執行的任何功能變數名稱伺服器（DNS）連接到 Azure Sentinel。
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
ms.openlocfilehash: a88696ba69fdf53f5c7e15d174b126d69f4230ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555433"
---
# <a name="connect-your-domain-name-server"></a>連接您的功能變數名稱伺服器

> [!IMPORTANT]
> Azure Sentinel 中的 DNS 資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以將在 Windows 上執行的任何功能變數名稱伺服器（DNS）連接到 Azure Sentinel。 這是藉由在 DNS 機器上安裝代理程式來完成。 使用 DNS 記錄，您可以從 DNS 伺服器收集、分析及相互關聯分析和審核記錄以及其他相關資料，以取得組織 DNS 基礎結構的安全性、效能和操作相關的深入解析。

當您啟用 DNS 記錄連接時，您可以：
- 指出嘗試解析惡意網域名稱的用戶端
- 指出過時的資源記錄
- 指出經常查詢的網域名稱和 Talkative DNS 用戶端
- 檢視 DNS 伺服器上的要求負載
- 檢視動態 DNS 註冊失敗

## <a name="connected-sources"></a>連接的來源

下表描述此方案支援的連線來源：

| **連線的來源** | **支援** | **描述** |
| --- | --- | --- |
| [Windows 代理程式](../azure-monitor/platform/agent-windows.md) | Yes | 此解決方案會收集來自 Windows 代理程式的 DNS 資訊。 |
| [Linux 代理程式](../azure-monitor/learn/quick-collect-linux-computer.md) | No | 此解決方案不會收集來自直接 Linux 代理程式的 DNS 資訊。 |
| [System Center Operations Manager 管理群組](../azure-monitor/platform/om-agents.md) | Yes | 此解決方案會收集來自連線 Operations Manager 管理群組的代理程式之中的 DNS 資訊。 Operations Manager 代理程式不需要直接連線到 Azure 監視器。 資料會從管理群組轉送至 Log Analytics 工作區。 |
| [Azure 儲存體帳戶](../azure-monitor/platform/collect-azure-metrics-logs.md) | No | 此解決方案沒有使用 Azure 儲存體。 |

### <a name="data-collection-details"></a>資料收集詳細資料

此解決方案會從已安裝 Log Analytics 代理程式的 DNS 伺服器收集 DNS 清查和 DNS 事件相關資料。 清查相關資料 (例如 DNS 伺服器數目、區域和資源記錄) 的收集方式是執行 DNS PowerShell Cmdlet。 此資料每兩天會更新一次。 事件相關資料是以接近即時的方式，從 Windows Server 2012 R2 增強的 DNS 記錄與診斷功能所提供的[分析和稽核記錄](https://technet.microsoft.com/library/dn800669.aspx#enhanc)進行收集。


## <a name="connect-your-dns-appliance"></a>連接您的 DNS 應用裝置

1. 在 Azure Sentinel 入口網站中，選取 [**資料連線器**]，然後選擇 [ **DNS （預覽）** ] 磚。
1. 如果您的 DNS 機器位於 Azure：
    1. 按一下 [**在 Azure Windows 虛擬機器上安裝代理程式**]。
    1. 在 [**虛擬機器**] 清單中，選取您想要串流至 AZURE SENTINEL 的 DNS 機器。 請確定這是 Windows VM。
    1. 在針對該 VM 開啟的視窗中，按一下 **[** 連線]。  
    1. 按一下 [ **DNS 連接器**] 視窗中的 [**啟用**]。 

2. 如果您的 DNS 機器不是 Azure VM：
    1. 按一下 [**在非 Azure 電腦上安裝代理程式**]。
    1. 在 [**直接代理程式**] 視窗中，選取 [**下載 windows 代理程式（64位）** ] 或 [**下載 windows 代理程式（32位）**]。
    1. 在您的 DNS 電腦上安裝代理程式。 複製 [**工作區識別碼**]、[**主要金鑰**] 和 [**次要金鑰**]，並在安裝期間出現提示時使用。

3. 若要在 Log Analytics 中使用 DNS 記錄的相關架構，請搜尋**DnsEvents**。

## <a name="validate"></a>Validate 

在 Log Analytics 中，搜尋架構**DnsEvents** ，並確認有事件。

## <a name="troubleshooting"></a>疑難排解

如果查閱查詢未顯示在 Azure Sentinel 中，請遵循下列步驟，以便正確地顯示查詢：
1. 開啟伺服器上的[DNS 分析記錄](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))檔。
2. 請確定 DNSEvents 出現在 Log Analytics 集合清單中。
3. 開啟[Azure DNS 分析](../azure-monitor/insights/dns-analytics.md)。
4. 在 Azure DNS 分析] 的 [設定] 下，變更任何**設定、儲存**，然後視需要將它變更為 [重新儲存]。
5. 請檢查 Azure DNS 分析，以確保現在會顯示查詢。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 DNS 內部部署應用裝置連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
