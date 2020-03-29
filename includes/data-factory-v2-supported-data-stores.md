---
title: 包含檔案
description: 包含檔案
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 12/13/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: c4225ab2505fc072f705f8126fc8a04581e0f80c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75440343"
---
| 類別 | 資料存放區 | 支援作為來源 | 支援作為接收器 | 受 [Azure IR](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 支援 | 由[自承載的 IR](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime)支援 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob 儲存體](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 認知搜索索引](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB (SQL API)](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 宇宙 DB 的蒙戈DB API](../articles/data-factory/connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 資料總管](../articles/data-factory/connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Azure Data Lake Storage Gen1](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Storage Gen2](../articles/data-factory/connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[瑪麗亞DB的 Azure 資料庫](../articles/data-factory/connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[MySQL 的 Azure 資料庫](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[適用於 PostgreSQL 的 Azure 資料庫](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 檔存儲](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Database](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL 資料庫託管實例](../articles/data-factory/connector-azure-sql-database-managed-instance.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 突觸分析（以前的 SQL 資料倉儲）](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure 資料表儲存體](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **資料庫** |[亞馬遜紅移](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[蜂巢](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../articles/data-factory/connector-informix.md) |✓ | | |✓  |
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-microsoft-access.md) |✓ | | |✓  |
| &nbsp; |[Mysql](../articles/data-factory/connector-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Netezza](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[甲骨文](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[後格雷SQL](../articles/data-factory/connector-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Presto (預覽)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[通過開放中心進行 SAP 業務倉庫](../articles/data-factory/connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  |
| &nbsp; |[SAP Business Warehouse via MDX](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[SAP 表](../articles/data-factory/connector-sap-table.md) |✓ | | |✓  |
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[泰拉達](../articles/data-factory/connector-teradata.md) |✓ | |✓ |✓  |
| &nbsp; |[Vertica](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  |
| **Nosql** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase (預覽)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Mongodb](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  |
| **檔案** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[檔案系統](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Ftp](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[Google Cloud Storage](../articles/data-factory/connector-google-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ |✓ |✓ |✓  |
| **一般通訊協定** |[一般 HTTP](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[泛型 OData](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[一般 ODBC](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  |
| &nbsp; |[一般 REST](../articles/data-factory/connector-rest.md) |✓ | |✓ |✓  |
| **服務和應用程式** |[Amazon Marketplace Web Service](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Common Data Service](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Concur (預覽)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics AX](../articles/data-factory/connector-dynamics-ax.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Google AdWords](../articles/data-factory/connector-google-adwords.md) |✓ | |✓ |✓  |
| &nbsp; |[HubSpot (預覽)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento (預覽)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo (預覽)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[辦公室 365](../articles/data-factory/connector-office-365.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua (預覽)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Responsys (預覽)](../articles/data-factory/connector-oracle-responsys.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Service Cloud (預覽)](../articles/data-factory/connector-oracle-service-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[貝寶（預覽）](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks (預覽)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce 服務雲端](../articles/data-factory/connector-salesforce-service-cloud.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce 行銷雲端](../articles/data-factory/connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  |
| &nbsp; |[Shopify (預覽)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square (預覽)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[Web 表（HTML 表）](../articles/data-factory/connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho (預覽)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> 如果連接器標記為*預覽*，您可以試用它並向我們提供回饋。 如果要依賴解決方案中的預覽連接器，請與[Azure 支援](https://azure.microsoft.com/support/)。
