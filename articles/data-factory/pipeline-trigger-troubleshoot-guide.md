---
title: 針對 Azure Data Factory 中的管線協調流程和觸發程式進行疑難排解
description: 使用不同的方法來針對 Azure Data Factory 中的管線觸發程式問題進行疑難排解。
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0ceee3c65e8c4df5d843bb441fb6426a0f4eb696
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220247"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>針對 Azure Data Factory 中的管線協調流程和觸發程式進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure Data Factory 中，「管線執行」可定義管線執行的執行個體。 例如，假設您有一個在上午8:00 點、9:00 AM 和 10:00 AM 執行的管線。 在此情況下，有三個不同的管線執行。 每個管線執行都有唯一的管線執行識別碼。 執行識別碼是 (GUID) 的全域唯一識別碼，可定義該特定管線執行。

將引數傳遞給管線中定義的參數，通常可將管線執行具現化。 您可以手動方式或使用觸發程式來執行管線。 如需詳細資料，請參閱 [Azure Data Factory 中的管線執行和觸發](concepts-pipeline-execution-triggers.md) 程式。

## <a name="common-issues-causes-and-solutions"></a>常見的問題、原因和解決方案

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Functions 的應用程式管線擲回私人端點連線錯誤
 
您有 Data Factory 和在私人端點上執行的 Azure 函數應用程式。 您正在嘗試執行與函數應用程式互動的管線。 您已嘗試三種不同的方法，但其中一個會傳回錯誤「不正確的要求」，而其他兩個方法會傳回「103錯誤禁止」。

**原因**： Data Factory 目前不支援函數應用程式的私人端點連接器。 Azure Functions 拒絕呼叫，因為它已設定為只允許來自私用連結的連接。

**解決方案**：建立 **PrivateLinkService** 端點並提供函數應用程式的 DNS。

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>管線執行已取消，但監視器仍顯示進度狀態

當您取消管線執行時，管線監視通常仍會顯示進度狀態。 因為瀏覽器快取問題，所以會發生這種情況。 您也可能沒有正確的監視篩選。

**解決** 方式：重新整理瀏覽器並套用正確的監視篩選。
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>複製管線時看到「DelimitedTextMoreColumnsThanDefined」錯誤
 
如果您要複製的資料夾包含具有不同架構的檔案，例如變數的資料行數目、不同的分隔符號、引號字元設定或某些資料問題，Data Factory 管線可能會擲回此錯誤：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**解決** 方式：建立複製活動時，選取 **二進位複製** 選項。 如此一來，您就可以將資料從一個 data lake 大量複製或遷移到另一個 data lake，Data Factory 不會開啟檔案來讀取架構。 相反地，Data Factory 會將每個檔案視為二進位檔，並將它複製到另一個位置。

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime"></a>當您達到整合執行時間的容量限制時，管線執行失敗

錯誤訊息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**原因**：您已達到整合執行時間的容量限制。 您可能會同時使用相同的整合執行時間來執行大量的資料流程。 如需詳細資料，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) 。

**解決方案**：
 
- 在不同的觸發時間執行您的管線。
- 建立新的整合執行時間，並將您的管線分割到多個整合執行時間。

### <a name="you-have-activity-level-errors-and-failures-in-pipelines"></a>您在管線中有活動層級的錯誤和失敗

Azure Data Factory 協調流程允許條件式邏輯，並可讓使用者根據上一個活動的結果來採用不同的路徑。 它允許四個條件式路徑： **成功時** (預設傳遞) 、在 **失敗** 時、 **完成** 時以及 **略過時**。 

Azure Data Factory 會評估所有分葉層級活動的結果。 只有當所有的離開都成功時，管線結果才會成功。 如果略過分葉活動，我們會改為評估其父活動。 

**解決方法**

1. 遵循 [如何處理管線失敗和錯誤，以](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)執行活動層級的檢查。
1. 使用 Azure Logic Apps 以定期 [依 Factory 查詢的](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)間隔來監視管線。

## <a name="monitor-pipeline-failures-in-regular-intervals"></a>監視管線失敗的定期間隔

您可能需要依間隔（例如5分鐘）監視失敗的 Data Factory 管線。 您可以使用端點來查詢和篩選資料處理站中的管線執行。 

設定 Azure 邏輯應用程式，每5分鐘查詢一次失敗的所有管線，如 [Factory 查詢](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)中所述。 然後，您可以將附隨報告給我們的票證系統。

如需詳細資訊，請移至 [從 Data Factory 傳送通知，第2部分](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
