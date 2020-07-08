---
title: 快速入門：Fivetran 和資料倉儲
description: 開始使用 Fivetran 和 Azure Synapse Analytics 資料倉儲。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201645"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>快速入門：Fivetran 搭配資料倉儲使用 

此快速入門說明如何設定新的 Fivetran 使用者，以搭配透過 SQL 集區佈建之 Azure Synapse Analytics 資料倉儲使用。 此文章假設您已有現有的資料倉儲。

## <a name="set-up-a-connection"></a>設定連線

1. 尋找您用來連線到資料倉儲的完整伺服器名稱與資料庫名稱。
    
    若您需要有關尋找此資訊的協助，請參閱[連線到您的資料倉儲](../sql/connect-overview.md)。

2. 在安裝精靈中，選擇直接連線到您的資料庫或使用 SSH 通道來連線。

   若您選擇直接連線到您的資料庫，則必須建立防火牆規則以允許存取。 這是最簡單、安全的方法。

   若您選擇使用 SSH 通道來連線，Fivetran 會連線到您網路上的獨立伺服器。 伺服器會提供可連到您資料庫的 SSH 通道。 若您的資料庫位於虛擬網路上無法存取的子網路中，則必須使用此方法。

3. 在伺服器層級防火牆中新增 IP 位址 **52.0.2.4**，以允許從 Fivetran 到資料倉儲執行個體的連入連線。

   如需詳細資訊，請參閱[建立伺服器層級防火牆規則](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)。

## <a name="set-up-user-credentials"></a>設定使用者認證

1. 使用 SQL Server Management Studio (SSMS) 或您偏好的工具連線到您的資料倉儲。 以伺服器系統管理員使用者的身分登入。 接著，執行下列 SQL 命令以建立 Fivetran 使用者：

    - 在 master 資料庫中： 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - 在資料倉儲資料庫中：

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. 為 Fivetran 使用者授與對您資料倉儲的下列權限：

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


## <a name="connect-from-fivetran"></a>從 Fivetran 連線

若要從您的 Fivetran 帳戶連線到您的資料倉儲，請輸入您用來存取資料倉儲的認證： 

* 主機 (您的伺服器名稱)。
* 連接埠。
* 資料庫。
* 使用者 (使用者名稱必須是 **fivetran\@_server_name_**，其中 *server_name* 是您 Azure 主機 URI 的一部分：**_server\_name_.database.windows.net**)。
* 密碼。
