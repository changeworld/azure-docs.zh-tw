---
title: 使用適用于儲存體的 Azure 監視器監視 Azure 儲存體服務（預覽） |Microsoft Docs
description: 本文說明儲存體功能的 Azure 監視器，可讓儲存體管理員快速瞭解其 Azure 儲存體帳戶的效能和使用問題。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/15/2019
ms.openlocfilehash: 497ea5a27a56ed5f8192b694fac1c52c8bf57ca6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691120"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>使用適用于儲存體的 Azure 監視器監視儲存體服務（預覽）

適用于儲存體的 Azure 監視器（預覽）藉由提供 Azure 儲存體服務效能、容量和可用性的統一觀點，全面監視您的 Azure 儲存體帳戶。 您可以透過兩種方式來觀察儲存體容量和效能、直接從儲存體帳戶或從 Azure 監視器查看，查看不同的儲存體帳戶群組。 

本文將協助您瞭解儲存體（預覽） Azure 監視器的體驗，以針對大規模的儲存體帳戶健全狀況和效能衍生可採取行動的知識，並提供著重于熱點和診斷延遲、節流和可用性問題的功能。

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>儲存體 Azure 監視器簡介（預覽）

在深入探討經驗之前，您應該先瞭解它呈現和視覺化資訊的方式。 無論您是直接從儲存體帳戶或從 Azure 監視器選取儲存體功能，Azure 監視器的儲存體都會提供一致的體驗。 

結合的 it 提供：

* 根據儲存體服務或 API 作業的健全狀況，**大規模**顯示其可用性的快照集視圖、顯示儲存體服務接收的要求總數，以及顯示儲存體服務或 API 作業類型處理要求所花費的平均時間的延遲。 您也可以依 blob、檔案、資料表和佇列來查看容量。

* 向下切入特定儲存體帳戶的**分析**，以協助診斷問題，或依類別（可用性、效能、失敗和容量）執行詳細的分析。 選取其中任何一個選項可提供計量的深入觀點。  

* 可**自訂**，您可以在其中變更您想要查看的計量、修改或設定符合限制的臨界值，以及另存為您自己的活頁簿。 活頁簿中的圖表可以釘選到 Azure 儀表板。  

這項功能不會要求您啟用或設定任何專案，預設會收集儲存體帳戶的儲存體計量。 如果您不熟悉 Azure 儲存體上可用的計量，請參閱[Azure 儲存體計量](../../storage/common/storage-metrics-in-azure-monitor.md)來查看 Azure 儲存體計量中的描述和定義。

>[!NOTE]
>存取這項功能不需要付費，您只需支付所設定或啟用的 Azure 監視器基本功能的費用，如[Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面所述。

>[!NOTE]
>儲存體的 Azure 監視器不支援[一般用途 v1 帳戶](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>從 Azure 監視器查看

從 Azure 監視器，您可以從訂用帳戶中的多個儲存體帳戶查看交易、延遲和容量詳細資料，並協助識別效能、容量問題和失敗。

若要查看您所有訂用帳戶的儲存體帳戶使用率和可用性，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從 Azure 入口網站的左側窗格中選取 [**監視**]，然後在 [**深入**解析] 區段下，選取 [**儲存體帳戶（預覽）**]。

    ![多個儲存體帳戶的觀點](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>總覽活頁簿

在所選訂用帳戶的**總覽**活頁簿中，資料表會顯示在訂用帳戶內分組的最多10個儲存體帳戶的互動式儲存體計量和服務可用性狀態。 您可以根據您從下列下拉式清單中選取的選項來篩選結果：

* 訂用帳戶-只會列出具有儲存體帳戶**的訂閱。**  

* **儲存體帳戶**-預設會預先選取10個儲存體帳戶。 如果您在 [範圍] 選取器中選取 [所有或多個儲存體帳戶]，則會傳回最多200個儲存體帳戶。 例如，如果您所選取的三個訂用帳戶共有573個儲存體帳戶，則只會顯示200個帳戶。 

* **時間範圍**：根據預設，會依據對應的選取專案顯示最後4小時的資訊。

下拉式清單底下的 [計數器] 磚會匯總訂用帳戶中的儲存體帳戶總數，並反映選取的總計數。 活頁簿中的資料行有條件式色彩編碼或熱度圖，可報告交易計量或錯誤。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。 針對以錯誤為基礎的資料行，此值為紅色，而針對以度量為基礎的資料行，其值為藍色。

在 [資料行**可用性**]、[ **E2E 延遲**]、[**伺服器延遲**] 和 [**交易錯誤類型/錯誤**] 中選取值，會將您導向至符合針對該儲存體帳戶選取之資料行的特定儲存體度量類型的報表。 如需每個類別之活頁簿的詳細資訊，請參閱下面的[詳細儲存活頁簿](#detailed-storage-workbooks)一節。 

>[!NOTE]
>如需報告中可顯示哪些錯誤的詳細資訊，請參閱[回應類型架構](../../storage/common/monitor-storage-reference.md#metrics-dimensions)和尋找回應類型，例如**ServerOtherError**、 **ClientOtherError**、 **ClientThrottlingError**。 視選取的儲存體帳戶而定，如果報告的錯誤類型超過三個，所有其他錯誤都會在 [**其他**] 類別下呈現。

預設**可用性**閾值為：

* 警告-99%
* 重大-90%

若要根據觀察或需求的結果來設定可用性閾值，請參閱[修改可用性閾值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量活頁簿

選取頁面頂端的 [**容量**]，[**容量**] 活頁簿隨即開啟。 它會顯示帳戶中所使用的總儲存體數量，以及帳戶中每個資料服務所使用的容量，以協助識別使用中的儲存空間。

![多個儲存體帳戶容量活頁簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

活頁簿中的資料行有條件式色彩編碼或熱度圖，會以藍色值來報告容量計量。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。

當您在活頁簿中的任何一個資料行底下選取一個值時，您會向下切入至儲存體帳戶的**容量**活頁簿。 有關向下切入報表的進一步詳細資料，請參閱下面的[詳細儲存體活頁簿](#detailed-storage-workbooks)一節。 

## <a name="view-from-a-storage-account"></a>從儲存體帳戶查看

若要直接從儲存體帳戶存取適用於 VM 的 Azure 監視器：

1. 在 [Azure 入口網站中，選取 [儲存體帳戶]。

2. 從清單中選擇儲存體帳戶。 在 [監視] 區段中，選擇 [Insights （預覽）]。

    ![選取的儲存體帳戶總覽頁面](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在儲存體帳戶的**總覽**活頁簿中，它會顯示數個儲存體效能計量，可協助您快速評估：

* 儲存體服務的健康情況，以立即查看控制項外部的問題是否會影響其部署目的地區域中的儲存體服務，這會在 [**摘要**] 資料行底下指出。

* 互動式效能圖表，顯示與儲存體容量、可用性、交易和延遲相關的最基本詳細資料。  

* 顯示服務可用性、儲存體服務的交易總數、E2E 延遲和伺服器延遲的計量和狀態磚。

針對 [**失敗**]、[**效能**]、[**可用性**] 和 [**容量**] 選取任何一個按鈕，會開啟個別的活頁簿。 

![選取的儲存體帳戶總覽頁面](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>詳細的儲存活頁簿

無論您是從 [多個儲存體帳戶**總覽**] 活頁簿中選取 [資料行**可用性**]、[ **E2E 延遲**]、[**伺服器延遲**] 和 [**交易錯誤類型/錯誤**] 的值，或是從特定儲存體帳戶的 **[總覽**] 活頁簿中選取任何一個**失敗**、**效能**、**可用性**和**容量**的按鈕，每個都提供一組專為該類別量身打造的互動式  

* **可用性**會開啟**可用性**活頁簿。 它會顯示目前的 Azure 儲存體服務健全狀況狀態，這是一個資料表，其中顯示由儲存體帳戶中定義的資料服務所分類之每個物件的可用健全狀況狀態，並具有代表所選時間範圍的趨勢線，以及帳戶中每個資料服務的可用性趨勢圖表。  

    ![可用性報告範例](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 延遲**和**伺服器延遲**會開啟 [**效能**] 活頁簿。 其中包含 [匯總狀態] 磚，其中顯示 E2E 延遲和伺服器延遲、E2E 與伺服器延遲的效能圖表，以及根據儲存體帳戶中定義的資料服務所分類的 API，細分成功呼叫的延遲的資料表。

    ![效能報告範例](./media/storage-insights-overview/storage-account-performance-01.png)

* 選取方格中列出的任何錯誤類別，即會開啟 [**失敗**] 活頁簿。 此報表會顯示所有其他用戶端錯誤的計量磚，但描述了其中一個和一個成功的要求、用戶端節流錯誤、ClientOtherError 屬性特有的交易**回應類型**維度計量的效能圖表，以及兩個數據表-依**回應類型**的**API 名稱**和交易的交易。

   ![失敗報告範例](./media/storage-insights-overview/storage-account-failures-01.png)

* **容量**會開啟**容量**活頁簿。 它會顯示磚和圖表中帳戶內每個儲存體資料物件所使用的儲存體總量，以及帳戶中儲存多少個數據物件。  

    ![選取的儲存體帳戶容量頁面](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>釘選和匯出

您可以選取區段右上角的圖釘圖示，將任何一個度量區段釘選到 Azure 儀表板。

![度量區段釘選至儀表板範例](./media/storage-insights-overview/workbook-pin-example.png)

多訂用帳戶和儲存體帳戶的**總覽**或**容量**活頁簿支援以 Excel 格式匯出結果，方法是選取圖釘圖示右邊的向下箭號圖示。

![匯出活頁簿方格結果範例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>自訂儲存體的 Azure 監視器（預覽）

本節重點說明編輯活頁簿以自訂以支援資料分析需求的常見案例：

* 將活頁簿的範圍設為一律選取特定訂用帳戶或儲存體帳戶
* 變更方格中的計量
* 變更可用性閾值
* 變更色彩呈現

這些自訂專案會儲存至自訂活頁簿，以避免覆寫已發行之活頁簿中的預設設定。 活頁簿會儲存在資源群組中，不論是在您私人的**我的報表**區段中，或是在可存取資源群組的每個人都可存取的 [**共用報表**] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫來啟動它。

![從命令列啟動活頁簿圖庫](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定訂用帳戶或儲存體帳戶

您可以在每次執行時，將多個訂用帳戶和儲存體帳戶**總覽**或**容量**活頁簿設定為特定訂用帳戶或儲存體帳戶的範圍，然後執行下列步驟。

1. 從入口網站選取 [**監視**]，然後從左側窗格中選取 [**儲存體帳戶（預覽）** ]。

2. 在 [**總覽**] 活頁簿上，從命令列選取 [**編輯**]。

3. 從 [訂用帳戶] 下拉式**清單中選取**您想要它預設為的一個或多個訂用帳戶。 請記住，活頁簿最多支援選取10個訂用帳戶。  

4. 從 [**儲存體帳戶**] 下拉式清單中選取您想要其預設的一或多個帳戶。 請記住，活頁簿支援總共選取200個儲存體帳戶。 

5. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改活頁簿中的計量和色彩

預先建立的活頁簿包含計量資料，而且您可以修改或移除任何一個視覺效果，並自訂您小組的特定需求。

在我們的範例中，我們會使用多訂用帳戶和儲存體帳戶容量活頁簿，以示範如何：

* 移除度量
* 變更色彩呈現

您可以對任何一個預先建立的**失敗**、**效能**、**可用性**和**容量**活頁簿執行相同的變更。

1. 從入口網站選取 [**監視**]，然後從左側窗格中選取 [**儲存體帳戶（預覽）** ]。

2. 選取 [**容量**] 以切換至 [容量] 活頁簿，並從命令列中選取命令列中的 [**編輯**]。

    ![選取 [編輯] 以修改活頁簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在 [計量] 區段旁，選取 [**編輯**]。

    ![選取 [編輯] 以修改容量活頁簿計量](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我們即將移除 [使用的**帳戶容量時間軸**] 資料行，因此請選取 [計量] 方格中的 [資料**行設定**]。

    ![編輯資料行設定](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 [**編輯資料行設定**] 窗格中，選取 [microsoft] 資料**行**區段底下的 [ **Storageaccounts-容量-UsedCapacity 時間軸 $ |]。帳戶已使用容量時間軸 $**，並在下拉式清單資料**行**轉譯器底下選取 [**隱藏**]。

6. 選取 [**儲存並關閉**] 以認可變更。

現在，讓我們將報表中容量計量的色彩主題變更為使用綠色，而不是藍色。

1. 選取 [計量] 方格中的 [資料**行設定**]。

2. 在 [**編輯資料行設定**] 窗格中，**選取**[ **storageaccounts-容量-UsedCapacity $ | microsoft. storage/storageaccounts/blobservices-容量-BlobCapacity**$ | microsoft. storage/storageaccounts/fileservices-容量-FileCapacity $ | microsoft. storage/storageaccounts/queueservices-容量-QueueCapacity $]。 在下拉式清單的 [**色板**] 底下，選取 [**綠色**]。

3. 選取 [**儲存並關閉**] 以認可變更。

4. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性閾值

在此範例中，我們會使用儲存體帳戶容量活頁簿，並示範如何修改可用性閾值。 根據預設，磚和方格報告百分比的可用性設定為最低閾值為90，閾值上限為99。 我們將 [**可用性（依據 API 名稱**）] 方格的 [**可用性%** ] 的最小臨界值變更為85%，這表示當閾值低於85% 時，健全狀況狀態會變更為 [重大]。 

1. 從入口網站中選取 [**儲存體帳戶**]，然後從清單中選取儲存體帳戶。

2. 從左側窗格中選取 [ **Insights （預覽）** ]。

3. 在活頁簿中，選取 [**可用性**] 以切換至可用性活頁簿，然後從命令列選取 [**編輯**]。 

4. 向下流覽至頁面底部，然後在左側 [**可用性依據 API** ] 方格旁，選取 [**編輯**]。

    ![依 API 名稱方格設定編輯可用性](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 選取 [資料**行設定**]，然後在 [**編輯資料行設定**] 窗格的 [資料**行**] 區段下選取 [**可用性（%）（閾值 + 已格式化）**。

6. 將 [**重大**健全狀況狀態] 的值從**90**變更為**85** ，然後按一下 [**儲存並關閉**]。

    ![修改重大狀態的可用性臨界值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。

## <a name="troubleshooting"></a>疑難排解

本節將協助您進行診斷，並疑難排解使用 Azure 監視器儲存（預覽）時可能會遇到的一些常見問題。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解決效能、容量或可用性問題

若要協助針對您使用 Azure 監視器 for Storage （預覽）識別的任何儲存體相關問題進行疑難排解，請參閱 Azure 儲存體[疑難排解指引](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

### <a name="why-can-i-only-see-200-storage-accounts"></a>為什麼我只能看到200儲存體帳戶？

所選儲存體帳戶的數目限制為200，不論選取的訂閱數目為何。

### <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>當我按一下儀表板中最近釘選的磚時，會發生什麼事？

* 如果您按一下磚上的任何位置，則會帶您前往已釘選磚的索引標籤。 例如，如果您在 [儲存體帳戶總覽] 索引標籤中釘選圖形，那麼當您按一下儀表板中的磚時，就會開啟該預設視圖，不過，如果您從自己儲存的複本釘選圖形，則會開啟您儲存的複本。
* 標題左上方的篩選圖示會開啟 [設定磚設定] 索引標籤。
* 右上方的橢圓形圖示會提供您 [自訂標題資料]、[自訂]、[重新整理] 和 [從儀表板移除] 的選項。

### <a name="what-happens-when-i-save-a-workbook"></a>當我儲存活頁簿時，會發生什麼事？

* 當您儲存活頁簿時，它可讓您使用編輯和變更標題來建立活頁簿的新複本。 儲存不會覆寫活頁簿，目前的活頁簿一律會是預設的視圖。
* **未**儲存的活頁簿只是預設的視圖。


### <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>為什麼我在入口網站中看不到我的所有訂用帳戶？

入口網站只會在入口網站啟動時顯示所選訂用帳戶的資料。 若要變更選取的訂用帳戶，請移至右上方，然後按一下具有篩選圖示的筆記本。 這會顯示 [目錄 + 訂用帳戶] 索引標籤。

![目錄 + 訂閱](./media/storage-insights-overview/fqa3.png)

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>如何變更可用性的著色和閾值？

請參閱[修改可用性閾值](storage-insights-overview.md#modify-the-availability-threshold)一節，以瞭解如何變更可用性的著色和閾值的詳細步驟。

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>如何分析儲存 Azure 監視器中顯示的資料並進行疑難排解？

 請參閱[監視、診斷和疑難排解 Microsoft Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-monitoring-diagnosing-troubleshooting)一文，以取得有關如何分析和疑難排解 Azure 監視器中顯示的 Azure 儲存體資料的詳細資訊。

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>為什麼我在計量中看不到所有類型的錯誤？

目前，最多會顯示三種不同類型的錯誤，而其餘的錯誤會在單一值區中群組在一起。 它是使用 splitByLimit 來控制，而且可以修改。 若要變更此屬性：

1. 按一下 [編輯活頁簿]。
2. 移至 [計量]，按一下 [編輯]，然後選取 [**交易]、** [加總] 或您想要編輯的任何度量。

    ![移至 [計量]，然後按一下 [交易、總和] 上的 [編輯]](./media/storage-insights-overview/fqa7.png)

1. 然後變更分割數目。

    ![選取度量參數」](./media/storage-insights-overview/fqa7-2.png)

如果您想要查看與指定 splitByLimit 為 n + 1 不同的錯誤類型，則會針對其餘錯誤額外加上1。

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>我在某個儲存體帳戶上儲存了活頁簿。 為什麼我無法立即找到它？

每個活頁簿都會儲存在您儲存它的儲存體帳戶中。 嘗試尋找使用者儲存活頁簿的特定儲存體帳戶。 否則，就無法尋找特定的活頁簿，而不需要知道資源（儲存體帳戶）。

### <a name="what-is-time-range"></a>什麼是時間範圍？

時間範圍會顯示特定時間範圍內的資料。 例如，如果時間範圍是24小時，則會顯示過去24小時內的資料。

### <a name="what-is-time-granularity-time-grain"></a>什麼是時間細微性（時間細微性）？

時間細微性是兩個資料點之間的時間差。 例如，如果 [時間細微性] 設定為1秒，表示每秒都會收集計量。

### <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>將活頁簿的任何部分釘選到儀表板後，會有什麼時間細微性？

預設的時間細微性設定為 [自動]，目前無法變更。

### <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>如何? 變更儀表板上活頁簿步驟的 timespan/time 範圍嗎？

根據預設，儀表板磚上的 timespan/time 範圍設定為24小時，若要變更此值，請按一下右上方的省略號，選取 [**自訂磚資料**]，勾選 [覆寫標題層級的儀表板時間設定] 方塊，然後使用下拉式功能表挑選 timespan。  

![選取磚右上角的省略號，然後選擇 [自訂此資料]](./media/storage-insights-overview/fqa-data-settings.png)

![在 [設定磚設定] 中，選取 [timespan] 下拉式清單以變更 timespan/time 範圍](./media/storage-insights-overview/fqa-timespan.png)

### <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>如何? 變更活頁簿或已釘選到儀表板之活頁簿步驟的標題？

釘選到儀表板之活頁簿或活頁簿步驟的標題，會保留其在活頁簿中的相同名稱。 若要變更標題，您必須儲存自己的活頁簿複本。 接著，您就能夠在按下 [儲存] 之前先命名活頁簿。

![選取頂端的 [儲存] 以儲存活頁簿的複本，並變更其名稱](./media/storage-insights-overview/fqa-change-workbook-name.png)

若要變更已儲存活頁簿中步驟的名稱，請選取步驟底下的 [編輯]，然後選取 [設定] 最下方的齒輪。

![選取活頁簿步驟底部的 [編輯]，以開啟 [設定](./media/storage-insights-overview/fqa-edit.png)
![] 中的設定選取底部的齒輪，即可變更步驟名稱](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>後續步驟

* 設定計量[警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications.md)，以設定自動化警示以協助偵測問題。

* 瞭解活頁簿設計來支援的案例、如何撰寫新的和自訂現有的報表，以及如何[使用 Azure 監視器活頁簿建立互動式報表](../app/usage-workbooks.md)。

* 如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
