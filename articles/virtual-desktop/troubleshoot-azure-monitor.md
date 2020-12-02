---
title: 針對監視 Windows 虛擬桌面預覽版進行疑難排解-Azure
description: 如何針對 Windows 虛擬桌面 Azure 監視器的問題進行疑難排解。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466338"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>針對 Windows 虛擬桌面的 Azure 監視器進行疑難排解 (預覽) 

>[!IMPORTANT]
>適用于 Windows 虛擬桌面的 Azure 監視器目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文提供 Windows 虛擬桌面 (preview) 中 Azure 監視器常見問題的已知問題和解決方案。

## <a name="the-configuration-workbook-isnt-working-properly"></a>設定活頁簿無法正常運作

如果 Azure 監視器設定活頁簿無法運作，您可以使用這些資源手動設定其部分：

- 若要手動啟用診斷或存取 Log Analytics 工作區，請參閱 [將 Windows 虛擬桌面診斷傳送至 Log analytics](diagnostics-log-analytics.md)。
- 若要以手動方式在主機上安裝 Log Analytics 擴充功能，請參閱 [適用于 Windows 的 Log analytics 虛擬機器擴充](../virtual-machines/extensions/oms-windows.md)功能。
- 若要設定新的 Log Analytics 工作區，請參閱 [Azure 入口網站中的建立 Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。
- 若要新增或移除效能計數器，請參閱設定 [效能計數器](../azure-monitor/platform/data-sources-performance-counters.md)。
- 若要設定 Log Analytics 工作區的事件，請參閱 [使用 Log analytics 代理程式收集 Windows 事件記錄檔資料來源](../azure-monitor/platform/data-sources-windows-events.md)。

或者，問題可能是因為缺乏資源或沒有必要的許可權所造成。

如果訂用帳戶沒有任何 Windows 虛擬桌面資源，則不會顯示在 *訂* 用帳戶參數中。

如果您沒有正確訂用帳戶的讀取權限，這些訂用帳戶將不會顯示在 *訂* 用帳戶參數中，您將不會在儀表板中看到其資料。 若要解決此問題，請洽詢您的訂用帳戶擁有者，並要求讀取存取權。

## <a name="my-data-isnt-displaying-properly"></a>我的資料未正確顯示

如果您的資料未正確顯示，Azure 監視器設定程式期間可能會發生一些問題。 首先，請確定您已填寫設定活頁簿中的所有欄位，如 [使用 Windows 虛擬桌面的 Azure 監視器中所述，以監視您的部署](azure-monitor.md)。 您可以隨時變更新的和現有環境的設定。 如果您遺漏任何計數器或事件，與其相關聯的資料將不會出現在 Azure 入口網站中。

如果您沒有遺漏任何資訊，但您的資料仍未正確顯示，則查詢或資料來源可能會有問題。 

如果您沒有看到任何安裝錯誤，但仍未看到您預期的資料，您可能需要等候15分鐘，然後重新整理摘要。 Azure 監視器有15分鐘的延遲時間來擴展記錄資料。 若要深入瞭解，請參閱 [Azure 監視器中的記錄資料內嵌時間](../azure-monitor/platform/data-ingestion-time.md)。

最後，如果您沒有遺漏任何資訊，但您的資料仍未顯示，則查詢或資料來源可能會有問題。 如果發生這種情況，您可能需要聯繫支援人員來解決問題。

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>我想要自訂 Windows 虛擬桌面的 Azure 監視器

適用于 Windows 虛擬桌面的 Azure 監視器會使用 Azure 監視器的活頁簿。 活頁簿可讓您儲存 Windows 虛擬桌面活頁簿範本的複本，並建立您自己的自訂專案。

當產品群組更新原始範本時，不會更新自訂範本。 這是在活頁簿工具中的設計，您必須儲存更新範本的複本，並重新建立自訂以採用更新。 如需詳細資訊，請參閱疑難排解以活頁 [簿為基礎的深入](../azure-monitor/insights/troubleshoot-workbooks.md) 解析和活頁 [簿總覽](../azure-monitor/platform/workbooks-overview.md)。

## <a name="i-cant-interpret-the-data"></a>我無法解讀資料

深入瞭解 [Windows 虛擬桌面詞彙 Azure 監視器](azure-monitor-glossary.md)的資料詞彙。

## <a name="the-data-i-need-isnt-available"></a>我所需的資料無法使用

找不到可協助診斷問題的資料點嗎？ 傳送意見反應給我們！

- 若要瞭解如何離開意見反應，請參閱 [疑難排解總覽、意見反應，以及 Windows 虛擬桌面的支援](troubleshoot-set-up-overview.md)。
- 您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 或 [UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中留下 windows 虛擬桌面的意見反應。

## <a name="known-issues"></a>已知問題

這些是我們目前所知的問題，並致力於修正：

- 您目前只能選取一個訂用帳戶、資源群組，以及一次監視的主機集區。 基於這個原因，當使用 [使用者報表] 頁面來瞭解使用者的體驗時，您必須確認您擁有使用者已使用的正確主機集區，否則他們的資料將不會填入視覺效果。

- 除非您儲存活頁簿的自訂範本，否則目前無法在 Azure 監視器中儲存我的最愛設定。 這表示在每次開啟 Windows 虛擬桌面的 Azure 監視器時，IT 系統管理員都必須輸入其訂用帳戶名稱、資源組名和主機集區的喜好設定。

- 目前無法將 Windows 虛擬桌面 Azure 監視器中的資料匯出至 Excel。

- 所選訂用帳戶內所有產品的所有嚴重性 1 Azure 監視器警示都會顯示在 [總覽] 頁面中。 這是設計，因為訂用帳戶中其他產品的警示可能會影響 Windows 虛擬桌面。 目前，查詢僅限於嚴重性1警示，但不包括 [總覽] 頁面中的高優先順序嚴重性0警示。

- 某些錯誤訊息不會以方便使用的方式片語，而且不會在檔中描述所有錯誤訊息。

## <a name="next-steps"></a>後續步驟

如果您不確定如何解讀資料，或想要深入瞭解常見的詞彙，請參閱 [Windows 虛擬桌面的 Azure 監視器詞彙](azure-monitor-glossary.md)。 如果您想要瞭解如何設定和使用 Windows 虛擬桌面的 Azure 監視器，請參閱 [使用 Windows 虛擬桌面的 Azure 監視器來監視您的部署](azure-monitor.md)。