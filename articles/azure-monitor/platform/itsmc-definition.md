---
title: Log Analytics 中的 IT 服務管理連接器
description: 本文提供 IT 服務管理連接器 (ITSMC) 的總覽，以及如何使用它來監視和管理 Log Analytics 中的 ITSM 工作專案並快速解決問題的相關資訊。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 20c59e5ecc24dfe5c9eadb05899bf37d39ce09e7
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882278"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>使用 IT 服務管理連接器將 Azure 連接至 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

本文提供如何在 Log Analytics 中設定 IT 服務管理連接器 (ITSMC) 以集中管理工作專案的相關資訊。

## <a name="add-it-service-management-connector"></a>新增 IT 服務管理連接器

您必須先新增 ITSMC，才能建立連接。

1. 在 [Azure 入口網站中，選取 [ **建立資源**：

   ![顯示 [建立資源] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/azure-add-new-resource.png)

2. 搜尋 Azure Marketplace 中的 **IT 服務管理連接器** 。 選取 [ **建立**]：

   ![顯示 Azure Marketplace 中 [建立] 按鈕的螢幕擷取畫面。](media/itsmc-overview/add-itsmc-solution.png)

3. 在 [ **LA 工作區** ] 區段中，選取您要安裝 ITSMC 的 Azure Log Analytics 工作區。
   >[!NOTE]
   >
   > * ITSMC 只能安裝在下欄區域的 Log Analytics 工作區中：美國東部、美國西部2、美國中南部、美國中西部、US Gov 亞利桑那州、US Gov 維吉尼亞州、加拿大中部、西歐、英國、東南亞、日本東部、印度中部和澳大利亞東南部。

4. 在 [ **Log Analytics 工作區** ] 區段中，選取您要在其中建立 ITSMC 資源的資源群組：

   ![顯示 Log Analytics 工作區區段的螢幕擷取畫面。](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >在從 Microsoft Operations Management Suite (OMS) 到 Azure 監視器的持續轉換過程中，OMS 工作區現在稱為 *Log Analytics 工作區*。

5. 選取 [確定]。

部署 ITSMC 資源時，視窗右上角會出現通知。

## <a name="create-an-itsm-connection"></a>建立 ITSM 連線

安裝 ITSMC 之後，您可以建立連接。

若要建立連線，您必須準備 ITSM 工具以允許來自 ITSMC 的連接。  

根據您要連線的 ITSM 產品，選取下列其中一個連結以取得指示：

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

備妥您的 ITSM 工具之後，請完成下列步驟來建立連接：

1. 在 [ **所有資源**] 中，尋找 **>servicedesk (*您的工作區名稱*)**：

   ![顯示 Azure 入口網站中最近資源的螢幕擷取畫面。](media/itsmc-overview/itsm-connections.png)

1. 在左窗格的 [ **工作區資料來源** ] 底下，選取 [ **ITSM 連接**：

   ![顯示 [ITSM 連接] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/add-new-itsm-connection.png)
1. 選取 [ **新增連接**]。

1. 依照 ITSM 產品/服務的說明，指定連線設定：

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > 依預設，ITSMC 會每隔24小時重新整理一次連線的設定資料。 若要立即重新整理連線的資料以反映您所做的任何編輯或範本更新，請在連線的分頁上選取 [ **同步** 處理] 按鈕：
   >
   > ![顯示連接分頁上 [同步處理] 按鈕的螢幕擷取畫面。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>使用 ITSMC

   您可以使用 ITSMC，從 Azure 監視器警示建立警示到 ITSM 工具。

## <a name="create-itsm-work-items-from-azure-alerts"></a>從 Azure 警示建立 ITSM 工作項目

建立 ITSM 連線之後，您可以在 ITSM 工具中根據 Azure 警示建立工作專案。 若要建立工作專案，您將使用動作群組中的 ITSM 動作。

動作群組提供模組化且可重複使用的方式來觸發 Azure 警示的動作。 您可以在 Azure 入口網站中使用具有計量警示、活動記錄警示和 Azure Log Analytics 警示的動作群組。

> [!NOTE]
> 建立 ITSM 連線之後，您需要等候30分鐘，同步處理常式才會完成。

### <a name="template-definitions"></a>範本定義

   有工作專案類型可以使用 ITSM 工具所定義的範本。
藉由使用範本，您可以定義欄位，這些欄位會根據定義為動作群組一部分的固定值來自動填入。 您可以在 ITSM 工具中定義範本。
您可以定義您想要在動作群組定義中使用的範本。

使用下列程式建立動作群組：

1. 在 [Azure 入口網站中，選取 [  **警示**]。
2. 在畫面頂端的功能表中，選取 [ **管理動作**：

    ![顯示 [管理動作] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/action-groups-selection-big.png)

   [ **建立動作群組** ] 視窗隨即出現。

3. 選取您要在其中建立動作群組的 **訂** 用帳戶和 **資源群組** 。 提供動作群組的 **動作組名** 和 **顯示名稱** 。 選取 **[下一步：通知]**。

    ![顯示 [建立動作群組] 視窗的螢幕擷取畫面。](media/itsmc-overview/action-groups-details.png)

4. 在通知清單中選取 **[下一步：動作]**。
5. 在 [動作] 清單中，選取 [**動作類型**] 清單中的 [ **ITSM** ]。 提供動作的 **名稱** 。 選取代表 **編輯詳細資料** 的畫筆按鈕。

    ![顯示動作群組定義的螢幕擷取畫面。](media/itsmc-definition/action-group-pen.png)

6. 在 **訂** 用帳戶清單中，選取您的 Log Analytics 工作區所在的訂用帳戶。 在 [ **連接** ] 清單中，選取您的 ITSM 連接器名稱。 它後面會接著您的工作區名稱。 例如，MyITSMConnector (MyWorkspace) 。

7. 選取 **工作專案** 類型。

8. 如果您想要以固定值填滿現成的欄位，請選取 [ **使用自訂範本**]。 否則，請在 **範本** 清單中選擇現有的 [範本](#template-definitions)，然後在範本欄位中輸入固定值。

9. 如果您 **針對每個設定專案選取 [建立個別的工作專案**]，則每個設定專案都會有自己的工作專案。 也就是說，每個設定專案都會有一個工作專案。

    * 如果您在工作專案下拉式清單中選取 [事件] 或 [警示]： 
        * 如果您勾選 [ **為每個設定專案建立個別的工作專案** ] 核取方塊，則每個警示都會建立新的工作專案。 ITSM 系統中的每個設定專案都可以有一個以上的工作專案。

            例如：
            1) 具有3個設定專案的警示1： A、B、C 會建立3個工作專案。
            2) 具有1個設定專案的警示2： D 將會建立1個工作專案。

                **此流程結束時，會有4個警示**
        * 如果您清除 [ **為每個設定專案建立個別的工作專案** ] 核取方塊，則會出現警示，將不會建立新的工作專案。 工作專案將會根據警示規則合併。

            例如：
            1) 具有3個設定專案的警示1： A、B、C 會建立1個工作專案。
            2) 相同警示規則的警示2與1個設定專案的階段1相同： D 將會合並到階段1中的工作專案。
            3) 具有1個設定專案之不同警示規則的警示3： E 將會建立1個工作專案。

                **此流程結束時，會有2個警示**

       ![顯示 [ITSM 事件] 視窗的螢幕擷取畫面。](media/itsmc-overview/itsm-action-configuration.png)

    * 如果您在工作專案下拉式清單中選取 [事件]：如果您針對選項按鈕選取範圍中的 **每個記錄專案選取 [建立個別的工作專案** ]，則每個警示都會建立新的工作專案。 如果您針對選項按鈕選取範圍中的 **每個設定專案選取 [建立個別工作專案** ]，則每個設定專案都會有自己的工作專案。
   ![顯示 [ITSM 事件] 視窗的螢幕擷取畫面。](media/itsmc-overview/itsm-action-configuration-event.png)

10. 選取 [確定]。

當您建立或編輯 Azure 警示規則時，請使用具有 ITSM 動作的動作群組。 當警示觸發時，會在 ITSM 工具中建立或更新工作專案。

> [!NOTE]
>
>- 如需 ITSM 動作價格的詳細資訊，請參閱動作群組的 [定價頁面](https://azure.microsoft.com/pricing/details/monitor/) 。
>
>
>- 當您使用 [ITSM] 動作傳送警示規則時，警示規則定義中的 [簡短描述] 欄位限制為40個字元。

## <a name="next-steps"></a>後續步驟

* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)
