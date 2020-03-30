---
title: 使用 Azure 監視器創建、查看和管理指標警報
description: 瞭解如何使用 Azure 門戶或 CLI 創建、查看和管理指標警報規則。
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369381"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>使用 Azure 監視器建立、檢視及管理計量警示

Azure 監視器中的指標警報提供了一種在其中一個指標超過閾值時收到通知的方法。 計量警示可在一系列的多維度平台計量、自訂計量、Application Insights 標準和自訂計量上運作。 在本文中，我們將介紹如何通過 Azure 門戶和 Azure CLI 創建、查看和管理指標警報規則。 您還可以使用 Azure 資源管理器範本創建指標警報規則，這些範本在[單獨的文章中](alerts-metric-create-templates.md)介紹。

您可以從[計量警示概觀](alerts-metric-overview.md)深入了解計量警示工作的運作方式。

## <a name="create-with-azure-portal"></a>使用 Azure 入口網站建立

下列程序說明如何在 Azure 入口網站建立計量警示規則：

1. 在[Azure 門戶](https://portal.azure.com)中，按一下**監視器**。 [監視] 刀鋒視窗會將所有監視設定與資料合併在一個檢視中。

2. 按一下 [警示]****，然後按一下 [+ 新增警示規則]****。

    > [!TIP]
    > 大部分的資源刀鋒視窗也都有 [警示]**** (位於 [監視]**** 下的資源功能表)，您也可以從該處建立警示。

3. 在載入的內容窗格中，按一下 [選取目標]****，選取您要發出警示的目標資源。 使用 [訂用帳戶]**** 與 [資源類型]**** 下拉式清單尋找您要監視的資源。 您也可以使用搜尋列來尋找您的資源。

4. 如果選取的資源有可對其建立警示的計量，位於右下方的 [可用訊號]**** 會包含計量。 您可以檢視此[文章](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)中的計量警示所支援資源類型的完整清單。

5. 選取目標資源之後，請按一下 [新增準則]****。

6. 您會看到該資源支援的訊號清單，請選取您要對其建立警示的計量。

7. 您將看到過去六小時的指標圖表。 使用**圖表期間**下拉清單可選擇查看指標的較長歷史記錄。

8. 如果指標具有維度，您將看到一個維度表。 為每個維度選取一或多個值。
    - 顯示的維度值基於過去三天的指標資料。
    - 如果未顯示要查找的維度值，請按一下"+"以添加自訂值。
    - **Select \*** 也可以讓您選取任何維度。 **Select \*** 會將選擇動態擴充至維度的所有目前與未來值。

    指標警報規則將評估所選值組合的條件。 [深入了解對多維度計量發出警示的運作方式](alerts-metric-overview.md)。

9. 選擇**閾值**類型、**運算子**和**聚合類型**。 這將確定指標警報規則將評估的邏輯。
    - 如果使用**靜態**閾值，請繼續定義**閾值**。 指標圖表可説明確定什麼是合理的閾值。
    - 如果使用**動態**閾值，請繼續定義**閾值敏感度**。 計量圖表會顯示以最近資料為基礎的計算臨界值。 [深入了解動態閾值條件類型和敏感度選項](alerts-dynamic-thresholds.md)。

10. 可以選擇通過調整**聚合細微性**和**計算頻率**來優化條件。 

11. 按一下 [完成]****。

12. (選擇性) 如果您想要監視複雜的警示規則，請新增另一個準則。 目前使用者可以使用具有動態閾值準則的警示規則作為單一準則。

13. 填寫**警報詳細資訊**，如**警報規則名稱**、**描述**和**嚴重性**。

14. 透過選取現有動作群組或建立新的動作群組，將動作群組新增至警示。

15. 按一下 [完成]**** 以儲存計量警示規則。

> [!NOTE]
> 透過入口網站建立的計量警示規則，會在與目標資源相同的資源群組中建立。

## <a name="view-and-manage-with-azure-portal"></a>使用 Azure 入口網站檢視及管理

您可以使用 [警示] 下的 [管理規則] 刀鋒視窗來檢視及管理計量警示規則。 下面的程序示範如何檢視您的計量警示規則及進行編輯。

1. 在 Azure 入口網站中，瀏覽到 [監視]****

2. 按一下 [警示]**** 與 [管理規則]****

3. 在 [管理規則]**** 刀鋒視窗中，您可以跨訂用帳戶檢視您的所有警示規則。 您可以使用**資源組**、**資源類型****和資源進**一步篩選規則。 如果您只想要查看計量警示，請選取 [訊號類型]**** 作為計量。

    > [!TIP]
    > 在 [管理規則]**** 刀鋒視窗中，您可以選取多個警示規則，並加以啟用/停用。 這在某些目標資源必須進行維護時可能有幫助

4. 按一下要編輯之計量警示規則的名稱

5. 在 [編輯規則] 中，按一下要編輯的 [警示準則]****。 您可以視需要變更計量、閾值條件與其他欄位

    > [!NOTE]
    > 建立計量警示之後，就無法編輯 [目標資源]**** 與 [警示規則名稱]****。

6. 按一下 [完成]**** 以儲存您的編輯。

## <a name="with-azure-cli"></a>透過 Azure CLI

前面的各節介紹了如何使用 Azure 門戶創建、查看和管理指標警報規則。 此節說明如何使用跨平台 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 執行相同的作業。 開始使用 Azure CLI 最快的方式就是透過 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)。 在本文中，我們將使用雲外殼。

1. 轉到 Azure 門戶，按一下**雲外殼**。

2. 在出現提示時，您可以搭配 ``--help`` 選項來使用命令，以深入了解該命令與其使用方式。 例如，以下命令顯示可用於創建、查看和管理指標警報的命令清單

    ```azurecli
    az monitor metrics alert --help
    ```

3. 您可以建立簡單的計量警示規則，監視 VM 上的平均 CPU 百分比是否大於 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. 您可以使用下列命令來檢視資源群組中的所有計量警示

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. 您可以使用規則的名稱或資源識別碼，來查看特定計量警示規則的詳細資料。

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. 您可以使用下列命令來停用計量警示規則。

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. 您可以使用下列命令來刪除計量警示規則。

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure Resource Manager 範本建立計量警示](../../azure-monitor/platform/alerts-metric-create-templates.md)。
- [了解計量警示的運作方式](alerts-metric-overview.md)。
- [了解計量警示如何搭配動態閾值條件運作](alerts-dynamic-thresholds.md)。
- [了解計量警示的 Webhook 結構描述](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

