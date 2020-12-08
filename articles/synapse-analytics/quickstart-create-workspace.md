---
title: 快速入門：建立 Synapse 工作區
description: 遵循此指南中的步驟建立 Synapse 工作區。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: fb7672a0d7bdd14415a51f2296c281e92cf5542a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450657"
---
# <a name="quickstart-create-a-synapse-workspace"></a>快速入門：建立 Synapse 工作區
本快速入門說明如何使用 Azure 入口網站來建立 Azure Synapse 工作區。

## <a name="create-a-synapse-workspace"></a>建立 Synapse 工作區

1. 開啟 [Azure 入口網站](https://portal.azure.com)，並在頂端搜尋 **Synapse**。
1. 在 [服務] 底下的搜尋結果中，選取 [Azure Synapse Analytics]。
1. 選取 [新增] 以建立工作區。
1. 在 [基本項目] 索引標籤中，為工作區提供唯一的名稱。 在本文件中，我們會使用 **myworkspace**
1. 您需要 ADLSGEN2 帳戶來建立工作區。 最簡單的選擇是建立新的帳戶。 如果想要重複使用現有的帳戶，則必須執行一些額外的設定。 
1. 選項 1 建立新的 ADLSGEN2 帳戶 
    1. 在 **選取 Data Lake Storage Gen 2** 中按一下 [建立新的] 並命名為 **contosolake**。
    1. 在 **選取 Data Lake Storage Gen 2** 中按一下 [檔案系統] 並命名為 **users**。
1. 選項 2 請參閱本文件末尾的 **準備儲存體帳戶** 指示。
1. 您的 Azure Synapse 工作區會使用此儲存體帳戶作為「主要」儲存體帳戶，以及用來儲存工作區資料的容器。 工作區會將資料儲存在 Apache Spark 資料表中。 其會將 Spark 應用程式記錄儲存在名為 **/synapse/workspacename** 的資料夾底下。
1. 選取 [檢閱+建立] > [建立]。 您的工作區將會在幾分鐘內就緒。

> [!NOTE]
> 建立 Azure Synapse 工作區之後，您將無法將工作區移至另一個 Azure Active Directory 租用戶。 如果您透過訂用帳戶移轉或其他動作來這麼做，您可能會無法存取工作區中的成品。  

## <a name="open-synapse-studio"></a>開啟 Synapse Studio

建立 Azure Synapse 工作區之後，有兩種方式可以開啟 Synapse Studio：

* 在 [Azure 入口網站](https://portal.azure.com)中開啟 Synapse 工作區。 在 [概觀] 區段的頂端，選取 [啟動 Synapse Studio]。
* 移至 `https://web.azuresynapse.net` 並登入您的工作區。

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>準備要與 Azure Synapse Analytics 搭配使用的現有儲存體帳戶

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至現有的 ADLSGEN2 儲存體帳戶
1. 在左窗格上選取 [存取控制 (IAM)]。 然後指派下列角色，或確定其已指派：
    * 將您自己指派給 **擁有者** 角色。
    * 將您自己指派給 **儲存體 Blob 資料擁有者** 角色。
1. 在左側窗格中，選取 [容器] 並建立容器。
1. 您可以為容器提供任何名稱。 在本文件中，我們會將容器命名為 **users**。
1. 接受 [公用存取層級] 的預設設定，然後選取 [建立]。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>從您的工作區設定儲存體帳戶的存取權

Azure Synapse 工作區的受控身分識別可能已經有儲存體帳戶的存取權。 請依照下列步驟來確認：

1. 開啟 [Azure 入口網站](https://portal.azure.com)，然後開啟為您工作區選擇的主要儲存體帳戶。
1. 從左窗格中選取 [存取控制 (IAM)]。
1. 指派下列角色，或確定其已指派。 我們使用相同名稱作為工作區身分識別和工作區名稱。
    * 針對儲存體帳戶上的 [儲存體 Blob 資料參與者] 角色，請指派 **myworkspace** 作為工作區身分識別。
    * 指派 **myworkspace** 作為工作區名稱。

1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

* [建立專用 SQL 集區](quickstart-create-sql-pool-studio.md) 
* [建立無伺服器 Apache Spark 集區](quickstart-create-apache-spark-pool-portal.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)
