---
title: 在 Azure 應用程式組態同步處理中使用 GitHub 動作
description: 在 GitHub 存放庫上執行已定義的動作時，請使用 GitHub 動作來觸發應用程式組態實例的更新
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523708"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>使用 GitHub Actions 將您的應用程式組態執行個體進行同步處理
Azure 應用程式組態根據 GitHub 存放庫上執行的動作，使用 GitHub 動作來觸發應用程式組態實例的更新。 GitHub 工作流程會觸發設定更新，以便將這些更新整合到用來更新應用程式程式碼的相同工作流程中。

GitHub 動作[工作流程](https://help.github.com/articles/about-github-actions#workflow)會在 github 存放庫中定義自動化程式。 此程式會告訴 GitHub 如何建立和部署您的 GitHub 專案。 Azure 應用程式組態提供*Azure 應用程式組態同步*動作，以便在對來源存放庫進行變更時，啟用應用程式組態實例的更新。 

在您存放庫的 `/.github/workflows/` 路徑中找到的 YAML （. yml）檔案會定義您的工作流程。 這個定義包含工作流程的步驟和參數。

GitHub 事件（例如推送至存放庫）可以觸發 GitHub 動作工作流程。  [ *Azure 應用程式組態同步*] 動作可讓您在指定的 GitHub 動作發生時，觸發應用程式組態實例的更新。 您可以在推送、查看或分支應用程式佈建檔時觸發設定更新，就像使用應用程式程式碼一樣。

GitHub[檔](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)可讓您深入瞭解 github 工作流程和動作。 

## <a name="enable-github-actions-in-your-repository"></a>在您的存放庫中啟用 GitHub 動作
若要開始使用此 GitHub 動作，請移至您的存放庫並選取 [**動作**] 索引標籤。按一下 [**新增工作流程**]，然後**自行設定工作流程**。 最後，在 marketplace 中搜尋「Azure 應用程式組態同步」。
> [!div class="mx-imgBorder"]
> ![選取 [動作] 索引標籤](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![選取應用程式設定同步動作](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>推送之後同步處理設定檔案
當變更推送至 `appsettings.json`時，此動作會同步處理 Azure 應用程式組態的檔案。 當開發人員將變更推送到 `appsettings.json`時，應用程式組態同步動作會使用新的值來更新應用程式組態實例。

此工作流程的第一個區段會指定動作會*在*包含 `appsettings.json` 到*主要*分支的*推送*上觸發。 第二個區段會列出觸發動作之後執行的作業。 動作會簽出相關的檔案，並使用儲存在存放庫中做為秘密的連接字串來更新應用程式組態實例。  如需在 GitHub 中使用密碼的詳細資訊，請參閱 Github 的關於建立和使用加密密碼[的文章](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>在同步處理時使用動態磁碟區標
當 `appsettings.json` 更新時，上一個動作會更新應用程式組態實例。 這個動作會在每次同步處理時插入動態磁碟區標，確保每個同步處理都可以唯一識別，並允許程式碼變更對應到設定變更。

此工作流程的第一個區段會指定動作會*在*包含 `appsettings.json` 到*主要*分支的*推送*上觸發。 第二個區段會執行作業，以根據認可雜湊來建立設定更新的唯一標籤。 然後，作業會使用新的值和此更新的唯一標籤來更新應用程式組態實例。

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>使用嚴格同步處理
啟用 strict 模式時，同步處理可確保應用程式組態實例完全符合指定前置詞和標籤的設定檔。 具有與不在設定檔中相同前置詞和標籤的索引鍵/值組會被刪除。 
 
如果未啟用 strict 模式，同步處理只會從設定檔案中設定索引鍵/值。 將不會刪除索引鍵/值組。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>使用最大深度來限制 GitHub 動作
Nested JSON 屬性的預設行為是將整個物件壓平合併。  下列 JSON 會定義此索引鍵/值組：

| Key | 值 |
| --- | --- |
| 物件：內部： InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

如果將嵌套物件視為推送至設定實例的值，您可以使用*深度*值，以適當的深度停止簡維。 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

假設深度為2，則上述範例現在會傳回下列機碼值組：

| Key | 值 |
| --- | --- |
| 物件：內部 | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>瞭解動作輸入
輸入參數會指定在執行時間期間，動作所使用的資料。  下表包含應用程式組態同步處理所接受的輸入參數，以及每個的預期值。  如需 GitHub 動作之動作輸入的詳細資訊，請參閱 GitHub[檔](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)。

> [!Note]
> 輸入識別碼不區分大小寫。


| 輸入名稱 | 必要項？ | 值 |
|----|----|----|
| configurationFile | 是 | 存放庫中設定檔的相對路徑。  支援 Glob 模式，而且可以包含多個檔案。 |
| 格式 | 是 | 設定檔案的檔案格式。  有效的格式為： JSON、YAML、properties。 |
| connectionString | 是 | 應用程式組態實例的連接字串。 連接字串應儲存為 GitHub 存放庫中的秘密，而且工作流程中只能使用秘密名稱。 |
| separator | 是 | 將設定檔簡維成成對的機碼值時，所使用的分隔符號。  有效的值為：。 , ;: - _ __ / |
| 前置詞 | 否 | 要加入至索引鍵開頭的前置詞。 |
| label | 否 | 設定索引鍵/值組時使用的標籤。 如果未指定，則會使用 null 標籤。 |
| strict | 否 | 判斷是否已啟用 strict 模式的布林值。 預設值為 false。 |
| 豐富 | 否 | 簡維設定檔的最大深度。  深度必須是正數。  預設值將沒有最大深度。 |
| tags | 否 | 指定在索引鍵/值組上設定的標記。  預期的格式為下列圖形之 JSON 物件的 stringified 形式： {[propertyName： string]： string;}每個屬性名稱-值都會變成一個標記。 |

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解應用程式組態同步 GitHub 動作，以及如何使用它來自動化應用程式組態實例的更新。 若要瞭解 Azure 應用程式組態如何對索引鍵/值組中的變更做出反應，請繼續進行下一篇[文章](./concept-app-configuration-event.md)。
