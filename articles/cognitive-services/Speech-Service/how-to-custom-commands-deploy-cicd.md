---
title: '使用 Azure DevOps (預覽的持續部署) '
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何為您的自訂命令應用程式設定連續部署。 您可以建立腳本來支援持續部署工作流程。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: bd1ab5110313380c90e71ed161935c7274a845b7
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839243"
---
# <a name="continuous-deployment-with-azure-devops"></a>使用 Azure DevOps 進行持續部署

在本文中，您將瞭解如何為您的自訂命令應用程式設定連續部署。 支援 CI/CD 工作流程的腳本會提供給您。

## <a name="prerequisite"></a>必要條件
> [!div class = "checklist"]
> * 適用于開發 (開發) 的自訂命令應用程式
> * 適用于生產環境 (生產) 的自訂命令應用程式
> * 註冊 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)

## <a name="exportimportpublish"></a>匯出/匯入/發佈

腳本會裝載于 [認知服務語音助理-自訂命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 將 bash 目錄中的腳本複製到您的儲存機制。 請確定您維持相同的路徑。

### <a name="set-up-a-pipeline"></a>設定管線 

1. 移至 **Azure DevOps-管線** ，然後按一下 [新增管線]
1. 在 **[連線]** 區段中，選取您存放庫的位置（這些腳本位於何處）
1. 在 [ **選取** ] 區段中，選取您的存放庫
1. 在 [ **設定** ] 區段中，選取 [入門管線]
1. 接下來，您將會看到具有 YAML 檔案的編輯器，請將「步驟」區段取代為此腳本。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. 請注意，這些腳本假設您使用的是區域 `westus2` ，如果不是，則會據以更新工作的引數

    > [!div class="mx-imgBorder"]
    > ![反白顯示引數中區域值的螢幕擷取畫面。](media/custom-commands/cicd-new-pipeline-yaml.png)

1. 在 [儲存並執行] 按鈕中，開啟下拉式清單，然後按一下 [儲存]。

### <a name="hook-up-the-pipeline-with-your-application"></a>將管線與您的應用程式連結

1. 流覽至管線的主頁面。
1. 在右上角的下拉式清單中，選取 [ **編輯管線**]。 它會取得 YAML 編輯器。 
1. 在右上角的 [執行] 按鈕旁邊，選取 [ **變數**]。 按一下 [ **新增變數**]。
1. 新增下列變數：
    
    | 變數 | 描述 |
    | ------- | --------------- | ----------- |
    | SourceAppId | 開發應用程式的識別碼 |
    | TargetAppId | 生產應用程式的識別碼 |
    | SubscriptionKey | 適用于這兩個應用程式的訂用帳戶金鑰 |
    | 文化特性 | 應用程式的文化特性 (亦即 en-us)  |

    > [!div class="mx-imgBorder"]
    > ![傳送活動裝載](media/custom-commands/cicd-edit-pipeline-variables.png)

1. 按一下 [執行]，然後按一下執行中的 [作業]。 

    您應該會看到執行中的工作清單，其中包含：「匯出來源應用程式」、「匯入目標應用程式」 & 「定型和發佈目標應用程式」

## <a name="deploy-from-source-code"></a>從原始程式碼部署

如果您想要將應用程式的定義保留在存放庫中，我們會提供從原始程式碼進行部署的腳本。 由於腳本是在 bash 中，如果您使用的是 Windows，則必須安裝 [Linux 子系統](https://docs.microsoft.com/windows/wsl/install-win10)。

腳本會裝載于 [認知服務語音助理-自訂命令](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)。 將 bash 目錄中的腳本複製到您的儲存機制。 請確定您維持相同的路徑。

### <a name="prepare-your-repository"></a>準備您的存放庫

1. 為您的應用程式建立目錄，在我們的範例中建立一個名為「應用程式」的目錄。
1. 更新下列 bash 腳本的引數，然後執行。 它會將您應用程式的對話模型匯入 myapp.js的檔案
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 引數 | 描述 |
    | ------- | --------------- | ----------- |
    | region | 應用程式的區域，亦即 westus2。 |
    | subscriptionkey | 語音資源的訂用帳戶金鑰。 |
    | appid | 您要匯出的自訂命令應用程式識別碼。 |

1. 將這些變更推送至您的存放庫。

### <a name="set-up-a-pipeline"></a>設定管線 

1. 移至 **Azure DevOps-管線** ，然後按一下 [新增管線]
1. 在 **[連線]** 區段中，選取您存放庫的位置（這些腳本位於何處）
1. 在 [ **選取** ] 區段中，選取您的存放庫
1. 在 [ **設定** ] 區段中，選取 [入門管線]
1. 接下來，您將會看到具有 YAML 檔案的編輯器，請將「步驟」區段取代為此腳本。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > 這些腳本假設您使用的是區域 westus2，如果不是，則會據以更新工作的引數

1. 在 [儲存並執行] 按鈕中，開啟下拉式清單，然後按一下 [儲存]。

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>將管線與目標應用程式連結

1. 流覽至管線的主頁面。
1. 在右上角的下拉式清單中，選取 [ **編輯管線**]。 它會取得 YAML 編輯器。 
1. 在右上角的 [執行] 按鈕旁邊，選取 [ **變數**]。 按一下 [ **新增變數**]。
1. 新增下列變數：

    | 變數 | 描述 |
    | ------- | --------------- | ----------- |
    | TargetAppId | 生產應用程式的識別碼 |
    | SubscriptionKey | 適用于這兩個應用程式的訂用帳戶金鑰 |
    | 文化特性 | 應用程式的文化特性 (亦即 en-us)  |

1. 按一下 [執行]，然後按一下執行中的 [作業]。
    您應該會看到執行中的工作清單，其中包含：「匯入應用程式」 & 「定型和發佈應用程式」

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [請參閱 GitHub 上的範例](https://aka.ms/speech/cc-samples)