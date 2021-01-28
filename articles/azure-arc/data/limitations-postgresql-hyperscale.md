---
title: Azure Arc enabled 于 postgresql 超大規模的限制
description: Azure Arc enabled 于 postgresql 超大規模的限制
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 01/21/2021
ms.topic: how-to
ms.openlocfilehash: ecf3c1f0d553c6521178fa5e1f67fc1cf95de73e
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987042"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled 于 postgresql 超大規模的限制

本文描述啟用 Azure Arc 于 postgresql 超大規模的限制。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>備份與還原

- 「時間點還原」 (例如不支援還原到特定的日期和時間) 至相同的伺服器群組。 執行時間點還原時，您必須在還原之前部署的不同伺服器群組上還原。 還原至新的伺服器群組之後，您可以刪除來源的伺服器群組。
- 于 postgresql 第12版支援還原 (備份的整個內容，而不是還原到特定的時間點) 至相同的伺服器群組。 因為于 postgresql 引擎與時間軸的限制，所以不支援于 postgresql 第11版。 若要針對第11版的于 postgresql 伺服器群組還原備份的整個內容，您必須將它還原至不同的伺服器群組。


## <a name="databases"></a>資料庫

不支援在伺服器群組中裝載一個以上的資料庫。


## <a name="security"></a>安全性

不支援管理使用者和角色。 現在，請繼續使用 postgres 標準使用者。

## <a name="roles-and-responsibilities"></a>角色和職責

Microsoft 及其客戶之間的角色和責任，在 Azure PaaS 服務 (平臺即服務) 和 Azure 混合式 (（例如 Azure Arc 啟用的于 postgresql 超大規模) ）之間有所不同。 

### <a name="frequently-asked-questions"></a>常見問題集

下表摘要說明有關支援角色和責任之常見問題的解答。

| 問題                      | Azure 平臺即服務 (PaaS)  | Azure Arc 混合式服務 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 誰提供基礎結構？  | Microsoft                          | 客戶                  |
| 誰提供軟體？ *       | Microsoft                          | Microsoft                 |
| 誰負責作業？ | Microsoft                          | 客戶                  |
| Microsoft 是否提供 Sla？      | 是                                | 否                        |
| 誰負責 Sla？ | Microsoft                          | 客戶                  |

\* Azure 服務

__為什麼 Microsoft 不提供 Azure Arc 混合式服務的 Sla？__ 因為 Microsoft 並未擁有基礎結構，所以無法操作它。 客戶的做法。

## <a name="next-steps"></a>下一步

- **試試看。** 快速開始使用 [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 快速入門 AZURE KUBERNETES SERVICE (AKS) 、AWS 彈性 Kubernetes 服務 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或 Azure VM。 

- **建立您自己的。** 請遵循下列步驟，在您自己的 Kubernetes 叢集上建立： 
   1. [安裝用戶端工具](install-client-tools.md)
   2. [建立 Azure Arc 資料控制器](create-data-controller.md)
   3. [在 Azure Arc 上建立適用於 PostgreSQL 的 Azure 資料庫超大規模伺服器群組](create-postgresql-hyperscale-server-group.md) 

- **學習**
   - [深入瞭解 Azure Arc 啟用的資料服務](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [瞭解 Azure Arc](https://aka.ms/azurearc)
