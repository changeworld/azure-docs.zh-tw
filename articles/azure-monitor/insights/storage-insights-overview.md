---
title: 使用適用於儲存體的 Azure 監視器監視 Azure 儲存體服務 | Microsoft Docs
description: 此文章描述適用於儲存體的 Azure 監視器功能，可讓儲存體管理員快速了解其 Azure 儲存體帳戶的效能和使用率問題。
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 7ab7071f504231290f72646e59a30fa855cff6cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84944486"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>使用適用於儲存體的 Azure 監視器監視儲存體服務

適用於儲存體的 Azure 監視器，藉由提供 Azure 儲存體服務效能、容量和可用性的統一檢視，讓您能夠全面監視 Azure 儲存體帳戶。 您可以透過兩種方式來觀察儲存體容量和效能：直接從儲存體帳戶檢視，或者從 Azure 監視器檢視，以查看不同的儲存體帳戶群組。 

此文章將協助您了解適用於儲存體的 Azure 監視器所提供的體驗，以大規模獲取有關儲存體帳戶健康情況和效能的可操作知識，並提供著重於熱點和診斷延遲、節流及可用性問題的功能。

## <a name="introduction-to-azure-monitor-for-storage"></a>適用於儲存體的 Azure 監視器簡介

深入體驗之前，您應該先了解其呈現和視覺化資訊的方式。 無論您直接從儲存體帳戶或從 Azure 監視器選取儲存體功能，適用於儲存體的 Azure 監視器會提供一致的體驗。 

將其合併可提供：

* **大規模檢視方塊**，以根據儲存體服務或 API 作業的健康情況來顯示其可用性的快照集檢視；使用率，以顯示儲存體服務所接收的要求總數；以及延遲，以顯示儲存體服務或 API 作業類型處理要求所花費的平均時間。 您也可以依 Blob、檔案、資料表和佇列來檢視容量。

* 特定儲存體帳戶的**向下切入分析**，有助於依類別 (可用性、效能、失敗及容量) 來診斷問題或執行詳細的分析。 選取這其中任一個選項可讓您深入檢視計量。  

* **可自訂的**，讓您能夠變更想要查看的計量、修改或設定符合限制的閾值，並另存為自己的活頁簿。 活頁簿中的圖表均可釘選到 Azure 儀表板。  

此功能不會要求您啟用或設定任何項目，預設會從您的儲存體帳戶收集儲存體計量。 如果您不熟悉 Azure 儲存體上可用的計量，請藉由檢閱 [Azure 儲存體計量](../../storage/common/storage-metrics-in-azure-monitor.md)來檢視 Azure 儲存體計量中的描述和定義。

>[!NOTE]
>存取此功能不需要付費，您只需要支付所設定或啟用的 Azure 監視器基本功能的費用，如 [Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面上所述。

>[!NOTE]
>適用於儲存體的 Azure 監視器不支援[一般用途 v1 帳戶](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>從 Azure 監視器檢視

在 Azure 監視器中，您可以檢視來自訂用帳戶中多個儲存體帳戶的交易、延遲和容量詳細資料，並協助識別效能、容量問題及失敗。

若要檢視您所有訂用帳戶之間儲存體帳戶的使用率和可用性，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站的左窗格中選取 [監視器]，然後在 [見解] 區段下，選取 [儲存體帳戶]。

    ![多個儲存體帳戶檢視](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>[概觀] 活頁簿

在所選訂用帳戶的 [概觀] 活頁簿上，該資料表會針對訂用帳戶內已分組的最多 10 個儲存體帳戶，顯示互動式儲存體計量和服務可用性狀態。 您可以根據您從下列下拉式清單中選取的選項，篩選結果：

* **訂用帳戶**：僅列出具有儲存體帳戶的訂用帳戶。  

* **儲存體帳戶**：預設會預先選取 10 個儲存體帳戶。 如果您在範圍選取器中選取所有或多個儲存體帳戶，最多將傳回 200 個儲存體帳戶。 例如，如果您所選取的三個訂用帳戶共有 573 個儲存體帳戶，則只會顯示 200 個帳戶。 

* **時間範圍**：預設會根據對應的選取項目，顯示過去 4 小時的資訊。

位於下拉式清單底下的計數器磚會彙總訂用帳戶中的儲存體帳戶總數，並反映已選取的總數。 活頁簿中會針對資料行顯示條件式色彩編碼或熱度圖，可用來報告交易計量或錯誤。 最深的色彩具有最高的值，而較淺的色彩是以最低值為基礎。 針對以錯誤為基礎的資料行，此值為紅色，而針對以計量為基礎的資料行，此值為藍色。

在 [可用性]、[E2E 延遲]、[伺服器延遲] 與 [交易錯誤類型/錯誤] 資料行中選取一個值，可將您導向至為特定儲存體帳戶類型量身打造的報表，其符合針對該儲存體帳戶所選取的資料行。 如需適用於每個類別之活頁簿的詳細資訊，請參閱以下[詳細的儲存體活頁簿](#detailed-storage-workbooks)一節。 

>[!NOTE]
>如需可在報表中顯示哪些錯誤的詳細資訊，請參閱[回應類型結構描述](../../storage/common/monitor-storage-reference.md#metrics-dimensions)，並尋找 **ServerOtherError**、**ClientOtherError**、**ClientThrottlingError** 之類的回應類型。 視選取的儲存體帳戶而定，如果報告的錯誤類型超過三種，所有其他錯誤都會顯示於**其他**類別底下。

預設的**可用性**閾值為：

* 警告：99%
* 重大：90%

若要根據觀察或需求的結果設定可用性閾值，請參閱[修改可用性閾值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>[容量] 活頁簿

選取頁面頂端的 [容量]，[容量] 活頁簿就會開啟。 此活頁簿會為您顯示帳戶中所使用的儲存體總量，以及帳戶中每個資料服務所使用的容量，以協助識別使用率不足和過度的儲存體。

![多個儲存體帳戶的 [容量] 活頁簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

活頁簿中會針對資料行顯示條件式色彩編碼或熱度圖，其會以藍色值報告容量計量。 最深的色彩具有最高的值，而較淺的色彩是以最低值為基礎。

當您在活頁簿中的任一個資料行底下選取值時，即會向下切入至儲存體帳戶的 [容量] 活頁簿。 如需有關向下切入報表的進一步詳細資料，請參閱以下[詳細的儲存體活頁簿](#detailed-storage-workbooks)一節。 

## <a name="view-from-a-storage-account"></a>從儲存體帳戶檢視

直接從儲存體帳戶存取適用於 VM 的 Azure 監視器：

1. 在 Azure 入口網站中，選取 [儲存體帳戶]。

2. 從清單中，選擇儲存體帳戶。 在 [監視] 區段中，選擇 [見解]。

    ![所選取儲存體帳戶的 [概觀] 頁面](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在儲存體帳戶的 [概觀] 活頁簿上，其會顯示數個可協助您快速評估的儲存體效能計量：

* 儲存體服務的健康情況，可立即查看控制項以外的問題是否正影響其部署所在區域中的儲存體服務，相關敘述列於 [摘要] 資料行底下。

* 互動式效能圖表，可顯示與儲存體容量、可用性、交易及延遲相關的最基本詳細資料。  

* 計量和狀態磚，可醒目提示服務可用性、對儲存體服務的交易總數、E2E 延遲及伺服器延遲。

針對 [失敗]、[效能]、[可用性] 和 [容量] 選取任一個按鈕，即會開啟個別活頁簿。 

![所選取儲存體帳戶的 [概觀] 頁面](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>詳細的儲存體活頁簿

無論您是從多個儲存體帳戶 [概觀] 活頁簿的 [可用性]、[E2E 延遲]、[伺服器延遲] 與 [交易錯誤類型/錯誤] 資料行中選取值，或從特定儲存體帳戶的 [概觀] 活頁簿中針對 [失敗]、[效能]、[可用性] 及 [容量] 選取任一個按鈕，每個均提供一組專為該類別量身打造的互動式儲存體相關資訊。  

* [可用性] 會開啟 [可用性] 活頁簿。 此活頁簿會顯示 Azure 儲存體服務目前的健全狀態；一個資料表，可顯示由儲存體帳戶中定義的資料服務所分類之每個物件的可用健全狀態，以及代表所選時間範圍的趨勢線；以及對於帳戶中每個資料服務的可用性趨勢圖表。  

    ![可用性報表範例](./media/storage-insights-overview/storage-account-availability-01.png)

* [E2E 延遲] 和 [伺服器延遲] 會開啟 [效能] 活頁簿。 此活頁簿包含一個彙總狀態磚，可顯示 E2E 延遲和伺服器延遲；一個效能圖表，可顯示 E2E 與伺服器延遲；以及一個資料表，可根據儲存體帳戶中定義之資料服務所分類的 API 細分成功呼叫的延遲。

    ![效能報表範例](./media/storage-insights-overview/storage-account-performance-01.png)

* 選取方格中列出的任何錯誤類別，即會開啟 [失敗] 活頁簿。 此報表顯示所有其他用戶端錯誤的計量磚 (已描述的錯誤除外)，以及成功的要求、用戶端節流的錯誤、ClientOtherError 屬性特定之交易 [回應類型] 維度計量的效能圖表，以及兩個資料表：**依 API 名稱排序的交易**和**依回應類型排序的交易**。

   ![失敗報表範例](./media/storage-insights-overview/storage-account-failures-01.png)

* [容量] 會開啟 [容量] 活頁簿。 此活頁簿會在磚和圖表中顯示帳戶內針對每個儲存體資料物件所使用的儲存體總量，以及帳戶中儲存了多少個資料物件。  

    ![所選取儲存體帳戶的 [容量] 頁面](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>釘選和匯出

您可以將任一個計量區段釘選到 Azure 儀表板，只要選取該區段右上角的圖釘圖示即可。

![計量區段釘選到儀表板範例](./media/storage-insights-overview/workbook-pin-example.png)

多重訂用帳戶和儲存體帳戶的 [概觀] 或 [容量] 活頁簿支援藉由選取圖釘圖示右側的向下箭頭圖示，以 Excel 格式匯出結果。

![匯出活頁簿方格結果範例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>自訂適用於儲存體的 Azure 監視器

此節主要說明編輯自訂活頁簿以支援資料分析需求的常見案例：

* 將活頁簿的範圍設定為一律選取特定訂用帳戶或儲存體帳戶
* 變更方格中的計量
* 變更可用性閾值
* 變更色彩呈現

自訂會儲存至自訂活頁簿，以避免覆寫已發佈活頁簿中的預設設定。 活頁簿會儲存在資源群組內，位於您私人使用的 [我的報表] 區段中，或是可供每個具有資源群組存取權的人員存取的 [共用報表] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫加以啟動。

![從命令列啟動活頁簿庫](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定訂用帳戶或儲存體帳戶

您可以將多重訂用帳戶和儲存體帳戶 [概觀] 或 [容量] 活頁簿的範圍設定為每次執行時的特定訂用帳戶或儲存體帳戶，請執行下列步驟。

1. 在入口網站中選取 [監視器]，然後在左窗格中選取 [儲存體帳戶]。

2. 在 [概觀] 活頁簿上，從命令列中選取 [編輯]。

3. 從 [訂用帳戶] 下拉式清單中選取要作為預設值的一或多個訂用帳戶。 請記住，活頁簿最多支援選取 10 個訂用帳戶。  

4. 從 [儲存體帳戶] 下拉式清單中選取要作為預設值的一或多個帳戶。 請記住，活頁簿最多支援選取 200 個儲存體帳戶。 

5. 從命令列中選取 [另存新檔]，以使用您的自訂來儲存活頁簿的複本，然後按一下 [完成編輯] 以回到閱讀模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改活頁簿中的計量和色彩

預先建置的活頁簿包含計量資料，而您可以修改或移除任何一個視覺效果，並自訂您小組的特定需求。

在我們的範例中，我們會使用多重訂用帳戶和儲存體帳戶容量活頁簿，以示範如何：

* 移除計量
* 變更色彩呈現

您可以對任一個預先建置的 [失敗]、[效能]、[可用性] 及 [容量] 活頁簿執行相同變更。

1. 在入口網站中選取 [監視器]，然後在左窗格中選取 [儲存體帳戶]。

2. 選取 [容量] 以切換至容量活頁簿，然後從命令列中，選取命令列的 [編輯]。

    ![選取 [編輯] 以修改活頁簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在 [計量] 區段旁邊，選取 [編輯]。

    ![選取 [編輯] 以修改容量活頁簿計量](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我們即將移除 [帳戶使用的容量時間表] 資料行，因此，請選取計量方格中的 [資料行設定]。

    ![編輯資料行設定](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 [編輯資料行設定] 窗格中，選取 [資料行] 區段底下的 **microsoft.storage/storageaccounts-Capacity-UsedCapacity Timeline$|Account used capacity Timeline$** ，並在 [資料行轉譯器] 下拉式清單底下，選取 [隱藏]。

6. 選取 [儲存後關閉] 以認可變更。

現在，讓我們將報表中容量計量的色彩佈景主題變更為使用綠色，而不是藍色。

1. 在計量方格中，選取 [資料行設定]。

2. 在 [編輯資料行設定] 窗格中，選取 [資料行] 區段底下的 **microsoft.storage/storageaccounts-Capacity-UsedCapacity$|microsoft.storage/storageaccounts/blobservices-Capacity-BlobCapacity$|microsoft.storage/storageaccounts/fileservices-Capacity-FileCapacity$|microsoft.storage/storageaccounts/queueservices-Capacity-QueueCapacity$|microsoft.storage/storageaccounts/tableservices-Capacity-TableCapacity$** 。 在 [調色盤] 下拉式清單中，選取 [綠色]。

3. 選取 [儲存後關閉] 以認可變更。

4. 從命令列中選取 [另存新檔]，以使用您的自訂來儲存活頁簿的複本，然後按一下 [完成編輯] 以回到閱讀模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性閾值

在此範例中，我們會使用儲存體帳戶容量活頁簿，並示範如何修改可用性閾值。 根據預設，磚和方格報告的百分比可用性會設定為閾值下限 90 且閾值上限 99。 我們會將 [依 API 名稱排序的可用性] 方格中 [可用性 %] 的閾值下限變更為 85%，這表示當閾值低於 85% 時，健全狀態就會變更為重大。 

1. 在入口網站中選取 [儲存體帳戶]，然後從清單中選取儲存體帳戶。

2. 從左窗格中選取 [見解]。

3. 在活頁簿中，選取 [可用性] 以切換至可用性活頁簿，然後從命令列中選取 [編輯]。 

4. 向下捲動至頁面底部，然後在 [依 API 排序的可用性] 方格旁的左側，選取 [編輯]。

    ![編輯 [依 API 名稱排序的可用性] 方格設定](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 選取 [資料行設定]，然後在 [編輯資料行設定] 窗格的 [資料行] 區段底下，選取 [可用性 (%) (閾值 + 已格式化)]。

6. 將 [重大] 健全狀態的值從 **90** 變更為 **85**，然後按一下 [儲存後關閉]。

    ![修改重大狀態的可用性閾值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 從命令列中選取 [另存新檔]，以使用您的自訂來儲存活頁簿的複本，然後按一下 [完成編輯] 以回到閱讀模式。

## <a name="troubleshooting"></a>疑難排解

如需一般疑難排解指導方針，請參閱專用的活頁簿型深入解析[疑難排解文章](troubleshoot-workbooks.md)。

此節將協助您診斷在使用適用於儲存體的 Azure 監視器時可能遇到的一些常見問題，並進行疑難排解。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解決效能、容量或可用性問題

若要協助對您使用適用於儲存體的 Azure 監視器所識別的任何儲存體相關問題進行疑難排解，請參閱 Azure 儲存體[疑難排解指導方針](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>為什麼我只能看到 200 個儲存體帳戶？

無論選取了多少個訂用帳戶，選取的儲存體帳戶數目均限定為 200 個。

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>如何變更色彩設定和可用性閾值？

請參閱[修改可用性閾值](storage-insights-overview.md#modify-the-availability-threshold)一節，以了解如何變更色彩設定和可用性閾值的詳細步驟。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>如何分析適用於儲存體的 Azure 監視器中所顯示的資料並進行疑難排解？

 如需如何分析適用於儲存體的 Azure 監視器中所顯示的 Azure 儲存體資料並進行疑難排解的詳細資訊，請參閱[監視、診斷與疑難排解 Microsoft Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting)一文。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>為什麼我在計量中看不到所有類型的錯誤？

目前，最多只會顯示三種不同類型的錯誤，而其餘錯誤會在單一貯體中群組在一起。 其會使用 splitByLimit 來控制，而且可以修改。 若要變更此屬性：

1. 按一下編輯活頁簿。
2. 移至計量、按一下編輯，然後選取 [交易、總和] 或您想要編輯的任何計量。

    ![移至計量、按一下編輯，然後按一下 [交易、總和]](./media/storage-insights-overview/fqa7.png)

3. 接著，變更 [分割數目]。

    ![選取計量參數](./media/storage-insights-overview/fqa7-2.png)

如果您想要查看 n 個不同類型的錯誤，而不是將 splitByLimit 指定為 n+1，則要針對其餘錯誤額外加 1。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>我已將活頁簿儲存於某個儲存體帳戶上。 為什麼我現在找不到該活頁簿？

每個活頁簿都會儲存於您儲存時所使用的儲存體帳戶中。 嘗試尋找使用者儲存活頁簿時所使用的特定儲存體帳戶。 否則，就無法在不知道資源 (儲存體帳戶) 的情況下，尋找特定的活頁簿。

## <a name="next-steps"></a>後續步驟

* 設定[計量警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications.md)，以設定自動化警示來協助偵測問題。

* 檢閱[使用 Azure 監視器活頁簿建立互動式報表](../platform/workbooks-overview.md)，以了解設計活頁簿以提供支援、如何撰寫新報表和自訂現有報表等等的案例。

* 如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
