---
title: 教學課程 - 使用 Azure Databricks 以金鑰保存庫存取 Blob 儲存體
description: 本教學課程說明如何使用儲存在金鑰保存庫中的秘密，從 Azure Databricks 存取 Azure Blob 儲存體。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 026165c7c2052992e8ab485f9ab81c8964f38235
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647960"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>教學課程：使用 Azure Key Vault 從 Azure Databricks 存取 Azure Blob 儲存體

本教學課程說明如何使用儲存在金鑰保存庫中的秘密，從 Azure Databricks 存取 Azure Blob 儲存體。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶 Blob 容器
> * 建立 Azure Key Vault 並新增秘密
> * 建立 Azure Databricks 工作區並新增秘密範圍
> * 從 Azure Databricks 存取您的 Blob 容器

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

> [!Note]
> 本教學課程不適用 **Azure 免費試用版的訂用帳戶**。
> 如果您有免費帳戶，請移至您的設定檔，並將訂用帳戶變更為**隨用隨付**。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。 然後，為您所在區域的 vCPU [移除消費限制](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)並[要求增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。 當您建立 Azure Databricks 工作區時，您可以選取 [試用版 (進階 - 14 天的免費 DBU)]  定價層，讓工作區可免費存取進階 Azure Databricks DBU 14 天。

## <a name="create-a-storage-account-and-blob-container"></a>建立儲存體帳戶 Blob 容器

1. 在 Azure 入口網站中，選取 [建立資源]   > [儲存體]  。 然後，選取 [儲存體帳戶]  。

   ![尋找 Azure 儲存體帳戶資源](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. 選取您的訂用帳戶和資源群組，或建立新的資源群組。 然後，輸入儲存體帳戶名稱，並選擇位置。 選取 [檢閱 + 建立]  。

   ![設定儲存體帳戶屬性](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. 如果驗證失敗，請解決問題，然後再試一次。 如果驗證成功，請選取 [建立]  ，然後等候儲存體帳戶建立。

4. 瀏覽至您新建立的儲存體帳戶，然後在 [概觀]  頁面的 [服務]  下方選取 [Blob]  。 接著，選取 [+容器]  並輸入容器名稱。 選取 [確定]  。

   ![建立新容器](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. 找出您要上傳至 Blob 儲存體容器的檔案。 如果您沒有檔案，請使用文字編輯器建立含有一些資訊的新文字檔。 在此範例中，名為 **hw.txt** 的檔案包含 "hello world" 文字。 將您的文字檔儲存在本機，並將其上傳至您的 Blob 儲存體容器。

   ![將檔案上傳至容器](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. 返回您的儲存體帳戶，然後選取 [設定]  下方的 [存取金鑰]  。 將 [儲存體帳戶名稱]  和 [金鑰 1]  複製到文字編輯器，以供稍後在本教學課程中使用。

   ![尋找儲存體帳戶存取金鑰](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>建立 Azure Key Vault 並新增秘密

1. 在 Azure 入口網站中選取 [建立資源]  ，並在搜尋方塊中輸入**金鑰保存庫**。

   ![建立 Azure 資源搜尋方塊](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. 系統會自動選取金鑰保存庫資源。 選取 [建立]  。

   ![建立金鑰保存庫資源](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. 在 [建立金鑰保存庫]  頁面上輸入下列資訊，並保留其餘欄位的預設值：

   |屬性|描述|
   |--------|-----------|
   |名稱|金鑰保存庫的唯一名稱。|
   |訂用帳戶|選擇訂用帳戶。|
   |資源群組|選擇資源群組或建立新的群組。|
   |Location|選擇位置。|

   ![Azure Key Vault 屬性](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. 提供上述資訊之後，請選取 [建立]  。 

4. 在 Azure 入口網站中瀏覽至您新建立的金鑰保存庫，然後選取 [秘密]  。 然後，選取 [+ 產生/匯入]  。 

   ![產生新的金鑰保存庫祕密](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. 在 [建立祕密]  頁面上提供下列資訊，並保留其餘欄位的預設值：

   |屬性|值|
   |--------|-----------|
   |上傳選項|手動|
   |名稱|儲存體帳戶金鑰的易記名稱。|
   |值|您儲存體帳戶中的 key1。|

   ![新金鑰保存庫祕密的屬性](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. 將金鑰名稱儲存在文字編輯器中以供稍後在本教學課程中使用，然後選取 [建立]  。 然後，瀏覽至 [屬性]  功能表。 將 [DNS 名稱]  和 [資源識別碼]  複製到文字編輯器，以供稍後在本教學課程中使用。

   ![複製 Azure Key Vault DNS 名稱和資源識別碼](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>建立 Azure Databricks 工作區並新增秘密範圍

1. 在 Azure 入口網站中，選取 [建立資源]   > [分析]   > [Azure Databricks]  。

    ![Azure 入口網站上的 Databricks](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. 在 [Azure Databricks 服務]  下方提供下列值，以建立 Databricks 工作區。

   |屬性  |描述  |
   |---------|---------|
   |工作區名稱     | 提供您 Databricks 工作區的名稱        |
   |訂用帳戶     | 從下拉式清單中選取您的 Azure 訂用帳戶。        |
   |資源群組     | 選取包含金鑰保存庫的相同資源群組。 |
   |Location     | 選取與您的 Azure Key Vault 相同的位置。 如需所有可用的區域，請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/services/)。        |
   |定價層     |  選擇 [標準]  或 [進階]  。 如需這些定價層的詳細資訊，請參閱 [Databricks 定價頁面](https://azure.microsoft.com/pricing/details/databricks/)。       |

   ![Databricks 工作區屬性](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   選取 [建立]  。

3. 在 Azure 入口網站中瀏覽至您新建立的 Azure Databricks 資源，然後選取 [啟動工作區]  。

   ![啟動 Azure Databricks 工作區](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. 當您的 Azure Databricks 工作區在個別視窗中開啟之後，請將 **#secrets/createScope** 附加至 URL。 URL 應具有下列格式： 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. 輸入範圍名稱，然後輸入您先前儲存的 Azure Key Vault DNS 名稱和資源識別碼。 將範圍名稱儲存在文字編輯器中，以供稍後在本教學課程中使用。 然後，選取 [Create]  \(建立\)。

   ![在 Azure Databricks 工作區中建立密碼範圍](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>從 Azure Databricks 存取您的 Blob 容器

1. 從 Azure Databricks 工作區的首頁，選取 [一般工作]  下方的 [新增叢集]  。

   ![建立新的 Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. 輸入叢集名稱，然後選取 [建立叢集]  。 建立叢集需要幾分鐘的時間。

3. 在建立叢集後，瀏覽至 Azure Databricks 工作區的首頁，並選取 [一般工作]  下方的 [新增 Notebook]  。

   ![建立新的 Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. 輸入 Notebook 名稱，並將語言設定為 Python。 將叢集設定為您在上一個步驟中建立的叢集名稱。

5. 執行下列命令，以掛接您的 Blob 儲存體容器。 請記得變更下列屬性的值：

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** 是一個 DBFS 路徑，代表將會掛接 Blob 儲存體容器或容器內的資料夾 (指定於來源中) 的位置。
   * **conf-key** 可以是 `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` 或 `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** 是您在上一節中建立的秘密範圍名稱。 
   * **key-name** 是您在金鑰保存庫中為儲存體帳戶金鑰建立的祕密名稱。

   ![在 Notebook 中建立 Blob 儲存體掛接](./media/store-secrets-azure-key-vault/command1.png)

6. 執行下列命令，將 Blob 儲存體容器中的文字檔讀取至資料框架。 變更命令中的值，以符合您的掛接名稱和檔案名稱。

   ```python
   df = spark.read.text("/mnt/<mount-name>/<file-name>")
   ```

   ![將檔案讀取至資料框架](./media/store-secrets-azure-key-vault/command2.png)

7. 使用下列命令，顯示您的檔案內容。

   ```python
   df.show()
   ```
   ![顯示資料框架](./media/store-secrets-azure-key-vault/command3.png)

8. 若要取消掛接您的 Blob 儲存體，請執行下列命令：

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![取消掛接儲存體帳戶](./media/store-secrets-azure-key-vault/command4.png)

9. 請注意，取消掛接之後，您就無法再讀取 Blob 儲存體帳戶。

   ![取消掛接儲存體帳戶錯誤](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除整個資源群組：

1. 從 Azure 入口網站的左側功能表中選取 [資源群組]  ，然後瀏覽至您的資源群組。

2. 選取 [刪除資源群組]  ，然後輸入您的資源群組名稱。 然後選取 [刪除]  。 

## <a name="next-steps"></a>後續步驟

前往下一篇文章，了解如何在有服務端點啟用 Cosmos DB 的環境中實作 VNet 插入的 Databricks。
> [!div class="nextstepaction"]
> [教學課程：使用 Cosmos DB 端點實作 Azure Databricks](service-endpoint-cosmosdb.md)
