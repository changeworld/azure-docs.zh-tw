---
title: 針對 ADF 中的 CI-CD、Azure DevOps 和 GitHub 問題進行疑難排解
description: 使用不同的方法來針對 ADF 中的 CI CD 問題進行疑難排解。
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 11/26/2020
ms.openlocfilehash: f07cc8e3d5e9d6f59671a3c8c2efd9f5fb9f27b7
ms.sourcegitcommit: 236014c3274b31f03e5fcee5de510f9cacdc27a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96299051"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>針對 ADF 中的 CI-CD、Azure DevOps 和 GitHub 問題進行疑難排解 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探討 Azure Data Factory 中 CI-CD、Azure DevOps 和 GitHub 問題的常見疑難排解方法。

如果您在使用原始檔控制或 DevOps 技術方面有任何疑問或問題，以下是您可能會覺得有用的一些文章：

- 請參閱 [adf 中的原始檔控制](source-control.md) ，以瞭解如何在 adf 中練習原始檔控制。 
- 請參閱  [adf 中的 CI cd](continuous-integration-deployment.md) ，以深入瞭解如何在 adf 中練習 DEVOPS ci cd。
 
## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>因為不同的租使用者，連接到 Git 存放庫失敗

#### <a name="issue"></a>問題
    
有時您會遇到驗證問題，例如 HTTP 狀態401。 尤其是當您有多個具有來賓帳戶的租使用者時，可能會變得更複雜。

#### <a name="cause"></a>原因

我們觀察到的是，權杖是從原始的租使用者取得，但 ADF 是在來賓租使用者中，而且嘗試使用權杖來造訪來賓租使用者中的 DevOps。 這不是預期的行為。

#### <a name="recommendation"></a>建議

您應改為使用從來賓租使用者發出的權杖。 例如，您必須將相同的 Azure Active Directory 指派為您的來賓租使用者及 DevOps，讓它可以正確地設定權杖行為，並使用正確的租使用者。

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>參數檔案中的範本參數無效

#### <a name="issue"></a>問題

如果我們刪除 Dev 分支中的觸發程式（已在具有 **相同** 設定的測試或生產分支中使用 (例如 frequency 和 interval) ），則發行管線部署會成功，並在個別環境中刪除對應的觸發程式。 但是，如果您有 **不同** 的設定 (例如在測試/生產環境中觸發程式的頻率和間隔) ，而且如果您在開發環境中刪除相同的觸發程式，則部署會失敗併發生錯誤。

#### <a name="cause"></a>原因

CI/CD 管線失敗，並出現下列錯誤：

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>建議

發生此錯誤的原因是，我們經常刪除已參數化的觸發程式，因此 (的 ARM 範本中將無法使用參數，因為該觸發程式已不存在) 。 因為參數不再位於 ARM 範本中，所以我們必須更新 DevOps 管線中的覆寫參數。 否則，每次 ARM 範本中的參數變更時，都必須在部署工作) 的 DevOps 管線 (中更新覆寫的參數。

### <a name="updating-property-type-is-not-supported"></a>不支援更新屬性類型

#### <a name="issue"></a>問題

CI/CD 發行管線因下列錯誤而失敗：

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>原因

這是因為目標 factory 中的 Integration Runtime 具有相同的名稱，但類型不同。 部署時，Integration Runtime 必須是相同的類型。

#### <a name="recommendation"></a>建議

- 請參閱以下 CI/CD 的最佳作法：

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- 整合執行時間不會經常變更，而且在 CI/CD 的所有階段中都很類似，因此 Data Factory 預期您在 CI/CD 的所有階段都有相同的名稱和類型的整合執行時間。 如果 [名稱] 和 [類型] & 屬性不同，請務必符合來源和目標 IR 設定，然後部署發行管線。
- 如果您想要在所有階段中共用整合執行階段，請考慮使用三元處理站，只包含共用的整合執行階段。 您可以在所有環境中，使用此共用處理站作為連結的整合執行階段類型。

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>檔建立或更新失敗，因為參考無效

#### <a name="issue"></a>問題

嘗試將變更發佈至 Data Factory 時，您會收到下列錯誤訊息：

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>徵狀

您已卸離 Git 設定，並使用選取的 [匯入資源] 旗標再次設定，這會將 Data Factory 設定為「同步」。 這表示沒有要發行的變更。

**解決方法**

卸離 Git 設定並再次設定，並確定不要勾選 [匯入現有資源] 核取方塊。

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory 將失敗從某個資源群組移至另一個資源群組

#### <a name="issue"></a>問題

您無法將 Data Factory 從一個資源群組移至另一個資源群組，因為發生下列錯誤而失敗：

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>解決方法

您必須刪除 SSIS IR 和共用的 IRs，才能允許移動作業。 如果您不想要刪除 IRs，最好的方式是遵循複製和複製檔來進行複製，完成之後，請刪除舊的 Data factory。

###  <a name="unable-to-export-and-import-arm-template"></a>無法匯出和匯入 ARM 範本

#### <a name="issue"></a>問題

無法匯出和匯入 ARM 範本。 入口網站上未發生錯誤，但在瀏覽器追蹤中，您可能會看到下列錯誤：

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>原因

您已建立客戶角色作為使用者，而且沒有必要的許可權。 在 UI 中載入 factory 時，會檢查工廠的一系列公開控制值。 在此情況下，使用者的存取角色沒有存取 *queryFeaturesValue* API 的許可權。 若要存取此 API，全域參數功能會關閉。 ARM 匯出程式碼路徑部分依賴全域參數功能。

#### <a name="resolution"></a>解決方法

為了解決此問題，您必須將下列許可權新增至您的角色： *DataFactory/factory/queryFeaturesValue/action*。 依預設，此許可權應該包含在「Data Factory 參與者」角色中。

## <a name="next-steps"></a>下一步

如需疑難排解的詳細資訊，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
*  [Data Factory 的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)


 
