---
title: 疑難排解： SDK 建立的工作區無法啟動 Synapse Studio
description: 解析 SDK 所建立工作區的步驟無法啟動 Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466171"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>針對使用 SDK 建立的 Azure Synapse Analytics 工作區進行疑難排解

本文提供從使用軟體發展工具組 (SDK) 所建立的 Synapse 工作區啟動 Synapse Studio 的疑難排解步驟。


## <a name="prerequisites"></a>必要條件

- 使用 SDK 建立的 Synapse 工作區

## <a name="workaround"></a>因應措施

若要從 SDK 建立的工作區啟動 Synapse Studio，請完成下列步驟： 
  1.    執行 `az synapse workspace create` 來建立工作區。
  2.    執行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 來擷取受控識別的識別碼。
  3.    藉由執行，將儲存體 Blob 資料參與者角色授與受控識別儲存體帳戶 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    執行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 來新增防火牆規則。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Synapse](../overview-what-is.md)
* [開始使用](../get-started.md)
