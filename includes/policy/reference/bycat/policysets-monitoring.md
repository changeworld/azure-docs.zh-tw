---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e5edcfad636c76107adc7e72b50ea61249aa00a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807104"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[部署-設定必要條件，以啟用虛擬機器上的 Azure 監視器和 Azure 安全性代理程式](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |設定機器以自動安裝 Azure 監視器和 Azure 安全性代理程式。 「安全性中心」會從代理程式收集事件，並使用這些事件來提供安全性警示，並 (建議) 量身訂做的強化工作。 在與電腦相同的區域中建立資源群組和 Log Analytics 工作區，以儲存審核記錄。 此原則僅適用于幾個區域中的 Vm。 |5 |1.0.0-preview |
|[為虛擬機器擴展集啟用 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器擴展集的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 注意：如果您的擴展集 upgradePolicy 設為 Manual，您必須透過呼叫升級，將擴充功能套用至集合中的所有虛擬機器。 在 CLI 中，這會是 az vmss update-instances。 |6 |1.0.1 |
|[啟用適用於 VM 的 Azure 監視器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定的範圍 (管理群組、訂用帳戶或資源群組) 中啟用適用於虛擬機器 (VM) 的 Azure 監視器。 請使用 Log Analytics 工作區作為參數。 |10 |2.0.0 |
