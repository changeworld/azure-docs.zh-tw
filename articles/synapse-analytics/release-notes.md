---
title: 版本資訊
description: Azure Synapse Analytics (工作區) 的版本資訊
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658495"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Azure Synapse Analytics (預覽) 版本資訊

本文將說明 Azure Synapse Analytics (工作區) 的限制和問題。 如需相關資訊，請參閱[什麼是 Azure Synapse Analytics (工作區)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (工作區) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- 問題和客戶的影響：SDK 所建立的工作區無法啟動 Synapse Studio

- 因應措施：完成下列步驟： 
  1.    執行 `az synapse workspace create` 來建立工作區。
  2.    執行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 來擷取受控識別的識別碼。
  3.    執行 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`，將工作區新增為儲存體帳戶的角色。
  4.    執行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 來新增防火牆規則。

## <a name="next-steps"></a>後續步驟

* [建立工作區](quickstart-create-workspace.md)
* [使用 Synapse Studio](quickstart-synapse-studio.md)
* [建立 SQL 集區](quickstart-create-sql-pool-portal.md)
* [使用 SQL 隨選](quickstart-sql-on-demand.md)
* [建立 Apache Spark 集區](quickstart-create-apache-spark-pool-portal.md)