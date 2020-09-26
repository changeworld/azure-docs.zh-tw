---
title: Azure Data Factory 中的持續整合和傳遞
description: 了解如何使用持續整合和傳遞將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: a5856d85b6a967f49fd651942ca6e4596bf15e7d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320966"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory 中的持續整合和傳遞

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概觀

持續整合是指進行相關實作，以自動並及早測試對您的程式碼基底所做的每項變更。 在持續整合期間執行測試，並將變更推送至暫存或生產系統後，就會進行持續傳遞。

在 Azure Data Factory 中，持續整合和傳遞 (CI/CD) 是指將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。 Azure Data Factory 利用 [Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)來儲存各種 ADF 實體 (管線、資料集、資料流程等等) 的設定。 有兩個建議方法可將資料處理站提升至另一個環境：

-    使用 Data Factory 與 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) 的整合進行自動化部署
-    使用 Data Factory UX 與 Azure Resource Manager 的整合，手動上傳 Resource Manager 範本。

如需這項功能的 9 分鐘簡介和示範，請觀看這段影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命週期

以下是使用 Azure Repos Git 設定的 Azure 資料處理站中 CI/CD 生命週期的範例概觀。 如需如何設定 Git 存放庫的詳細資訊，請參閱 [Azure Data Factory 中的原始檔控制](source-control.md)。

1.  使用 Azure Repos Git 建立和設定開發資料處理站。 所有開發人員都應該具有撰寫 Data Factory 資源 (例如管線和資料集) 的權限。

1.  開發人員會[建立功能分支](source-control.md#creating-feature-branches)以進行變更。 他們會使用最新的變更來對管線執行進行偵錯。 如需如何對管線執行進行偵錯的詳細資訊，請參閱[使用 Azure Data Factory 進行反覆式開發和偵錯](iterative-development-debugging.md)。

1.  如果開發人員滿意其變更，便可從其功能分支建立對主要或共同作業分支的提取要求，讓同事檢閱其變更。

1.  在提取要求經過核准並在主要分支中合併變更後，變更就會發佈到開發處理站。

1.  當小組準備好要將變更部署至測試或 UAT 時 (使用者接受度測試) factory 時，小組會進入 Azure Pipelines 版本，並將所需的開發 factory 版本部署至 UAT。 此部署會在 Azure Pipelines 工作中進行，並使用 Resource Manager 範本參數來套用適當的組態。

1.  在測試處理站中驗證變更之後，請使用管線發行的下一個工作來部署到生產處理站。

> [!NOTE]
> 只有開發處理站會與 git 存放庫相關聯。 測試和生產處理站不得有相關聯的 git 存放庫，而且只能透過 Azure DevOps 管線或透過資源管理範本進行更新。

下圖醒目提示此生命週期的不同步驟。

![透過 Azure Pipelines 進行持續整合的圖表](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure Pipelines 發行將持續整合自動化

以下是設定 Azure Pipelines 發行以將資料處理站自動部署至多個環境的指南。

### <a name="requirements"></a>需求

-   使用 [Azure Resource Manager 服務端點](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)連結至 Visual Studio Team Foundation Server 或 Azure Repos 的 Azure 訂用帳戶。

-   設定了 Azure Repos Git 整合的資料處理站。

-    [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)，其中包含每個環境的秘密。

### <a name="set-up-an-azure-pipelines-release"></a>設定 Azure Pipelines 發行

1.  在 [Azure DevOps](https://dev.azure.com/) 中，開啟使用您的資料處理站設定的專案。

1.  在頁面的左側，選取 [管線]，然後選取 [發行]。

    ![選取管線、發行](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  選取 [新增管線]，或者，如有現有的管線，請選取 [新增]，然後選取 [新增發行管線]。

1.  選取 [空白作業] 範本。

    ![選取空白作業](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 [階段名稱] 中，輸入您的環境名稱。

1.  選取 [新增成品]，然後選取使用您的開發資料處理站所設定的 git 存放庫。 針對 [預設分支] 選取存放庫的[發佈分支](source-control.md#configure-publishing-settings)。 根據預設，此發行分支為 `adf_publish`。 針對 [預設版本]，選取 [來自預設分支的最新版本]。

    ![新增構件](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  新增 Azure Resource Manager 部署工作：

    a.  在階段檢視中，選取 [檢視階段工作]。

    ![階段檢視](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  建立新的工作。 搜尋 **ARM 範本部署**，然後選取 [ **新增**]。

    c.  在部署工作中，選取目標資料處理站的訂用帳戶、資源群組和位置。 視需要提供認證。

    d.  在 [動作] 清單中，選取 [建立或更新資源群組]。

    e.  選取 [範本] 方塊旁的省略符號按鈕 ( **...** )。 瀏覽在已設定 git 存放庫的發佈分支中產生的 Azure Resource Manager 範本。 在 adf_publish 分支的 <FactoryName> 資料夾中尋找 `ARMTemplateForFactory.json` 檔案。

    f.  選取 [範本參數] 方塊旁的 **…** ，以選擇 parameters 檔案。 在 adf_publish 分支的 <FactoryName> 資料夾中尋找 `ARMTemplateParametersForFactory.json` 檔案。

    g.  選取 [覆寫範本參數] 方塊旁的 **…** ，然後輸入目標資料處理站所需的參數值。 針對來自 Azure Key Vault 的認證，請在雙引號之間輸入秘密的名稱。 例如，如果秘密的名稱是 cred1，請針對此值輸入 **"$(cred1)"** 。

    h. 針對 [部署模式] 選取 [增量]。

    > [!WARNING]
    > 在完整部署模式中，存在於資源群組中但未在新的 Resource Manager 範本中指定的資源將會被 **刪除**。 如需詳細資訊，請參閱 [Azure Resource Manager 部署模式](../azure-resource-manager/templates/deployment-modes.md)

    ![Data Factory 生產部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  儲存發行管線。

1. 若要觸發發行，請選取 [建立發行]。 若要自動建立發行，請參閱 [Azure DevOps 發行觸發程序](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![建立建立發行](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 案例中，不同環境中的整合執行階段 (IR) 類型必須相同。 例如，如果您在開發環境中有自我裝載 IR，則相同的 IR 在其他環境 (例如測試和生產環境) 中也必須屬於自我裝載類型。 同樣地，如果您要跨多個階段共用整合執行階段，則必須將所有環境中的整合執行階段設定為連結自我裝載，例如開發、測試和生產環境。

### <a name="get-secrets-from-azure-key-vault"></a>從 Azure Key Vault 取得秘密

如果您要將祕密傳遞至 Azure Resource Manager 範本，建議您使用 Azure Key Vault 搭配 Azure Pipelines 發行。

有兩種方式可處理秘密︰

1.  將祕密新增至參數檔案。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)。

    建立已上傳至發佈分支之 parameters 檔案的複本。 使用下列格式，設定您想要從 Key Vault 取得的參數值：

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    當您使用此方法時，系統會自動從金鑰保存庫提取密碼。

    參數檔案也必須位於發行分支中。

1. 在上一節中所述的 Azure Resource Manager 部署工作之前，新增 [Azure Key Vault 工作](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    1.  在 [工作] 索引標籤上，建立新的工作。 搜尋 **Azure Key Vault** 並加以新增。

    1.  在 Key Vault 工作中，選取您在其中建立金鑰保存庫的訂用帳戶。 視需要提供認證，然後選取金鑰保存庫。

    ![新增 Key Vault 工作](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>為 Azure Pipelines 代理程式授與權限

如果未設定正確的權限，Azure Key Vault 工作可能會失敗並出現「拒絕存取」錯誤。 請下載發行的記錄，並找出 .ps1 檔案，其中包含為 Azure Pipelines 代理程式賦予權限的命令。 您可以直接執行此命令。 也可以從檔案複製主體識別碼，然後在 Azure 入口網站中手動新增存取原則。 `Get`和 `List` 是所需的最低權限。

### <a name="updating-active-triggers"></a>更新作用中的觸發程序

如果您嘗試更新使用中的觸發程序，部署可能會失敗。 若要更新使用中的觸發程序，您必須手動將其停止，然後在部署後加以啟動。 您可以使用 Azure PowerShell 工作來執行此作業：

1.  在發行的 [工作] 索引標籤中，新增 [Azure Powershell] 工作。 選擇工作版本 4.*。 

1.  選取您的處理站所在的訂用帳戶。

1.  選取 [指令檔路徑] 作為指令碼類型。 這會要求您將 PowerShell 指令碼儲存在存放庫中。 下列 PowerShell 指令碼可以用來停止觸發程序：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

在部署之後，您可完成類似的步驟 (使用 `Start-AzDataFactoryV2Trigger` 函式) 以重新啟動觸發程序。

資料處理站小組已提供位於本文底部的[範例部署前後指令碼](#script)。 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>為每個環境手動提升 Resource Manager 範本

1. 在 [ARM 範本]清單中，選取 [匯出 ARM 範本]，以在開發環境中匯出資料處理站的 Resource Manager 範本。

   ![匯出 Resource Manager 範本](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在您的測試和生產資料處理站中，選取 [匯入 ARM 範本]。 此動作會將您移至 Azure 入口網站，您可以在此處匯入先前匯出的範本。 選取 [在編輯器中建立自己的範本]，以開啟 Resource Manager 範本編輯器。

   ![建立自己的範本](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 選取 [載入檔案]，然後選取所產生的 Resource Manager 範本。 這是 **arm_template.json** 檔案，其位於步驟 1 匯出的 .zip 檔案中。

   ![編輯範本](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在 [設定] 區段中，輸入設定值，像是連結服務認證。 當您完成時，請選取 [購買] 以部署 Resource Manager 範本。

   ![設定區段](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>搭配使用自訂參數與 Resource Manager 範本

如果開發處理站具有相關聯的 git 存放庫，對於經由發佈或匯出範本所產生的 Resource Manager 範本，您可覆寫其預設 Resource Manager 範本參數。 在這些情況下，您可以覆寫預設參數化範本：

* 您使用自動化 CI/CD，而且想要在 Resource Manager 部署期間變更某些屬性，但預設不會將屬性參數化。
* 您的處理站很大，以致預設 Resource Manager 範本無效，這是因為其具有的參數超過允許的上限 (256)。

若要覆寫預設參數化範本，請移至管理中樞，並在 [原始檔控制] 區段中選取 [ **參數化範本** ]。 選取 [ **編輯範本** ] 以開啟 [參數化範本程式碼編輯器]。 

![管理自訂參數](media/author-management-hub/management-hub-custom-parameters.png)

建立自訂參數化範本會在 git 分支的根資料夾中，建立名為 **arm-template-parameters-definition.js** 的檔案。 您必須使用該確切的檔案名稱。

![自訂 parameters 檔案](media/continuous-integration-deployment/custom-parameters.png)

從共同作業分支發佈時，Data Factory 會讀取這個檔案，並使用其組態來產生參數化的屬性。 如果找不到檔案，則會使用預設範本。

匯出 Resource Manager 範本時，Data Factory 會從目前正在處理的分支（而不是共同作業分支）讀取此檔案。 您可以從私人分支建立或編輯該檔案，在 UI 中選取 [匯出 ARM 範本]，即可測試變更。 接著，您可將檔案合併到共同作業分支中。

> [!NOTE]
> 自訂參數化範本不會變更 ARM 範本參數限制 256。 其可讓您選擇並減少參數化屬性的數目。

### <a name="custom-parameter-syntax"></a>自訂參數語法

以下是當您建立自訂 parameters 檔案 (arm-template-parameters-definition.json) 時所要遵循的一些指導方針。 此檔案是由每個實體類型的區段所組成：觸發程序、管線、連結服務、資料集、整合執行階段和資料流程。

* 輸入相關實體類型下的屬性路徑。
* 將屬性名稱設定為 `*`，表示您想要將其下的所有屬性參數化 (僅限向下的第一層，而非採用遞迴方式)。 您也可以提供此組態的例外狀況。
* 將屬性的值設定為字串，就表示您想要將屬性參數化。 使用此格式： `<action>:<name>:<stype>`。
   *  `<action>` 可以是下列其中一個字元：
      * `=` 表示將目前的值保留作為參數的預設值。
      * `-` 表示不要保留參數的預設值。
      * `|` 是 Azure Key Vault 中連接字串或金鑰祕密的特殊案例。
   * `<name>` 是參數的名稱。 如果空白，便會採用屬性的名稱。 如果值是以 `-` 字元開頭，則名稱會縮短。 例如，`AzureStorage1_properties_typeProperties_connectionString` 會縮短成 `AzureStorage1_connectionString`。
   * `<stype>` 是參數的類型。 如果 `<stype>` 空白，則預設類型為 `string`。 支援的值：`string`、`bool`、`number`、`object` 和 `securestring`。
* 在定義檔中指定陣列，就表示範本中的比對屬性是陣列。 Data Factory 會使用陣列的整合執行階段物件中所指定的定義，逐一查看陣列中的所有物件。 第二個物件 (字串) 會變成屬性的名稱，以作為每個反覆項目參數的名稱。
* 定義不得專屬於特定資源執行個體。 任何定義都會套用至該類型的所有資源。
* 根據預設，所有安全字串 (例如 Key Vault 秘密) 和安全字串 (例如連接字串、金鑰和權杖) 都會參數化。
 
### <a name="sample-parameterization-template"></a>範例參數化範本

以下是參數化範本可能的外觀範例：

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
以下說明如何建構前述範本，並依資源類型細分。

#### <a name="pipelines"></a>管線
    
* `activities/typeProperties/waitTimeInSeconds` 路徑中的任何屬性都已參數化。 管線中具有名為 `waitTimeInSeconds` 之程式碼層級屬性的任何活動 (例如，`Wait` 活動) 都會參數化為數字，並採用預設名稱。 但其在 Resource Manager 範本中沒有預設值。 在 Resource Manager 部署期間，必須加以輸入。
* 同樣地，名為 `headers` 的屬性 (例如，在 `Web` 活動中) 會以類型 `object` (JObject) 進行參數化。 其具有預設值，這與來源處理站的值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路徑 `typeProperties` 下的所有屬性都會使用其各自的預設值進行參數化。 例如，`IntegrationRuntimes` 類型屬性底下有兩個屬性：`computeProperties` 和 `ssisProperties`。 這兩個屬性類型都是使用其各自的預設值和類型 (物件) 來建立。

#### <a name="triggers"></a>觸發程序

* 在 `typeProperties` 底下，有兩個屬性已參數化。 第一個是 `maxConcurrency`，其已指定為具有預設值且類型為 `string`。 其具有預設參數名稱 `<entityName>_properties_typeProperties_maxConcurrency`。
* `recurrence` 屬性也已參數化。 在其底下，該層級的所有屬性都已指定成要參數化為字串，且具有預設值和參數名稱。 `interval` 屬性為例外狀況，其已參數化為 `number` 類型。 參數名稱後面會加上 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`。 同樣地，`freq` 屬性是字串並已參數化為字串。 不過，`freq` 屬性已參數化但沒有預設值。 名稱會縮短並加上尾碼。 例如： `<entityName>_freq` 。

#### <a name="linkedservices"></a>LinkedServices

* 連結服務是唯一的。 因為連結服務和資料集的類型廣泛，所以您可提供特定類型的自訂。 在此範例中，`AzureDataLakeStore` 類型的所有連結服務將會套用特定範本。 所有其他類型 (透過 `*`) 將會套用不同的範本。
* `connectionString` 屬性將會參數化為 `securestring` 值。 其不會有預設值。 但是會有後面加上 `connectionString` 的簡短參數名稱。
* `secretAccessKey` 的屬性會是 `AzureKeyVaultSecret` (例如，在 Amazon S3 連結服務中)。 其會自動參數化為 Azure Key Vault 秘密，並從已設定的金鑰保存庫提取。 您也可以將金鑰保存庫本身參數化。

#### <a name="datasets"></a>資料集

* 雖然特定類型的自訂適用於資料集，但您可提供組態，而不需明確擁有 \* 層級的組態。 在上述範例中，`typeProperties` 下的所有資料集屬性都已參數化。

### <a name="default-parameterization-template"></a>預設參數化範本

以下是目前的預設參數化範本。 如果您只需要新增幾個參數，則直接編輯此範本可能是很好的想法，因為您不會遺失現有的參數化結構。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    }
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>範例：參數化現有的 Azure Databricks 互動式叢集識別碼

下列範例顯示如何將單一值新增至預設參數化範本。 我們只想要將 Databricks 連結服務的現有 Azure Databricks 互動式叢集識別碼新增到 parameters 檔案。 請注意，除了在 `Microsoft.DataFactory/factories/linkedServices` 的 properties 欄位下新增 `existingClusterId` 以外，此檔案與先前的檔案相同。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>連結的 Resource Manager 範本

如果您已經為資料處理站設定 CI/CD，則隨著處理站日益狀大，您可能會超過 Azure Resource Manager 範本限制。 例如，其中一個限制是 Resource Manager 範本中的資源數目上限。 為了在產生處理站的完整 Resource Manager 範本時，容納大型處理站，Data Factory 現在會產生連結的 Resource Manager 範本。 透過這項功能，整個處理站承載會分成數個檔案，因此您不受限制約束。

如果您已設定 Git，則會產生連結的範本並與完整 Resource Manager 範本一起儲存在 adf_publish 分支中名為 linkedTemplates 的新資料夾中：

![連結的 Resource Manager 範本資料夾](media/continuous-integration-deployment/linked-resource-manager-templates.png)

連結的 Resource Manager 範本通常包含一個主要範本，以及一組連結到主要範本的子系範本。 父代範本稱為 ArmTemplate_master.json，而子系範本則會使用 ArmTemplate_0.json、ArmTemplate_1.json 等模式來命名。 

若要使用連結的範本，而不是完整的 Resource Manager 範本，請更新您的 CI/CD 工作，使其指向 ArmTemplate_master.json，而不是 ArmTemplateForFactory.json (完整 Resource Manager 範本)。 Resource Manager 也會要求您將連結的範本上傳到儲存體帳戶，以便 Azure 在部署期間存取這些範本。 如需詳細資訊，請參閱[透過 VSTS 部署連結的 Resource Manager 範本](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

請記得在在部署工作之前和之後，於 CI/CD 管線中新增 Data Factory 指令碼。

如果尚未設定 Git，則可透過 [ARM 範本] 清單中的 [匯出 ARM 範本]來存取連結的範本。

## <a name="hotfix-production-environment"></a>修復生產環境

如果您將處理站部署到生產環境，並發現有需要立即修正的錯誤 (bug)，但是您無法部署目前的共同作業分支，則可能需要部署 Hotfix。 這種方法也稱為快速檢修或 QFE。

1.    在 Azure DevOps 中，移至已部署至生產環境的發行。 尋找已部署的最後一個認可。

2.    從認可訊息中，取得共同作業分支的認可識別碼。

3.    從該認可建立新的 Hotfix 分支。

4.    移至 Azure Data Factory UX 並切換至 Hotfix 分支。

5.    藉由使用 Azure Data Factory UX，修正錯誤 (bug)。 測試您的變更。

6.    確認修正之後，請選取 [匯出 ARM 範本]，以取得 Hotfix Resource Manager 範本。

7.    手動將此組建簽入 adf_publish 分支。

8.    如果您已將發行管線設定為根據 adf_publish 簽入自動觸發，則會自動啟動新的發行。 否則，手動將發行排入佇列。

9.    將此 Hotfix 發行部署到測試和生產處理站。 此發行包含先前的生產承載，以及您在步驟 5 所做的修正。

10.   將 Hotfix 中的變更新增至開發分支，後續的發行就不會包含相同的錯誤 (bug)。

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您將 Git 整合與資料處理站搭配使用，而且具有 CI/CD 管線，可將您的變更從開發轉移到測試，然後再轉移到生產，我們建議採用以下最佳做法：

-   **Git 整合**。 只要使用 Git 整合設定您的開發資料處理站。 測試與生產變更已透過 CI/CD 部署，而且不需要 Git 整合。

-   **部署前後指令碼**。 在 CI/CD 的 Resource Manager 部署步驟之前，您必須先完成某些工作，例如停止並重新開機觸發程序以及執行清除作業。 我們建議您在部署工作前後使用 PowerShell 指令碼。 如需詳細資訊，請參閱[更新使用中的觸發程序](#updating-active-triggers)。 資料處理站小組已[提供可用的指令碼](#script) (位於此頁面的底部)。

-   **整合執行階段和共用**。 整合執行階段不會經常變更，而且在 CI/CD 中的所有階段都很類似。 所以 Data Factory 預期在 CI/CD 的所有階段中，要有相同的名稱和相同類型的整合執行階段。 如果您想要在所有階段中共用整合執行階段，請考慮使用三元處理站，只包含共用的整合執行階段。 您可以在所有環境中，使用此共用處理站作為連結的整合執行階段類型。

-   **Key Vault**。 當您使用連線資訊存放在 Azure Key Vault 中的連結服務時，建議您針對不同的環境保留個別的金鑰保存庫。 您也可以為每個金鑰保存庫設定不同的權限層級。 例如，您可能不希望小組成員具有生產秘密的權限。 如果您遵循此方法，我們建議您在所有階段中保留相同的秘密名稱。 如果您保留相同的秘密名稱，則不需要將所有 CI/CD 環境中的每個連接字串參數化，因為唯一的變更是金鑰保存庫名稱 (這是個別的參數)。

-  **資源命名** 由於 ARM 範本條件約束的緣故，如果您的資源在名稱中包含空格，則可能會發生部署問題。 Azure Data Factory 團隊建議使用 ' _ ' 或 '-' 字元，而不是資源的空格。 例如，' Pipeline_1 ' 會是比 ' Pipeline 1 ' 更理想的名稱。

## <a name="unsupported-features"></a>不支援的功能

- 根據設計，Data Factory 不允許揀選認可或選擇性發佈資源。 發佈將會包含在資料處理站中進行的所有變更。

    - 資料處理站實體彼此相依。 例如，觸發程序取決於管線，而管線取決於資料集和其他管線。 選擇性發佈資源子集可能會導致非預期的行為和錯誤。
    - 在少數情況下，當您需要選擇性發佈時，請考慮使用 Hotfix。 如需詳細資訊，請參閱 [修復生產環境](#hotfix-production-environment)。

- Azure Data Factory 小組不建議將 RBAC 控制項指派給資料處理站中 (管線、資料集等) 的個別實體。 例如，如果開發人員有管線或資料集的存取權，就應該能夠存取 data factory 中的所有管線或資料集。 如果您認為您需要在資料處理站內執行許多 RBAC 角色，請查看部署第二個 data factory。

-   您無法從私人分支發佈。

-   您目前無法在 Bitbucket 上裝載專案。

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a>範例部署前後指令碼

下列範例指令碼可用來在部署前停止觸發程序，且之後重新啟動觸發程序。 此指令碼也包含可將已移除的資源刪除的程式碼。 將指令碼儲存在 Azure DevOps Git 存放庫中，並透過使用 4.* 版的 Azure PowerShell 工作加以參考。

執行部署前指令碼時，您必須在 [指令碼引數] 欄位中指定下列參數的變化。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


執行部署後指令碼時，您必須在 [指令碼引數] 欄位中指定下列參數的變化。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell 工作](media/continuous-integration-deployment/continuous-integration-image11.png)

以下是可用於部署前後的指令碼。 其可說明已刪除的資源和資源參考。

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
