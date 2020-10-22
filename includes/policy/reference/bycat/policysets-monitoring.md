---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dc886c780a5400128f0e7396743c71e0584676cb
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92311594"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[為虛擬機器擴展集啟用 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器擴展集的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果您的擴展集 upgradePolicy 設為 Manual，您必須透過呼叫升級，將擴充功能套用至集合中的所有虛擬機器。 在 CLI 中，這會是 az vmss update-instances。 |6 |1.0.1 |
|[啟用適用於 VM 的 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器 (VM) 的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |10 |2.0.0 |
