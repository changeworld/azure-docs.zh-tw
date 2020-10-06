---
title: 版本資訊：Azure Synapse Analytics (工作區預覽)
description: Azure Synapse Analytics (工作區預覽) 的版本資訊
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031665"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Azure Synapse Analytics (工作區預覽) 版本資訊

本文將說明 Azure Synapse Analytics (工作區) 的限制和問題。 如需相關資訊，請參閱[什麼是 Azure Synapse Analytics (工作區)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- 問題和客戶的影響：SDK 所建立的工作區無法啟動 Synapse Studio

- 因應措施：完成下列步驟： 
  1.    執行 `az synapse workspace create` 來建立工作區。
  2.    執行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 來擷取受控識別的識別碼。
  3.    執行 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`，將工作區新增為儲存體帳戶的角色。
  4.    執行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 來新增防火牆規則。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Synapse](overview-what-is.md)
* [開始使用](get-started.md)
* [常見問題集](overview-faq.md)
