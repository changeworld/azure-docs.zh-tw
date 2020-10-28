---
title: 快速入門：使用 GitHub Actions 連線到 Azure MySQL
description: 從 GitHub Actions 工作流程使用 Azure MySQL
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ce045da7d21c2af0dfde5ee896b4f7f343ce6545
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541259"
---
# <a name="use-github-actions-to-connect-to-azure-mysql"></a>使用 GitHub Actions 連線到 Azure MySQL

藉由使用工作流程將資料庫更新部署至[適用於 MySQL 的 Azure 資料庫](https://azure.microsoft.com/services/mysql/)，來開始使用 [GitHub Actions](https://docs.github.com/en/actions)。 

## <a name="prerequisites"></a>先決條件

您將需要： 
- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 具有範例資料 (`data.sql`) 的 GitHub 存放庫。 如果您沒有 GitHub 帳戶，請[免費註冊](https://github.com/join)。  
- 適用於 MySQL 的 Azure 資料庫伺服器。
    - [快速入門：在 Azure 入口網站中建立適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>工作流程檔案概觀

GitHub Actions 工作流程是由您存放庫內 `/.github/workflows/` 路徑中的 YAML (.yml) 檔案所定義的。 此定義包含組成工作流程的各種步驟與參數。

檔案內有兩個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1.定義服務主體。 <br /> 2.建立 GitHub 祕密。 |
|**部署** | 1.部署資料庫。 |

## <a name="generate-deployment-credentials"></a>產生部署認證

您可以使用 [Azure CLI](/cli/azure/) 中的 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) 命令來建立[服務主體](../active-directory/develop/app-objects-and-service-principals.md)。 請使用 Azure 入口網站中的 [Azure Cloud Shell](https://shell.azure.com/)，或選取 [試試看] 按鈕來執行此命令。

以 Azure 上所裝載 MySQL 伺服器的名稱取代預留位置 `server-name`。 以連線至 MySQL 伺服器的訂用帳戶識別碼和資源群組取代 `subscription-id` 和 `resource-group`。  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

輸出是一個 JSON 物件，內有角色指派認證可讓您存取資料庫，如下所示。 複製此輸出 JSON 物件以供稍後使用。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 授與最小存取權永遠是最佳作法。 前面範例中的範圍限制為特定伺服器，而不是整個資源群組。

## <a name="copy-the-mysql-connection-string"></a>複製 MySQL 連接字串 

在 Azure 入口網站中，移至適用於 MySQL 的 Azure 資料庫伺服器，然後開啟 [設定] > [連接字串]。 複製 **ADO.NET** 連接字串。 取代 `your_database` 和 `your_password` 的預留位置值。 連接字串看起來會像這樣。 

```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
您將使用此連接字串作為 GitHub 秘密。 

## <a name="configure-the-github-secrets"></a>設定 GitHub 祕密

1. 在 [GitHub](https://github.com/) 中，瀏覽您的存放庫。

1. 選取 [設定] > [秘密] > [新增秘密]。

1. 將得自 Azure CLI 命令的整個 JSON 輸出貼到祕密的 [值] 欄位中。 將祕密命名為 `AZURE_CREDENTIALS`。

    當您稍後設定工作流程檔案時，會將祕密用於 Azure 登入動作的輸入 `creds`。 例如：

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. 再次選取 [新增密碼]。 

1. 將連接字串值貼到祕密的 [值] 欄位中。 將祕密命名為 `AZURE_MYSQL_CONNECTION_STRING`。


## <a name="add-your-workflow"></a>新增您的工作流程

1. 移至 GitHub 存放庫的 [動作]。 

2. 選取 [自行設定工作流程]。 

2. 刪除工作流程檔案 `on:` 區段之後的所有內容。 例如，剩餘的工作流程看起來可能像這樣。 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重新命名工作流程 `MySQL for GitHub Actions`，並新增簽出和登入動作。 這些動作會簽出您的站台碼，並使用您稍早建立的 `AZURE_CREDENTIALS` GitHub 祕密向 Azure 進行驗證。 

    ```yaml
    name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. 使用 Azure MySQL 的「部署」動作來連線到您的 MySQL 執行個體。 以伺服器的名稱取代 `MYSQL_SERVER_NAME`。 在存放庫的根層級中，您應該會有名為 `data.sql` 的 MySQL 資料檔案。 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. 藉由新增動作至 Azure 的登出，來完成您的工作流程。 以下是完成後的工作流程。 檔案會出現在存放庫的 `.github/workflows` 資料夾中。

    ```yaml
   name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>檢閱您的部署

1. 移至 GitHub 存放庫的 [動作]。 

1. 開啟第一個結果，以查看工作流程的執行詳細記錄。 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="GitHub 動作執行的記錄":::

## <a name="clean-up-resources"></a>清除資源

當您不再需要 Azure MySQL 資料庫和存放庫時，請刪除資源群組和 GitHub 存放庫，以清除您所部署的資源。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Azure 和 GitHub 整合](/azure/developer/github/)