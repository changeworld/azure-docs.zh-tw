---
title: Azure 監視器中的監視解決方案 | Microsoft Docs
description: Azure 監視器中的監視解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。  本文提供有關安裝及使用監視解決方案的資訊。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360810"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure 監視器中的監視解決方案
監視解決方案會運用 Azure 中的服務，提供特定應用程式或服務的額外作業見解。 本文提供 Azure 中監視解決方案的簡短概觀，以及使用和安裝它們的詳細資料。

> [!NOTE]
> 監視解決方案先前稱為管理解決方案。

監視解決方案通常會收集記錄資料，並提供查詢和檢視來分析所收集的資料。 它們也會運用 Azure 自動化等其他服務，執行應用程式或服務相關的動作。

您可以針對您使用的任何應用程式和服務，將監視解決方案新增至 Azure 監視器。 它們通常是免費提供的，但會收集可能會叫用使用費用的資料。 除了 [Microsoft 所提供的解決方案](solutions-creating.md)，合作夥伴和客戶可以建立要用於其自己的環境中或可透過社群供您客戶使用的管理解決方案。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>使用監視解決方案
在 Azure 監視器中開啟 [概觀] 頁面，以顯示工作區中每個已安裝解決方案的圖格。 

1. 移至 [Azure 入口網站](https://ms.portal.azure.com)。 搜尋並選取 [**監視**]。
1. 在 [Insights] 功能表下方，選取 [其他]。
1. 使用畫面頂端的下拉式清單方塊，來變更針對圖格所使用的工作區或時間範圍。
1. 按一下解決方案的圖格，以開啟其視圖，其中包含更詳細的分析收集的資料。

![總覽](media/solutions/overview.png)

監視解決方案可以包含多種的 Azure 資源，而您可以檢視解決方案隨附的任何資源，就像任何其他資源一樣。 例如，解決方案中包含的任何記錄查詢都會列在 [查詢總管](../log-query/get-started-portal.md#load-queries) 中的 [解決方案查詢] 下方。您在使用[記錄查詢](../log-query/log-query-overview.md) 執行臨機操作分析時，使用那些查詢。

## <a name="list-installed-monitoring-solutions"></a>列出已安裝的監視解決方案 
使用下列程序，列出您的訂用帳戶中已安裝的監視解決方案。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com)。 搜尋並選取 [**解決方案**]。
1. 隨即列出您所有工作區中安裝的解決方案。 解決方案的名稱後面接著其安裝所在的工作區名稱。
1. 使用畫面頂端的下拉式方塊，依據訂用帳戶或資源群組進行篩選。


![列出所有解決方案](media/solutions/list-solutions-all.png)

按一下解決方案名稱，以開啟其摘要頁面。 此頁面會顯示解決方案中包含的任何檢視，並為解決方案本身及其工作區提供不同的選項。 使用以上其中一個程序來檢視解決方案的摘要頁面，以列出解決方案，然後按一下解決方案的名稱。

![解決方案屬性](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>安裝監視解決方案
您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com) 取得來自 Microsoft 和夥伴的管理解決方案。 您可以使用下列程序來搜尋可用的解決方案並安裝它們。 當您安裝解決方案時，您必須選取將在其中安裝解決方案且將收集其資料的 [Log Analytics 工作區](../platform/manage-access.md)。

1. 從[訂用帳戶的解決方案清單](#list-installed-monitoring-solutions)，按一下 [新增]。
1. 流覽或搜尋解決方案。 您也可以從[這個搜尋連結](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)流覽解決方案。
1. 找出您想要的監視解決方案，然後完整閱讀它的描述。
1. 按一下 [建立] 以啟動安裝程序。
1. 當安裝程式開始時，系統會提示您指定 Log Analytics 工作區，並提供解決方案的任何必要設定。

![安裝解決方案](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>從社群安裝解決方案
社群成員可以將管理解決方案提交至 Azure 快速入門範本。 您可以直接安裝這些解決方案，或下載其範本以便稍後安裝。

1. 依照 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)中所述的程序來連結工作區和帳戶。
2. 移至 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。 
3. 搜尋您感興趣的解決方案。
4. 選取結果中的解決方案，以檢視其詳細資料。
5. 按一下 [部署至 Azure] 按鈕。
6. 除了解決方案中任何參數的值以外，系統還會提示您提供資源群組和位置等資訊。
7. 按一下 [購買] 以安裝解決方案。


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作區和自動化帳戶
所有監視解決方案都需要有 [Log Analytics 工作區](../platform/manage-access.md)，才能儲存解決方案所收集的資料以及裝載其記錄搜尋和檢視。 有些解決方案也需要有 [OMS 工作區](../../automation/automation-security-overview.md#automation-account-overview)來容納 Runbook 和相關資源。 工作區和帳戶必須符合下列需求。

* 每次安裝解決方案時，只能使用一個 Log Analytics 工作區和一個自動化帳戶。 您可以將解決方案個別安裝於多個工作區中。
* 如果解決方案需要自動化帳戶，則 Log Analytics 工作區和自動化帳戶必須彼此連結。 Log Analytics 工作區只能連結到一個自動化帳戶，而自動化帳戶只能連結到一個 Log Analytics 工作區。
* 若要連結，Log Analytics 工作區和自動化帳戶必須位於相同的資源群組與區域中。 例外狀況是位於美國東部區域的工作區，以及位於美國東部 2 的自動化帳戶。

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>建立 Log Analytics 工作區與自動化帳戶之間的連結
指定 Log Analytics 工作區和自動化帳戶的方式，取決於解決方案的安裝方法。

* 當您透過 Azure Marketplace 安裝解決方案時，系統會提示您提供工作區和自動化帳戶。 如果它們尚未連結，則會建立它們之間的連結。
* 對於 Azure Marketplace 外部的解決方案，您必須在安裝解決方案之前，先連結 Log Analytics 工作區和自動化帳戶。 若要這麼做，請選取 Azure Marketplace 中的任何解決方案，並選取 Log Analytics 工作區和自動化帳戶。 您不必實際安裝解決方案，因為在選取 Log Analytics 工作區和自動化帳戶時，就會立即建立連結。 一旦建立連結之後，您即可將該 Log Analytics 工作區和自動化帳戶用於任何解決方案。

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>驗證 Log Analytics 工作區與自動化帳戶之間的連結
您可以使用下列程序，驗證 Log Analytics 工作區與自動化帳戶之間的連結。

1. 在 Azure 入口網站中選取自動化帳戶。
1. 捲動到功能表的 [相關資源] 區段。
1. 如果已啟用 [工作區] 設定，則會將此帳戶連結到 Log Analytics 工作區。 您可以按一下 [工作區] 來檢視工作區的詳細資料。

## <a name="remove-a-monitoring-solution"></a>移除監視解決方案
若要移除已安裝的解決方案，請在[已安裝的解決方案清單](#list-installed-monitoring-solutions)中找到它。 按一下解決方案名稱，以開啟其摘要頁面，然後按一下 [刪除]。


## <a name="next-steps"></a>後續步驟
* 取得[來自 Microsoft 的監視解決方案清單](solutions-inventory.md)。
* 了解如何[建立查詢](../log-query/log-query-overview.md)，以分析監視解決方案所收集的資料。

