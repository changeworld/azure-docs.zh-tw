---
title: Azure Data Factory 連接器總覽
description: 瞭解 Data Factory 中支援的連接器。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: a729d470cccd4121523c767ada9077a51361c061
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181966"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory 連接器總覽

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 透過複製、資料流程、查閱、取得中繼資料和刪除活動來支援下列資料存放區和格式。 按一下每個資料存放區，以瞭解支援的功能和對應的設定詳細資料。

## <a name="supported-data-stores"></a>支援的資料存放區

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>與更多資料存放區整合

Azure Data Factory 可以觸及超過上述清單的一組更廣泛的資料存放區。 如果您需要將資料移入/移出 Azure Data Factory 內建連接器清單中的資料存放區，以下是一些可擴充的選項：
- 針對資料庫和資料倉儲，您通常可以找到對應的 ODBC 驅動程式，您可以使用 [一般 odbc 連接器](connector-odbc.md)。
- 針對 SaaS 應用程式：
    - 如果它提供 RESTful Api，您可以使用 [一般 REST 連接器](connector-rest.md)。
    - 如果它有 OData 摘要，您可以使用 [泛型 odata 連接器](connector-odata.md)。
    - 如果它提供 SOAP Api，您可以使用 [一般 HTTP 連接器](connector-http.md)。
    - 如果它有 ODBC 驅動程式，您可以使用 [一般 odbc 連接器](connector-odbc.md)。
- 針對其他人，請檢查您是否可以將資料載入至任何 ADF 支援的資料存放區，或將資料公開為數據存放區（例如 Azure Blob/File/FTP/SFTP/等），然後讓 ADF 從中挑選。 您可以透過[Azure Function](control-flow-azure-function-activity.md)、[自訂活動](transform-data-using-dotnet-custom-activity.md)、 [Databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md)、 [Web 活動](control-flow-web-activity.md)等來叫用自訂資料載入機制。

## <a name="supported-file-formats"></a>支援的檔案格式

Azure Data Factory 支援下列檔案格式。 請參閱每篇文章，以取得以格式為基礎的設定。

- [Avro 格式](format-avro.md)
- [二進位格式](format-binary.md)
- [Common Data Model 格式](format-common-data-model.md)
- [分隔符號文字格式](format-delimited-text.md)
- [差異格式](format-delta.md)
- [Excel 格式](format-excel.md)
- [JSON 格式](format-json.md)
- [ORC 格式](format-orc.md)
- [Parquet 格式](format-parquet.md)
- [XML 格式](format-xml.md)

## <a name="next-steps"></a>後續步驟

- [複製活動](copy-activity-overview.md)
- [對應資料流](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)
