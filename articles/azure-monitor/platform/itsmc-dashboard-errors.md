---
title: 常見錯誤
description: 本檔包含存在於儀表板中的常見錯誤相關資訊
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 7240c1b0f19dc49ab4130c5ee2516dcfefb2e2c2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602198"
---
# <a name="errors-in-the-connector-status"></a>連接器狀態中的錯誤

在 [連接器狀態] 清單中，您可以找到可協助您修正 ITSM 連接器問題的錯誤。

## <a name="status-common-errors"></a>狀態常見錯誤

在本節中，您可以找到 [連接器狀態] 區段中顯示的常見錯誤，以及您應該如何解決此問題：

* **錯誤**：「來自 ServiceNow 的非預期回應，以及成功狀態碼。 回應： {"import_set"： "{import_set_id}"，"staging_table"： "x_mioms_microsoft_oms_incident"，"result"： [{"transform_map"： "OMS Incident"，"table"： "incident"，"status"： "error"，"error_message"： "{找不到目標記錄 |不正確資料表 |臨時表 "}" 無效

    **原因**：當下列情況時，ServiceNow 會傳回這類錯誤：
  * 在 ServiceNow 實例中部署的自訂腳本會導致事件被忽略。
  * 「OMS 整合器應用程式」程式碼本身是在 ServiceNow 端修改的，例如 onBefore 腳本。

    **解決方法**：停用資料匯入路徑的所有自訂腳本或程式碼修改。

* **錯誤**： "{" Error "： {" message "：" Operation Failed "，" detail "：" ACL 例外狀況更新失敗，因為安全性條件約束 "}"

    **原因**： ServiceNow 許可權設定錯誤

    **解決** 方式：檢查是否已依 [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)正確指派所有角色。

* **錯誤**：「傳送要求時發生錯誤」。

    **原因**：「ServiceNow 實例無法使用」

    **解決** 方式：檢查 ServiceNow 中的實例可能已刪除或無法使用。

* **錯誤**： "ServiceDeskHttpBadRequestException： StatusCode = 429"

    **原因**： ServiceNow 速率限制太高/低。

    **解決** 方式：增加或取消 ServiceNow 實例中的速率限制，[如下所述。](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)

* **錯誤**：「AccessToken 和 RefreshToken 無效。 使用者必須重新驗證。」

    **原因**：重新整理權杖已過期。

    **解決** 方式：同步處理 ITSM 連接器以產生新的重新整理權杖 [，如下所述。](./itsmc-resync-servicenow.md)

* **錯誤**：「無法建立/更新警示 {alertName} 的工作專案。 ITSM 連接器 {connectionIdentifier} 不存在或已刪除。」

    **原因**：已刪除 ITSM 連接器。

    **解決** 方式： ITSM 連接器已刪除，但仍有定義與其相關聯的 ITSM 動作群組。 有2個選項可解決此問題：
  * 尋找並停用或刪除這類動作
  * [重新設定動作群組](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) ，以使用現有的 ITSM 連接器。
  * [建立新的 ITSM 連接器](./itsmc-definition.md#create-an-itsm-connection) ，並 [重新設定動作群組以使用它](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="ui-common-errors"></a>UI 常見錯誤

* **錯誤**：「發生錯誤。 無法取得連接詳細資料。」 當客戶定義 ITSM 動作群組時，就會出現此錯誤。

    **原因**：新建立的 ITSM 連接器尚未完成初始同步處理。

    **解決方案**：建立新的 ITSM 連接器時，ITSM 連接器會開始同步處理來自 ITSM 系統的資訊，例如工作專案範本和工作專案。 同步 ITSM 連接器以產生新的重新整理權杖[，如下所述。](./itsmc-resync-servicenow.md)
