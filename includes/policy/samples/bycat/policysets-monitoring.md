---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a1e58ebf1ec22bf05b07cbbb95e335b85e3690f4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639377"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[為虛擬機器擴展集啟用 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器擴展集的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果您的擴展集 upgradePolicy 設為 Manual，您必須透過呼叫升級，將擴充功能套用至集合中的所有虛擬機器。 在 CLI 中，這會是 az vmss update-instances。 |6 |1.0.1 |
|[啟用適用於 VM 的 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器 (VM) 的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |6 |1.0.1 |
