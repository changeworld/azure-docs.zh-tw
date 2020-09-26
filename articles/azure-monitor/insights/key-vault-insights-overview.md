---
title: 使用 Key Vault 的 Azure 監視器監視 Key Vault |Microsoft Docs
description: 本文說明 Key Vault 的 Azure 監視器。
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: 4b91a9a73035b3add309e72ce544375520cf279e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278612"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>使用 Key Vault 的 Azure 監視器監視您的金鑰保存庫服務
適用于 Key Vault 的 Azure 監視器可提供您 Key Vault 要求、效能、失敗和延遲的統一觀點，藉以全面監視您的金鑰保存庫。
本文將協助您瞭解如何上架和自訂 Key Vault 的 Azure 監視器體驗。

## <a name="introduction-to-azure-monitor-for-key-vault"></a>Key Vault 的 Azure 監視器簡介

開始處理體驗之前，您應先了解其呈現和視覺化資訊的方式。
-    **整體觀點**會根據要求、失敗的細節，以及作業和延遲的概觀，顯示效能的快照集檢視。
-   **向下切入分析**特定的金鑰保存庫，可執行詳細的分析。
-    **可自訂項目**可讓您變更想要查看的計量、修改或設定對應於限制的閾值，以及儲存您自己的活頁簿。 活頁簿中的圖表可釘選到 Azure 儀表板。

Key Vault 的 Azure 監視器結合了記錄和計量，以提供全域監視解決方案。 所有使用者都可存取以計量為基礎的監視資料，但若要納入以記錄為基礎的視覺效果，使用者可能必須[啟用 Azure Key Vault 的記錄](../../key-vault/general/logging.md)。

## <a name="view-from-azure-monitor"></a>從 Azure 監視器檢視

在 Azure 監視器中，您可以檢視來自訂用帳戶中多個金鑰保存庫的要求、延遲和失敗詳細資料，並識別效能問題和節流案例。

若要查看您在所有訂用帳戶中的金鑰保存庫的使用量和作業，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)

2. 從 Azure 入口網站的左側窗格中選取 [ **監視** ]，然後在 [見解] 區段下選取 [ **金鑰保存庫**]。

![概觀體驗的螢幕擷取畫面，其中包含多個圖表](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>概觀活頁簿

在所選訂用帳戶的 [概觀] 活頁簿上，資料表會針對訂用帳戶內已分組的金鑰保存庫顯示互動式金鑰保存庫計量。 您可以根據您從下列下拉式清單中選取的選項，來篩選結果：

* 訂用帳戶 – 僅列出具有金鑰保存庫的訂用帳戶。

* 金鑰保存庫 – 依預設只會預先選取最多 5 個金鑰保存庫。 如果您在範圍選取器中選取所有或多個金鑰保存庫，則最多會傳回 200 個金鑰保存庫。 例如，如果您所選取的三個訂用帳戶共有 573 個金鑰保存庫，則只會顯示 200 個保存庫。

* 時間範圍 – 依預設會根據對應的選取項目顯示過去 24 小時的資訊。

計數器磚位於下拉式清單底下，會彙總所選訂用帳戶中的金鑰保存庫總數，並反映已選取的數目。 對於報告要求、失敗和延遲等計量的活頁簿資料行，會有以色彩區分條件的熱度圖。 最深的色彩具有最高的值，色彩愈淺則值愈低。

## <a name="failures-workbook"></a>失敗活頁簿

選取頁面頂端的 [失敗]，[失敗] 索引標籤即會開啟。 其中會顯示 API 點擊次數、一段時間的頻率，以及特定回應碼的數量。

![失敗活頁簿的螢幕擷取畫面](./media/key-vaults-insights-overview/failures.png)

活頁簿中的資料行有以色彩區分條件的熱度圖，會以藍色值報告 API 點擊次數計量。 最深的色彩具有最高的值，色彩愈淺則值愈低。

活頁簿會顯示 [成功] (2xx 狀態碼)、[驗證錯誤] (401/403 狀態碼)、[節流] (429 狀態碼) 和 [其他失敗] (4xx 狀態碼)。

若要進一步了解每個狀態碼代表的涵義，建議您閱讀 [Azure Key Vault 的狀態碼和回應碼](../../key-vault/general/authentication-requests-and-responses.md)的相關文件。

## <a name="view-from-a-key-vault-resource"></a>從 Key Vault 資源檢視

若要直接從金鑰保存庫存取 Key Vault 的 Azure 監視器：

1. 在 Azure 入口網站中，選取 [金鑰保存庫]。

2. 從清單中選擇金鑰保存庫。 在 [監視] 區段中，選擇 [見解]。

您也可以從 [Azure 監視器層級] 活頁簿中選取金鑰保存庫的資源名稱，藉以存取這些檢視。

![從金鑰保存庫資源檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/key-vault-resource-view.png)

在金鑰保存庫的 [概觀] 活頁簿上，會顯示數個可協助您快速評估的效能計量：

- 互動式效能圖表，會顯示與金鑰保存庫交易、延遲和可用性相關的基本詳細資料。

- 計量和狀態磚，會醒目提示服務可用性、金鑰保存庫資源的交易總數，以及整體延遲。

選取 [失敗] 或 [作業] 的任何其他索引標籤，會開啟對應的活頁簿。

![失敗檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/resource-failures.png)

「失敗」活頁簿會詳列所有金鑰保存庫要求在所選時間範圍內的結果，並提供 [成功] (2xx)、[驗證錯誤] (401/403)、[節流] (429) 和 [其他失敗] 的分類。

![作業檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/operations.png)

「作業」活頁簿可讓使用者深入檢視所有交易的完整詳細資料，並且可使用頂層磚依「結果狀態」篩選這些資料。

![作業檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/info.png)

使用者也可以根據上方資料表中的特定交易類型來界定檢視的範圍，而以動態方式更新下方資料表，讓使用者可在快顯內容窗格中檢視完整的作業詳細資料。

>[!NOTE]
> 請注意，使用者必須已啟用診斷設定，才能檢視此活頁簿。 若要深入了解如何啟用診斷設定，請參閱更多有關 [Azure Key Vault 記錄](../../key-vault/general/logging.md)的資訊。

## <a name="pin-and-export"></a>釘選和匯出

您可以將任何一個計量區段釘選到 Azure 儀表板，只要選取區段右上角的圖釘圖示即可。

多重訂用帳戶和金鑰保存庫概觀或失敗活頁簿支援藉由選取圖釘圖示左側的下載圖示，以 Excel 格式匯出結果。

![已選取釘選圖示的螢幕擷取畫面](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>自訂 Key Vault 的 Azure 監視器

本節主要說明編輯自訂活頁簿以支援資料分析需求的常見案例：
*  將活頁簿的範圍設為一律選取特定訂用帳戶或金鑰保存庫
* 變更方格中的計量
* 變更要求閾值
* 變更色彩呈現

您可以選取頂端工具列中的 [自訂] 按鈕以啟用編輯模式，並開始進行自訂。

![自訂按鈕的螢幕擷取畫面](./media/key-vaults-insights-overview/customize.png)

自訂項目會儲存至自訂活頁簿，以避免覆寫已發佈活頁簿中的預設設定。 活頁簿會儲存在資源群組內，保存在您私人使用的 [我的報表] 區段中，或是可供每個具有資源群組存取權的人員存取的 [共用報表] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿資源庫加以啟動。

![活頁簿資源庫的螢幕擷取畫面](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>指定訂用帳戶或金鑰保存庫

您可以執行下列步驟，將多重訂用帳戶和金鑰保存庫概觀或失敗活頁簿的範圍設定為每次執行時的特定訂用帳戶或金鑰保存庫：

1. 從入口網站中選取 [ **監視** ]，然後從左側窗格選取 [ **金鑰保存庫** ]。
2. 在 [概觀] 活頁簿上，從命令列中選取 [編輯]。
3. 從 [訂用帳戶] 下拉式清單中選取要作為預設值的一或多個訂用帳戶。 請記住，活頁簿最多支援選取 10 個訂用帳戶。
4. 從 [金鑰保存庫] 下拉式清單中選取要作為預設值的一或多個帳戶。 請記住，活頁簿最多支援選取 200 個儲存體帳戶。
5. 從命令列中選取 [另存新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [完成編輯] 以回到閱讀模式。

## <a name="troubleshooting"></a>疑難排解

如需一般疑難排解指引，請參閱專用的以活頁簿為基礎的深入解析 [疑難排解文章](troubleshoot-workbooks.md)。

本節將協助您進行診斷，並針對使用 Key Vault Azure 監視器時可能遇到的一些常見問題進行疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-issues-or-failures"></a>解決效能問題或失敗

若要協助您針對 Key Vault 使用 Azure 監視器識別的任何金鑰保存庫相關問題進行疑難排解，請參閱 [Azure Key Vault 檔](../../key-vault/index.yml)。

### <a name="why-can-i-only-see-200-key-vaults"></a>為什麼我只能看到200金鑰保存庫

可選取和檢視的金鑰保存庫數目限定為 200 個。 無論選取多少個訂用帳戶，選取的金鑰保存庫數目均限定為 200 個。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>為什麼我在訂用帳戶選擇器中看不到我的所有訂閱

我們只會顯示從選取的訂用帳戶篩選器中選擇、且包含金鑰保存庫的訂用帳戶；您可以在 Azure 入口網站標頭的 [目錄 + 訂用帳戶] 中選取該篩選器。

![訂用帳戶篩選器的螢幕擷取畫面](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>我想要進行變更，或將其他視覺效果新增至 Key Vault 見解，該怎麼做

若要進行變更，請選取 [編輯模式] 以修改活頁簿，然後，您可以將工作儲存為新的活頁簿，並繫結至指定的訂用帳戶和資源群組。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>釘選活頁簿的任何部分之後的時間細微性為何

我們採用「自動」時間精細度，這具體上取決於選取的時間範圍。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>當活頁簿的任何部分釘選時的時間範圍

時間範圍將取決於儀表板設定。

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>如果我想要查看其他資料或建立自己的視覺效果，該怎麼做？ 如何對 Key Vault 的見解進行變更

您可以使用編輯模式來編輯現有的活頁簿，然後將您的工作儲存為新的活頁簿，其中將包含您所有的新變更。

## <a name="next-steps"></a>後續步驟

檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解設計活頁簿以提供支援的案例、如何撰寫新報表和自訂現有報表，以及其他資訊。
