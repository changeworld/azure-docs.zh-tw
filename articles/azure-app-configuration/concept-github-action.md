---
title: 將您的 GitHub 存放庫同步至應用程式組態
description: 當您更新 GitHub 存放庫時，請使用 GitHub Actions 自動更新您的應用程式組態執行個體。
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 66d0e32e7dfdd5ab2abee5108ac8ce54c5222747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371816"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>將您的 GitHub 存放庫同步至應用程式組態

想要繼續使用現有原始檔控制實務的小組可以使用 GitHub Actions，自動將其 GitHub 存放庫與其應用程式組態存放區同步。 這可讓您以平常的方式對設定檔進行變更，同時獲得應用程式組態的優點，例如： <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   在您的程式碼外集中設定 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   更新設定而不需要重新部署整個應用程式 <br>
&nbsp;&nbsp;&nbsp;&nbsp;•   與 Azure App Service 和 Functions 之類的服務整合。 

GitHub Actions [工作流程](https://help.github.com/articles/about-github-actions#workflow)會在 GitHub 存放庫中定義自動化程序。 「Azure 應用程式組態同步」動作會在對來源存放庫進行變更時，觸發應用程式組態執行個體的更新。 其使用在您存放庫的 `/.github/workflows/` 路徑中找到的 YAML (.yml) 檔案，來定義步驟和參數。 您可以在推送、檢閱應用程式設定檔，或對其進行分支時，觸發設定更新，就像您處理應用程式程式碼一樣。

GitHub [文件](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) \(英文\) 提供 GitHub 工作流程和動作的深入觀點。 

## <a name="enable-github-actions-in-your-repository"></a>在您的存放庫中啟用 GitHub Actions
若要開始使用此 GitHub 動作，請移至您的存放庫，然後選取 [ **動作** ] 索引標籤。選取 [ **新增工作流程**]，然後 **自行設定工作流程**。 最後，在 Marketplace 中搜尋「Azure App Configuration Sync」。
> [!div class="mx-imgBorder"]
> ![選取 [動作] 索引標籤](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![選取應用程式設定同步處理動作](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>推送之後同步處理設定檔
當變更推送至 `appsettings.json` 時，此動作會同步處理 Azure 應用程式組態檔案。 當開發人員將變更推送到 `appsettings.json` 時，應用程式組態同步動作會使用新的值來更新應用程式組態執行個體。

此工作流程的第一個區段指定動作將會在「推送」至 *master* 分支的內容包含 `appsettings.json` 時觸發。 第二個區段列出觸發動作之後所執行的作業。 動作會簽出相關檔案，並使用儲存在存放庫中作為祕密的連接字串來更新應用程式組態執行個體。  如需在 GitHub 中使用祕密的詳細資訊，請參閱關於建立及使用已加密祕密的 [GitHub 的文章](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) \(英文\)。

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

## <a name="use-strict-sync"></a>使用嚴格同步處理
根據預設，GitHub 動作不會啟用嚴格模式，這表示同步處理只會將設定檔中的機碼/值新增至應用程式組態執行個體 (不會刪除機碼/值組)。 啟用嚴格模式將會從應用程式組態執行個體中刪除不在設定檔中的機碼/值組，使其符合設定檔。 如果您從多個來源進行同步處理，或搭配應用程式組態使用 Azure Key Vault，您會想要使用不同的前置詞或使用嚴格同步處理的標籤，以避免從其他檔案抹除組態設定 (請參閱下面的範例)。 

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
## <a name="sync-multiple-files-in-one-action"></a>在一個動作中同步處理多個檔案 

如果您的設定是在多個檔案中，您可以使用下列模式，以在其中一個檔案遭到修改時觸發同步處理。 此模式會使用 glob 程式庫 https://www.npmjs.com/package/glob 。 請注意，如果您的設定檔名稱包含逗號，則可以使用反斜線來 escape 逗號。 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>依前置詞或標籤同步
在同步動作中指定前置詞或標籤，只會同步處理該特定集合。 這對於搭配多個檔案使用嚴格同步處理很重要。 取決於設定組態的方式，前置詞或標籤可以與每個檔案相關聯，然後每個前置詞或標籤可以個別同步處理，而不會覆寫任何內容。 通常會針對不同的應用程式或服務使用前置詞，並將標籤用於不同的環境。 

依前置詞同步： 

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

依標籤同步： 

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>在同步處理時使用動態標籤
下列動作會在每次同步處理時插入動態標籤，確保可以唯一識別每個同步處理，並允許程式碼變更對應至設定變更。

此工作流程的第一個區段指定動作將會在「推送」至 *master* 分支的內容包含 `appsettings.json` 時觸發。 第二個區段會執行作業，其以認可雜湊為基礎來建立設定更新的唯一標籤。 然後，作業會使用新的值和此更新的唯一標籤來更新應用程式組態執行個體。

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

## <a name="use-azure-key-vault-with-github-action"></a>使用 Azure Key Vault 搭配 GitHub 動作
使用 AppConfiguration 搭配 Azure Key Vault 的開發人員應該使用兩個不同的檔案，通常是 appsettings.json 和 secretreferences.json。 secretreferences.json 將會包含金鑰保存庫祕密的 URL。

{ "mySecret": "{\"uri\":\"https://myKeyVault.vault.azure.net/secrets/mySecret"}" }

接著，可以將 GitHub 動作設定為在 appsettings.json 上進行嚴格同步處理，然後在 secretreferences.json 上進行非嚴格同步處理。 下列範例將會在更新任一檔案時觸發同步處理：

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

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
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>使用最大深度來限制 GitHub 動作
巢狀 JSON 屬性的預設行為是將整個物件壓平合併。  下列 JSON 會定義此機碼/值組：

| Key | 值 |
| --- | --- |
| Object:Inner:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

如果巢狀物件的目的是要作為推送至設定執行個體的值，您可以使用 *depth* 值，以適當的深度停止壓平合併。 

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

假設深度為 2，則上述範例現在會傳回下列機碼/值組：

| Key | 值 |
| --- | --- |
| Object:Inner | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>了解動作輸入
輸入參數會指定動作在執行階段所使用的資料。  下列表格包含應用程式組態同步處理所接受的輸入參數，以及每個的預期值。  如需 GitHub Actions 之動作輸入的詳細資訊，請參閱 GitHub 的[文件](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs) \(英文\)。

> [!Note]
> 輸入識別碼不區分大小寫。


| 輸入名稱 | 必要項？ | 值 |
|----|----|----|
| configurationFile | 是 | 設定檔在存放庫中的相對路徑。  支援 Glob 模式，而且可以包含多個檔案。 |
| format | 是 | 設定檔案的檔案格式。  有效的格式為：JSON、YAML、屬性。 |
| connectionString | 是 | 應用程式組態執行個體的連接字串。 連接字串應儲存為 GitHub 存放庫中的祕密，而且工作流程中只應使用祕密名稱。 |
| separator | 是 | 設定檔壓平合併成機碼/值組時，所使用的分隔符號。  有效的值為：. , ; : - _ __ / |
| prefix | 否 | 要加入至機碼開頭的前置詞。 |
| 標籤 | 否 | 設定機碼/值組時使用的標籤。 如果未指定，則會使用 Null 標籤。 |
| strict | 否 | 判斷是否已啟用嚴格模式的布林值。 預設值為 false。 |
| depth | 否 | 將設定檔壓平合併的最大深度。  深度必須是正數。  預設值將沒有最大深度。 |
| tags | 否 | 指定在機碼/值組上設定的標記。  預期的格式是具有下列形狀的字串化格式 JSON 物件：{ [propertyName: string]: string; } 每個屬性值-名稱都會成為標籤。 |

## <a name="next-steps"></a>後續步驟

在此文章中，您已了解應用程式組態同步 GitHub 動作，以及如何將其用於將應用程式組態執行個體的更新自動化。 若要了解 Azure 應用程式組態如何對機碼/值組中的變更做出反應，請繼續閱讀下一篇[文章](./concept-app-configuration-event.md)。
