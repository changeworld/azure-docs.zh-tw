---
title: 如何在其目標資源移至不同的 Azure 區域時，更新警示規則或動作規則
description: 當其目標資源移至不同的 Azure 區域時，如何更新警示規則或動作規則的背景和指示。
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 36cf5882913426062e281194b61a8c760141512a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944154"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>如何在其目標資源移至不同的 Azure 區域時，更新警示規則或動作規則

本文說明當您在區域之間移動其他 Azure 資源時，現有的 [警示規則](./alerts-overview.md) 和 [動作規則](./alerts-action-rules.md) 可能會受到影響的原因，以及如何找出並解決這些問題。 請參閱主要的 [資源移動檔](../../azure-resource-manager/management/move-region.md) ，以取得何時當資源在有用的地方以及設計移動流程的檢查清單時的詳細資訊。

## <a name="why-the-problem-exists"></a>問題所在的原因

警示規則和動作規則會參考其他 Azure 資源。 範例包括 [Azure vm](../../site-recovery/azure-to-azure-tutorial-migrate.md)、 [azure SQL](../../azure-sql/database/move-resources-across-regions.md)及 [Azure 儲存體](../../storage/common/storage-account-move.md)。 當您移動這些規則所參考的資源時，這些規則可能會因為找不到它們所參考的資源而無法正常地停止運作。

在移動目標資源之後，您的規則可能會停止運作的主要原因有兩個：

- 規則的範圍會明確參考舊的資源。
- 您的警示規則是以計量為基礎。

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>規則範圍明確參考舊資源

當您移動資源時，其資源識別碼會在大部分情況下變更。 在幕後，系統會先將資源複寫到新的區域，再從舊區域刪除資源。 此程式需要這兩個資源，因此有兩個不同的資源識別碼可同時存在一小段時間內。 由於資源識別碼必須是唯一的，因此必須在處理期間建立新的識別碼。 

**移動資源對現有規則有何影響？**

警示規則和動作規則具有適用的資源範圍。 範圍可以是整個訂用帳戶、資源群組，或一或多個特定的資源。
例如，以下是具有兩個資源的範圍 (兩個虛擬機器) 的規則：

![多重資源警示規則](media/alerts-resource-move/multi-resource-alert-rule.png)

如果規則範圍明確提及資源，且該資源已移動並變更其資源識別碼，則該規則會尋找錯誤或不存在的資源，因而失敗。

**如何修正此問題？**

更新或重新建立受影響的規則，以指向新的資源。 更新範圍的程式稍後會在本文中找到。

問題適用于這些規則類型：

- 活動記錄警示規則
- 動作規則
- 傳統警示
- 計量警示–如需詳細資訊，請參閱下一節以 [計量為基礎的警示規則](#alert-rules-based-on-metrics)。

> [!NOTE]
> 記錄搜尋警示規則和智慧型偵測器警示規則不會受到影響，因為其範圍可能是工作區或 Application Insights。 這兩個範圍目前都不支援區域移動。

## <a name="alert-rules-based-on-metrics"></a>以計量為基礎的警示規則

Azure 資源發出的計量為區域。 每當資源移至新區域時，就會開始在該新區域中發出其計量。 因此，任何以計量為基礎的警示規則都需要更新或重新建立，使其指向正確區域中目前的計量串流。

這項說明適用于 [度量警示規則](alerts-metric-overview.md) 和 [可用性測試警示規則](../app/monitor-web-app-availability.md)。

如果範圍中的 **所有** 資源都已移動，您就不需要重新建立規則。 您可以直接更新警示規則的任何欄位，例如警示規則描述，並加以儲存。
如果範圍中 **只有部分** 資源已移動，您必須從現有的規則中移除已移動的資源，並建立僅涵蓋移動資源的新規則。

## <a name="procedures-to-fix-problems"></a>修正問題的程式

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>從 Azure 入口網站識別與已移動資源相關聯的規則

- **針對警示規則** -流覽至 [警示] > 管理警示規則] > 依包含的訂用帳戶和移動的資源進行篩選。
> [!NOTE]
> 活動記錄警示規則不支援此進程。 您無法更新活動記錄警示規則的範圍，並將其指向另一個訂用帳戶中的資源。 相反地，您可以建立新的規則來取代舊的規則。

- **針對動作規則** -流覽至警示 > 管理動作 > 動作規則 (預覽) > 依包含的訂用帳戶和移動的資源進行篩選。

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>從 Azure 入口網站變更規則的範圍

1. 開啟您在上一個步驟中識別的規則，方法是按一下該規則。
2. 在 [ **資源**] 底下，按一下 [ **編輯** ]，然後視需要調整範圍。
3. 視需要調整規則的其他屬性。
4. 按一下 [儲存]。

![變更警示規則範圍](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本變更規則的範圍

1. 取得規則的 Azure Resource Manager 範本。  若要從 Azure 入口網站匯出規則的範本：
   1. 流覽至入口網站中的 [資源群組] 區段，然後開啟包含規則的資源群組。
   2. 在 [總覽] 區段中，選取 [ **顯示隱藏的類型** ] 核取方塊，然後依規則的相關類型進行篩選。
   3. 選取相關的規則以查看其詳細資料。
   4. 在 [ **設定**] 底下，選取 [ **匯出範本**]。
2. 修改範本。 如有需要，請分割成兩個規則 (與計量警示的某些案例相關，如上述) 所述。
3. 重新部署範本。

### <a name="change-scope-of-a-rule-using-rest-api"></a>使用 REST API 變更規則的範圍

1. 取得現有規則 (計量 [警示](/rest/api/monitor/metricalerts/get)、 [活動記錄警示](/rest/api/monitor/activitylogalerts/get)) 
2. 修改 [活動記錄警示](/rest/api/monitor/activitylogalerts/update) 的範圍 () 
3. 重新部署規則 (計量 [警示](/rest/api/monitor/metricalerts/createorupdate)、 [活動記錄警示](/rest/api/monitor/activitylogalerts/createorupdate)) 

### <a name="change-scope-of-a-rule-using-powershell"></a>使用 PowerShell 變更規則的範圍

1. 取得 (計量 [警示](/powershell/module/az.monitor/get-azmetricalertrulev2)、 [活動記錄警示](/powershell/module/az.monitor/get-azactivitylogalert)、 [動作規則](/powershell/module/az.alertsmanagement/get-azactionrule)) 的現有規則。
2. 修改範圍。 如有需要，請分割成兩個規則 (與計量警示的某些案例相關，如上述) 所述。
3. 重新部署規則 (計量 [警示](/powershell/module/az.monitor/add-azmetricalertrulev2)、 [活動記錄警示](/powershell/module/az.monitor/enable-azactivitylogalert)、 [動作規則](/powershell/module/az.alertsmanagement/set-azactionrule)) 。

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>使用 Azure CLI 變更規則的範圍

1.  取得現有規則 (計量 [警示](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show)、 [活動記錄警示](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)) 。
2.  直接更新規則範圍 (計量 [警示](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update)、 [活動記錄警示](/cli/azure/monitor/activity-log/alert/scope)) 
3.  如有需要，請分割成兩個規則 (與計量警示的某些案例相關，如上述) 所述。

## <a name="next-steps"></a>後續步驟

瞭解如何修正 [警示通知](alerts-troubleshoot.md)、計量 [警示](alerts-troubleshoot-metric.md)和 [記錄警示](alerts-troubleshoot-log.md)的其他問題。 
