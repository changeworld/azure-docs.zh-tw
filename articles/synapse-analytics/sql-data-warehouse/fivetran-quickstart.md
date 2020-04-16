---
title: 快速入門:五元和數據倉庫
description: 開始使用 Fivetran 和 Azure 同步分析數據倉庫。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8f164232a3b1782511758f93a9e9b8d17d3714d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414279"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>快速入門:帶有資料倉庫的五個tran 

此快速入門介紹如何設置新的 Fivetran 使用者,以便使用使用 SQL 池預配的 Azure Synapse 分析數據倉庫。 本文假定您有一個現有數據倉庫。

## <a name="set-up-a-connection"></a>設定連線

1. 尋找用於連接到資料倉庫的完全限定的伺服器名稱和資料庫名稱。
    
    如果您需要尋找此資訊的說明,請參考[到資料主目錄](../sql/connect-overview.md)。

2. 在安裝精靈中，選擇直接連線到您的資料庫或使用 SSH 通道來連線。

   若您選擇直接連線到您的資料庫，則必須建立防火牆規則以允許存取。 這是最簡單、安全的方法。

   如果選擇使用 SSH 隧道進行連接,Fivetran 將連接到網路上的獨立伺服器。 伺服器會提供可連到您資料庫的 SSH 通道。 若您的資料庫位於虛擬網路上無法存取的子網路中，則必須使用此方法。

3. 將 IP 位址**52.0.2.4**添加到伺服器級防火牆,以允許從 Fivetran 傳入數據倉庫實例。

   如需詳細資訊，請參閱[建立伺服器層級防火牆規則](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)。

## <a name="set-up-user-credentials"></a>設定使用者認證

1. 使用 SQL 伺服器管理工作室 (SSMS) 或您喜歡的工具連接到數據倉庫。 以伺服器系統管理員使用者的身分登入。 接著，執行下列 SQL 命令以建立 Fivetran 使用者：

    - 在 master 資料庫中： 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - 在資料倉儲資料庫中:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. 新增以下的權限到您的資料主目錄:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    若要建立資料庫範圍認證 (在使用者從使用 PolyBase 的 Azure Blob 儲存體載入檔案時會用到)，您需要 CONTROL 權限。

3. 新增適當的資源類別到該 Fivetran 使用者。 您使用的資源類別取決於建立資料行存放區索引所需的記憶體。 例如，與 Marketo 和 Salesforce 等產品的整合需要較高的資源類別，因為這些產品使用大量資料行與較大的資料量。 較高的資源類別需要更多記憶體以建立資料行存放區索引。

    建議您使用靜態資源類別。 您可以從 `staticrc20` 資源類別開始。 `staticrc20` 資源類別會為每個使用者配置 200 MB，不論您使用的效能等級為何。 如果初始資源類別的資料行存放區索引編製作業失敗，請增加資源類別。

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    如需詳細資訊，請參閱[記憶體和並行限制](memory-concurrency-limits.md)與[資源類別](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)。


## <a name="connect-from-fivetran"></a>從五通連線

要從 Fivetran 帳號連接到資料倉儲,請輸入用於存取資料主目錄的認證: 

* 主機 (您的伺服器名稱)。
* 連接埠。
* 資料庫。
* 使用者(使用者名應為**五\@server_name,***其中server_name*是 Azure 主機 URI 的一部分:**_\_伺服器 名稱_.database.windows.net**)。
* 密碼。
