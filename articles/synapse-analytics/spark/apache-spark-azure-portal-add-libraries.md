---
title: 在 Azure Synapse Analytics 中管理 Apache Spark 的程式庫
description: 瞭解如何在 Azure Synapse Analytics 中新增和管理 Apache Spark 所使用的程式庫。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: ad3231652056244fdfc814251e6caad025db77e5
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649893"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理 Apache Spark 的程式庫

程式庫提供可重複使用的程式碼，您可能會想要將它包含在程式或專案中。 若要讓您的應用程式可以使用協力廠商或本機建立的程式碼，您可以將程式庫安裝到您的其中一個 Spark 集區 (預覽) 。 一旦為 Spark 集區安裝程式庫之後，就可以使用相同集區的所有會話使用該程式庫。 

## <a name="default-installation"></a>預設安裝
Azure Synapse Analytics 中的 Apache Spark 有完整的 Anacondas 安裝以及額外的程式庫。 您可以在 [Apache Spark 版本支援](apache-spark-version-support.md)中找到完整的程式庫清單。 

當 Spark 實例啟動時，就會自動包含這些程式庫。 您可以在 Spark 集區新增額外的 Python 和自訂的套件 (預覽版) 層級。


## <a name="manage-python-packages"></a>管理 Python 套件
一旦識別出您想要用於 Spark 應用程式的程式庫，您就可以將它們安裝到 Spark 集區 (預覽) 。 

 您可以使用命令) 的 *requirements.txt* 檔案 (輸出， `pip freeze` 以升級虛擬環境。 在啟動集區時，會從 PyPi 下載此檔案中所列的套件以供安裝或升級。 每次從該 Spark 集區建立 Spark 實例時，就會使用此需求檔案。

> [!IMPORTANT]
> - 如果您要安裝的套件很大或需要很長的時間才能安裝，這會影響 Spark 實例的啟動時間。
> - 不支援在安裝時需要編譯器支援的套件，例如 GCC。
> - 封裝不能降級，只能新增或升級。

### <a name="requirements-format"></a>需求格式

下列程式碼片段顯示需求檔案的格式。 PyPi 套件名稱會與完全相同的版本一起列出。 此檔案遵循 [pip 凍結](https://pip.pypa.io/en/stable/reference/pip_freeze/) 參考檔中所述的格式。 此範例會釘選特定版本。 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>安裝 Python 套件
當您開發 Spark 應用程式時，可能會發現您需要更新現有的程式庫或安裝新的程式庫。 您可以在建立集區期間或之後更新程式庫。

#### <a name="install-packages-during-pool-creation"></a>在集區建立期間安裝套件
若要在集區建立期間將程式庫安裝到 Spark 集區 (預覽版) ：
   
1. 從 Azure 入口網站流覽至您的 Azure Synapse Analytics 工作區。
   
2. 選取 [ **建立 Apache Spark 集** 區]，然後選取 [ **其他設定** ] 索引標籤。 
   
3. 使用頁面 **封裝** 區段中的檔案選取器，上傳環境設定檔。 
   
![在集區建立期間新增 Python 程式庫](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "新增 Python 程式庫")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>從 Synapse 工作區安裝套件
若要從 Azure Synapse Analytics 入口網站更新或新增其他程式庫至 Spark 集區 (預覽) ：

1.  從 Azure 入口網站流覽至您的 Azure Synapse Analytics 工作區。
   
2.  從 Azure 入口網站啟動您的 Azure Synapse Analytics 工作區。

3.  從主要導覽面板中選取 [ **管理** ]，然後選取 [ **Apache Spark**集區]。
   
4. 選取單一 Spark 集區，並使用頁面  **封裝** 區段中的檔案選取器來上傳環境設定檔。

![在 synapse 中新增 Python 程式庫](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "新增 Python 程式庫")
   
#### <a name="install-packages-from-the-azure-portal"></a>從 Azure 入口網站安裝套件
若要將程式庫安裝到 Spark 集區 (預覽) 直接從 Azure 入口網站：
   
 1. 從 Azure 入口網站流覽至您的 Azure Synapse Analytics 工作區。
   
 2. 在 [ **Synapse resources** ] 區段下，選取 [ **Apache Spark** 集區] 索引標籤，並從清單中選取 Spark 集區。
   
 3. 從 Spark 集區的 [**設定**] 區段中選取 [**套件**]。 

 4. 使用檔案選取器上傳環境設定檔。

![醒目顯示 [上傳環境設定檔] 按鈕的螢幕擷取畫面。](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "新增 Python 程式庫")

### <a name="verify-installed-libraries"></a>確認已安裝的程式庫

若要確認是否已安裝正確的正確程式庫版本，請執行下列程式碼

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>更新 Python 套件
您可以隨時在會話之間新增或修改封裝。 上傳新的封裝設定檔時，這將會覆寫現有的套件和版本。  

若要更新或卸載程式庫：
1. 流覽至您的 Azure Synapse Analytics 工作區。 

2. 使用 Azure 入口網站或 Azure Synapse 工作區，選取您要更新的 **Apache Spark 集** 區。

3. 流覽至 [ **套件** ] 區段，然後上傳新的環境設定檔
   
4. 儲存變更之後，您將需要結束使用中的會話，並讓集區重新開機。 （選擇性）您可以選取核取方塊來強制進行 **新的設定**，以強制結束使用中的會話。

![新增 Python 程式庫](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "新增 Python 程式庫")
   

> [!IMPORTANT]
> 藉由選取強制執行 **新設定**的選項，您將會結束所選 Spark 集區的所有目前會話。 一旦會話結束，您就必須等待集區重新開機。 
>
> 如果未選取此設定，您將必須等待目前的 Spark 會話結束或手動停止。 一旦會話結束，您將需要讓集區重新開機。 


## <a name="manage-a-python-wheel"></a>管理 Python 輪子

### <a name="install-a-custom-wheel-file"></a>安裝自訂滾輪檔案
您可以將所有滾輪檔案上傳至與 Synapse 工作區連結的 Azure Data Lake Storage (Gen2) 帳戶，以將自訂的內建滾輪套件安裝在 Apache Spark 集區上。 

檔案應上傳至儲存體帳戶的預設容器中的下列路徑： 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>可以在會話之間新增或修改自訂套件。 不過，您必須等候集區和會話重新開機，以查看更新的套件。

## <a name="next-steps"></a>後續步驟
- 查看預設程式庫： [Apache Spark 版本支援](apache-spark-version-support.md)
