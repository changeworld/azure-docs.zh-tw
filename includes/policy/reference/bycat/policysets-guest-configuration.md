---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3f71eb1045f08a2eb6121c8c1c2ce86685606acf
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986903"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[稽核密碼安全性設定不安全的電腦](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |此方案會部署原則需求，並針對使用不安全密碼安全性設定的電腦進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[部署必要條件，以在虛擬機器上啟用來賓設定原則](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |此計畫會新增系統指派的受控識別，並將適合平台的客體設定延伸模組部署到可由客體設定原則監視的虛擬機器。 這是所有客體設定原則的必要條件，必須先指派給原則指派範圍，才能使用任何客體設定原則。 如需有關客體設定的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |4 |1.0.0-preview |
|[Windows 電腦應符合 Azure 安全性基準的需求](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |此方案使用不符合 Azure 安全性基準的設定來稽核 Windows 電腦。 如需詳細資訊，請參閱 [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
