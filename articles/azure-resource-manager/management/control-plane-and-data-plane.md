---
title: 控制平面和資料平面作業
description: 描述控制平面和資料平面作業之間的差異。 控制平面作業是由 Azure Resource Manager 處理。 資料平面作業是由服務處理。
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: a0575c37c80417d9859ef36366dc9f26cdb4dbe4
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90038997"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure 控制平面和資料平面

Azure 作業可分成兩個類別-控制平面和資料平面。 本文說明這兩種作業類型之間的差異。

您可以使用控制平面來管理訂用帳戶中的資源。 您可以使用資料平面來使用資源類型的實例所公開的功能。

例如：

* 您可以透過控制平面來建立虛擬機器。 建立虛擬機器之後，您可以透過資料平面作業與其互動，例如遠端桌面通訊協定 (RDP) 。

* 您可以透過控制平面來建立儲存體帳戶。 您可以使用資料平面來讀取和寫入儲存體帳戶中的資料。

* 您可以透過控制平面來建立 Cosmos 資料庫。 若要查詢 Cosmos 資料庫中的資料，您可以使用資料平面。

## <a name="control-plane"></a>控制平面

控制平面作業的所有要求都會傳送至 Azure Resource Manager 的 URL。 該 URL 會因 Azure 環境而異。

* 若為 Azure global，則 URL 為 `https://management.azure.com` 。
* 若為 Azure Government，則 URL 為 `https://management.usgovcloudapi.net/` 。
* 若為 Azure 德國，則 URL 為 `https://management.microsoftazure.de/` 。
* 若為中國世紀 Microsoft Azure，則 URL 為 `https://management.chinacloudapi.cn` 。

若要探索哪些作業使用 Azure Resource Manager URL，請參閱 [Azure REST API](/rest/api/azure/)。 例如，適用于 MySql 的 [建立或更新](/rest/api/mysql/databases/createorupdate) 作業是控制平面作業，因為要求 URL 為：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager 會處理所有的控制平面要求。 它會自動套用您為了管理資源所實行的 Azure 功能，例如：

* [Azure 角色型存取控制 (RBAC) ](../../role-based-access-control/overview.md)
* [Azure 原則](../../governance/policy/overview.md)
* [管理鎖定](lock-resources.md)
* [活動記錄](view-activity-logs.md)

驗證要求之後，Azure Resource Manager 會將它傳送到資源提供者，以完成此作業。

控制平面包含處理要求的兩個案例-「綠色欄位」和「棕色欄位」。 綠色欄位指的是新的資源。 棕色欄位指的是現有的資源。 當您部署資源時，Azure Resource Manager 瞭解何時建立新的資源，以及何時更新現有的資源。 您不必擔心會建立相同的資源。

## <a name="data-plane"></a>資料平面

資料平面作業的要求會傳送至您的實例特定的端點。 例如，認知服務中的偵測 [語言](/rest/api/cognitiveservices/textanalytics/detect%20language/detect%20language) 作業是資料平面作業，因為要求 URL 為：

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

資料平面作業不限於 REST API。 他們可能需要額外的認證，例如登入虛擬機器或資料庫伺服器。

強制執行管理和治理的功能可能不會套用到資料平面作業。 您必須考慮使用者與解決方案互動的不同方式。 例如，防止使用者刪除資料庫的鎖定，無法防止使用者透過查詢刪除資料。

您可以使用某些原則來管理資料平面作業。 如需詳細資訊，請參閱 [Azure 原則中 (預覽版) 的資源提供者模式](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)。

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的總覽，請參閱 [Azure Resource Manager 是什麼？](overview.md)

* 若要深入瞭解原則定義對新資源和現有資源的影響，請參閱 [評估新 Azure 原則定義的影響](../../governance/policy/concepts/evaluate-impact.md)。
