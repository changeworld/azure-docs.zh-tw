---
title: 將 GitHub 儲存函式庫同步到應用設定
description: 使用 GitHub 操作在更新 GitHub 儲存庫時自動更新應用配置實例。
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585487"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>將 GitHub 儲存函式庫同步到應用設定

希望繼續使用其現有原始程式碼管理實務的團隊可以使用GitHub操作自動將其GitHub儲存庫與其應用配置儲存同步。 這允許您像通常一樣對設定檔進行更改,同時獲得應用配置優勢,例如: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 程式碼之外的集中設定 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 更新配置,而無需重新部署整個應用 <br>
&nbsp;&nbsp;&nbsp;&nbsp;• 與 Azure 應用服務和功能等服務整合。 

GitHub 操作[工作流](https://help.github.com/articles/about-github-actions#workflow)定義 GitHub 儲存庫中的自動化進程。 對來源儲存庫進行更改時 *,Azure 應用配置同步*操作會觸發對應用配置實例的更新。 它使用在儲存庫路徑中找到的`/.github/workflows/`YAML (.yml) 檔案來定義步驟和參數。 在推送、查看或分支應用配置檔時,可以觸發配置更新,就像使用應用代碼一樣。

GitHub[文件](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow)提供 GitHub 工作流和操作的深入檢視。 

## <a name="enable-github-actions-in-your-repository"></a>在儲存式庫中開啟 GitHub 操作
要開始使用這個 GitHub 操作,請轉到儲存函式庫並選擇「**動作」** 選項卡。按下 **'新增工作流**」,然後**自己設定工作流**。 最後,在市場搜索"Azure 應用配置同步"。
> [!div class="mx-imgBorder"]
> ![選擇「動作」選項卡](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![選擇應用設定同步作業](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>發送後同步設定檔
此操作在將更改推送到`appsettings.json`時同步 Azure 應用配置檔。 當開發人員將更改推送到`appsettings.json`時,應用配置同步操作使用新值更新應用配置實例。

此工作流的第一部分指定`appsettings.json`操作在包含*為主分支的**推送**上*觸發。 第二部分列出了觸發操作後運行的作業。 該操作檢查相關文件並使用儲存庫中作為機密存儲的連接字串更新應用配置實例。  有關在 GitHub 中使用機密的詳細資訊,請參閱[GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)有關創建和使用加密機密的文章。

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

## <a name="use-a-dynamic-label-on-sync"></a>在同步時使用動態標籤
每當更新時`appsettings.json`,前面的操作都會更新應用配置實例。 此操作在每個同步上插入一個動態標籤,確保可以唯一標識每個同步,並允許將代碼更改映射到配置更改。

此工作流的第一部分指定`appsettings.json`操作在包含*為主分支的**推送**上*觸發。 第二部分運行一個作業,該作業基於提交哈希為配置更新創建唯一標籤。 然後,作業使用新值和此更新的唯一標籤更新應用配置實例。

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

## <a name="use-strict-sync"></a>使用嚴格同步
啟用嚴格模式後,同步可確保應用配置實例與給定首碼和標籤的設定檔完全匹配。 刪除設定檔中未具有相同首碼和標籤的鍵值對。 
 
如果未啟用嚴格模式,則同步將僅設定設定檔中的鍵值。 不會刪除任何鍵值對。 

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

## <a name="use-max-depth-to-limit-github-action"></a>使用最大深度限制 GitHub 操作
嵌套 JSON 屬性的默認行為是拼平整個物件。  下面的 JSON 定義此鍵值對:

| Key | 值 |
| --- | --- |
| 物件:內部鍵:內鍵 | 內部價值 |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

如果嵌套物件旨在將值推送到配置實例,則可以使用*深度*值在適當的深度停止展平。 

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

給定深度為 2,上述範例現在返回以下鍵值對:

| Key | 值 |
| --- | --- |
| 物件:內部 | {"內鍵":"內部價值"} |

## <a name="understand-action-inputs"></a>瞭解操作輸入
輸入參數指定操作在運行時使用的數據。  下表包含應用配置同步接受的輸入參數和每個參數的預期值。  有關 GitHub 操作的操作輸入的詳細資訊,請參閱 GitHub[的文件](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)。

> [!Note]
> 輸入指示號不區分大小寫。


| 輸入名稱 | 必要項？ | 值 |
|----|----|----|
| 設定檔 | 是 | 儲存庫中設定檔的相對路徑。  Glob 模式受支援,可以包含多個檔。 |
| format | 是 | 設定檔的檔案格式。  有效格式包括:JSON、YAML、屬性。 |
| connectionString | 是 | 應用配置實例的連接字串。 連接字串應作為機密存儲在 GitHub 儲存庫中,並且在工作流中應僅使用機密名稱。 |
| separator | 是 | 將設定檔拼平到鍵值對時使用的分隔符。  合法值為: 。 , ; : - _ __ / |
| prefix | 否 | 要添加到鍵開頭的首碼。 |
| 標籤 | 否 | 設置鍵值對時使用的標籤。 如果未指定,則使用空標籤。 |
| strict | 否 | 確定是否啟用嚴格模式的布爾值。 預設值為 false。 |
| 深度 | 否 | 用於拼平配置檔的最大深度。  深度必須為正數。  默認值將沒有最大深度。 |
| tags | 否 | 指定在鍵值對上設置的標記。  預期格式是以下形狀的 JSON 物件的字串化形式: { 屬性名稱: 字串":字串;每個屬性名稱值都將成為標記。 |

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了應用配置同步 GitHub 操作及其如何使用它來自動更新應用配置實例。 要瞭解 Azure 應用設定對鍵值對中的更改有何反應,請繼續執行下一[篇文章](./concept-app-configuration-event.md)。
