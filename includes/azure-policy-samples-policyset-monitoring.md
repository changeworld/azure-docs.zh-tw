---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/19/2020
ms.author: dacoulte
ms.openlocfilehash: b545e9a33c035ac2556c03efc5bc5c03fdc4b115
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495440"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[為 VM 擴展集 (VMSS) 啟用 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於 VM 擴展集的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果您的擴展集 upgradePolicy 設為 Manual，您必須透過呼叫升級，將擴充功能套用至集合中的所有虛擬機器。 在 CLI 中，這會是 az vmss update-instances。 |6 |1.0.0-preview |
|[啟用適用於 VM 的 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器 (VM) 的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |6 |1.0.0-preview |
