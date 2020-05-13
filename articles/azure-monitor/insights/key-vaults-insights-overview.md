---
title: 使用適用于 Key Vault 的 Azure 監視器監視 Key Vault （預覽） |Microsoft Docs
description: 本文說明金鑰保存庫的 Azure 監視器。
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 2387f14a537c15c891bff968573bf4d698c01770
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211299"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>使用適用于 Key Vault 的 Azure 監視器監視您的金鑰保存庫服務（預覽）
Azure 監視器 for Key Vault （預覽）藉由提供您 Key Vault 要求、效能、失敗和延遲的統一觀點，全面監視您的金鑰保存庫。
本文將協助您瞭解如何上架和自訂 Key Vault （預覽） Azure 監視器的體驗。

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Key Vault 的 Azure 監視器簡介（預覽）

在進入體驗之前，您應該先瞭解其呈現方式，並將資訊視覺化。
-    根據要求、失敗細分和作業和延遲的總覽，**以大規模的觀點**顯示效能的快照集。
-   向下切入特定金鑰保存庫的**分析**，以執行詳細的分析。
-    可**自訂**，您可以在其中變更您想要查看的計量、修改或設定符合限制的臨界值，以及儲存您自己的活頁簿。 活頁簿中的圖表可以釘選到 Azure 儀表板。

Key Vault 的 Azure 監視器結合了記錄和計量，以提供全域監視解決方案。 所有使用者都可以存取以計量為基礎的監視資料，不過包含以記錄為基礎的視覺效果可能需要使用者[啟用其 Azure Key Vault 的記錄](https://docs.microsoft.com/azure/key-vault/key-vault-logging)。

## <a name="configuring-your-key-vaults-for-monitoring"></a>設定用於監視的金鑰保存庫

> [!NOTE]
> 啟用記錄是付費服務，可提供額外的監視功能。

1. [作業 & 延遲] 索引標籤可協助您決定要啟用多少和哪些金鑰保存庫。 若要開始收集，請選取 [**啟用**] 按鈕，這會將您帶到個別的活頁簿，列出需要啟用診斷記錄的金鑰保存庫。

    ![顯示藍色 [啟用] 按鈕的 [作業和延遲] 索引標籤螢幕擷取畫面](./media/key-vaults-insights-overview/enable-logging.png)

2. 若要啟用診斷記錄，請按一下 [動作] 資料行下方的 [**啟用**] 連結，然後建立新的診斷設定，以將記錄傳送至 Log Analytics 工作區。 建議將所有記錄傳送至相同的工作區。

3. 儲存診斷設定之後，您就能夠在 Key Vault Insights 底下，看到所有以記錄為基礎的圖表和視覺效果。 請注意，可能需要幾分鐘到數小時的時間才能開始填入記錄。

4. 如需有關如何為您的 Key Vault 服務啟用診斷記錄的其他協助，請閱讀[完整指南](https://docs.microsoft.com/azure/key-vault/key-vault-logging)。

## <a name="view-from-azure-monitor"></a>從 Azure 監視器查看

從 Azure 監視器，您可以從訂用帳戶中的多個金鑰保存庫來查看要求、延遲和失敗詳細資料，並協助識別效能問題和節流案例。

若要查看您所有訂用帳戶的儲存體帳戶使用率和作業，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)

2. 從 Azure 入口網站的左側窗格中選取 [**監視**]，然後在 [深入解析] 區段下選取 [**金鑰保存庫（預覽）**]。

![具有多個圖形之總覽體驗的螢幕擷取畫面](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>總覽活頁簿

在所選訂用帳戶的總覽活頁簿上，資料表會顯示訂用帳戶內分組之金鑰保存庫的互動式金鑰保存庫計量。 您可以根據您從下列下拉式清單中選取的選項來篩選結果：

* 訂用帳戶–只會列出具有金鑰保存庫的訂用帳戶。

* 金鑰保存庫–根據預設，只會預先選取最多5個金鑰保存庫。 如果您在範圍選取器中選取所有或多個金鑰保存庫，則會傳回最多200個金鑰保存庫。 例如，如果您所選取的三個訂用帳戶共有573個金鑰保存庫，則只會顯示200保存庫。

* 時間範圍–根據預設，顯示過去24小時的資訊，以所做的對應選項為基礎。

[計數器] 磚在下拉式清單底下，會匯總所選訂用帳戶中的金鑰保存庫總數，並反映選取的數目。 針對報表要求、失敗和延遲計量的活頁簿資料行，有條件式色彩編碼的熱度圖。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。

## <a name="failures-workbook"></a>失敗活頁簿

選取頁面頂端的 [**失敗**]，[失敗] 索引標籤隨即開啟。 它會顯示 API 點擊、一段時間的頻率，以及特定回應碼的數量。

![失敗活頁簿的螢幕擷取畫面](./media/key-vaults-insights-overview/failures.png)

活頁簿中的資料行有條件式色彩編碼或熱度圖，會以藍色值來報告 API 叫用計量。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。

活頁簿會顯示成功（2xx 狀態碼）、驗證錯誤（401/403 狀態碼）、節流（429狀態碼）和其他失敗（4xx 狀態碼）。

若要進一步瞭解每個狀態碼所代表的內容，建議您閱讀有關[Azure Key Vault 狀態和回應碼](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses)的檔。

## <a name="operations--latency-workbook"></a>作業 & 延遲活頁簿

選取頁面頂端的 [**作業 & 延遲**]，[**作業 & 延遲**] 索引標籤隨即開啟。 此索引標籤可讓您將金鑰保存庫上線以進行監視。 如需詳細步驟，請參閱設定[您的金鑰保存庫以進行監視](#configuring-your-key-vaults-for-monitoring)一節。

您可以查看有多少個金鑰保存庫已啟用記錄功能。 如果至少已正確設定一個保存庫，則您將能夠看到顯示每個金鑰保存庫之作業和狀態碼的資料表。 您可以按一下資料列的 [詳細資料] 區段，以取得個別作業的其他資訊。

![作業和延遲圖表的螢幕擷取畫面](./media/key-vaults-insights-overview/logs.png)

如果您在此區段中看不到任何資料，請參考上一節以瞭解如何啟用 Azure Key Vault 的記錄，或查看下面的疑難排解一節。

## <a name="view-from-a-key-vault-resource"></a>從 Key Vault 資源觀看

若要直接從金鑰保存庫存取 Key Vault 的 Azure 監視器：

1. 在 [Azure 入口網站中，選取 [金鑰保存庫]。

2. 從清單中選擇金鑰保存庫。 在 [監視] 區段中，選擇 [Insights （預覽）]。

從 [Azure 監視器層級] 活頁簿中選取金鑰保存庫的資源名稱，也可以存取這些視圖。

![從金鑰保存庫資源觀看視圖的螢幕擷取畫面](./media/key-vaults-insights-overview/key-vault-resource-view.png)

在金鑰保存庫的**總覽**活頁簿中，它會顯示數個可協助您快速評估的效能計量：

- 互動式效能圖表，顯示與金鑰保存庫交易、延遲和可用性相關的最基本詳細資料。

- 計量和狀態磚會反白顯示服務可用性、金鑰保存庫資源的總交易數，以及整體延遲。

針對**失敗**或**作業**選取任何其他索引標籤，就會開啟個別的活頁簿。

![失敗視圖的螢幕擷取畫面](./media/key-vaults-insights-overview/resource-failures.png)

[失敗] 活頁簿會在所選的時間範圍內，細分所有金鑰保存庫要求的結果，並提供成功（2xx）、驗證錯誤（401/403）、節流（429）和其他失敗的分類。

![工作檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/operations.png)

作業活頁簿可讓使用者深入探索所有交易的完整詳細資料，您可以使用最上層磚依結果狀態篩選。

![工作檢視的螢幕擷取畫面](./media/key-vaults-insights-overview/info.png)

使用者也可以根據上方資料表中的特定交易類型來界定視圖的範圍，以動態方式更新較低的資料表，讓使用者可以在彈出內容窗格中查看完整的作業詳細資料。

>[!NOTE]
> 請注意，使用者必須啟用診斷設定，才能查看此活頁簿。 若要深入瞭解啟用診斷設定，請參閱[Azure Key Vault 記錄](https://docs.microsoft.com/azure/key-vault/general/logging)的詳細資訊。

## <a name="pin-and-export"></a>釘選和匯出

您可以選取區段右上角的圖釘圖示，將任何一個度量區段釘選到 Azure 儀表板。

[多訂用帳戶] 和 [金鑰保存庫] 總覽或 [失敗] 活頁簿支援以 Excel 格式匯出結果，方法是選取圖釘圖示左側的 [下載] 圖示。

![已選取釘選圖示的螢幕擷取畫面](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>自訂 Key Vault 的 Azure 監視器

本節重點說明編輯活頁簿以自訂以支援資料分析需求的常見案例：
*  將活頁簿的範圍設為一律選取特定訂用帳戶或金鑰保存庫
* 變更方格中的計量
* 變更要求閾值
* 變更色彩呈現

您可以啟用編輯模式來開始自訂，方法是選取頂端工具列中的 [**自訂**] 按鈕。

![[自訂] 按鈕的螢幕擷取畫面](./media/key-vaults-insights-overview/customize.png)

自訂會儲存至自訂活頁簿，以避免覆寫已發行之活頁簿中的預設設定。 活頁簿會儲存在資源群組中，不論是在私人的 [我的報表] 區段中，或是在可存取資源群組的每個人都可存取的 [共用報表] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫來啟動它。

![活頁簿資源庫的螢幕擷取畫面](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>指定訂用帳戶或金鑰保存庫

您可以執行下列步驟，將多個訂用帳戶和金鑰保存庫總覽或失敗活頁簿設定為每次執行時的特定訂用帳戶或金鑰保存庫的範圍：

1. 從入口網站選取 [**監視**]，然後從左側窗格中選取 [**金鑰保存庫（預覽）** ]。
2. 在 [**總覽**] 活頁簿上，從命令列選取 [**編輯**]。
3. 從 [訂用帳戶] 下拉式清單中選取您想要 yo 做為預設**值的一**或多個訂用帳戶。 請記住，活頁簿最多支援選取10個訂用帳戶。
4. 從 [**金鑰保存庫**] 下拉式清單中，選取您想要用來作為預設值的一或多個帳戶。 請記住，活頁簿支援總共選取200個儲存體帳戶。
5. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。

## <a name="troubleshooting"></a>疑難排解

本節將協助您進行診斷，並針對使用 Key Vault （預覽） Azure 監視器時可能會遇到的常見問題進行疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-issues-or-failures"></a>解決效能問題或失敗

若要協助疑難排解您使用 Azure 監視器 for Key Vault （預覽）識別的任何金鑰保存庫相關問題，請參閱[Azure Key Vault 檔](https://docs.microsoft.com/azure/key-vault/)。

### <a name="why-can-i-only-see-200-key-vaults"></a>為什麼我只能看到200金鑰保存庫？

您可以選取和查看200金鑰保存庫的限制。 無論選取的訂用帳戶數目為何，所選金鑰保存庫的數目限制為200。

### <a name="what-will-happen-when-a-pinned-item-is-clicked"></a>按一下釘選的專案時，會發生什麼事？

當您按一下儀表板上的釘選項目時，它會開啟下列兩件事之一：
* 如果已儲存見解，則會開啟儲存 pin 的見解實例。
* 如果未儲存深入解析，將會開啟新的預設深入解析實例。

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>為什麼我在訂閱選擇器中看不到我的所有訂用帳戶？

我們只會顯示包含金鑰保存庫的訂用帳戶，並從選取的訂用帳戶篩選中選擇，這會在 Azure 入口網站標頭的 [目錄 + 訂用帳戶] 中選取。

![訂用帳戶篩選的螢幕擷取畫面](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>我收到一則錯誤訊息，指出「查詢超過允許的工作區/區域數目上限」，現在該怎麼做？

目前，有25個區域和200個工作區的限制，若要查看您的資料，您將需要減少訂用帳戶和/或資源群組的數目。

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>我想要進行變更或新增其他視覺效果以 Key Vault 深入資訊，我該怎麼做？

若要進行變更，請選取 [編輯模式] 來修改活頁簿，然後將您的工作儲存為新的活頁簿，並系結至指定的訂用帳戶和資源群組。

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>當我們釘選活頁簿的任何部分時，會有什麼時間？

我們會利用「自動」時間細微性，因此取決於選取的時間範圍。

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>釘選活頁簿的任何部分時，時間範圍是什麼？

時間範圍將取決於 [儀表板] 設定。

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>為什麼我在 [作業 & 延遲] 區段底下看不到我的 Key Vault 的任何資料？

若要查看以記錄為基礎的資料，您必須為您想要監視的每個金鑰保存庫啟用記錄。 這可在每個金鑰保存庫的診斷設定底下完成。 您必須將資料傳送至指定的 Log Analytics 工作區。

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>我已經為我的 Key Vault 啟用記錄，為什麼我在作業 & 延遲之後仍無法看到我的資料？

診斷記錄目前無法追溯，因此只有在對您的金鑰保存庫採取動作之後，資料才會開始出現。 因此，可能需要一些時間，範圍從小時到一天，視您的金鑰保存庫的使用方式而定。

此外，如果您選取了大量的金鑰保存庫和訂用帳戶，您可能會因為查詢限制而無法查看您的資料。 若要查看您的資料，您可能需要減少選取的訂用帳戶或金鑰保存庫的數目。 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>如果我想要查看其他資料或建立自己的視覺效果，該怎麼做？ 如何對 Key Vault 深入解析進行變更？

您可以透過使用編輯模式來編輯現有的活頁簿，然後將您的工作儲存為新的活頁簿，將會有所有新的變更。

## <a name="next-steps"></a>後續步驟

瞭解活頁簿設計來支援的案例、如何撰寫新的和自訂現有的報表，以及如何[使用 Azure 監視器活頁簿建立互動式報表](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)。
