---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170096"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[允許的虛擬機器 SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |此原則可讓您指定一組組織所能部署的虛擬機器 SKU。 |拒絕 |1.0.0 |
|[稽核未設定災害復原的虛擬機器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |稽核未設定災害復原的虛擬機器。 若要深入了解災害復原，請造訪 https://aka.ms/asr-doc 。 |auditIfNotExists |1.0.0 |
|[稽核不是使用受控磁碟的 VM](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |此原則會稽核不是使用受控磁碟的 VM |稽核 |1.0.0 |
|[為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |此原則會在 VM 未設定反惡意程式碼軟體延伸模組時，部署預設組態的 Microsoft IaaSAntimalware 延伸模組。 |deployIfNotExists |1.0.0 |
|[應在虛擬機器擴展集中啟用診斷記錄](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |建議啟用記錄，以在發生事件或外洩需要調查時，能夠重新建立活動線索。 |AuditIfNotExists, Disabled |1.0.0 |
|[Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |此原則會稽核未設定自動更新 Microsoft Antimalware 保護簽章的任何 Windows 虛擬機器。 |AuditIfNotExists, Disabled |1.0.0 |
|[Microsoft IaaSAntimalware 延伸模組應該部署在 Windows 伺服器上](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |此原則會稽核任何未部署 Microsoft IaaSAntimalware 延伸模組的 Windows 伺服器 VM。 |AuditIfNotExists, Disabled |1.0.0 |
|[僅應安裝已核准的 VM 擴充功能](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |此原則會控管未核准的虛擬機器延伸模組。 |Audit, Deny, Disabled |1.0.0 |
|[虛擬機器擴展集需要自動 OS 映像修補](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |此原則會強制啟用虛擬機器擴展集上的自動 OS 映像修補，以藉由每月安全地套用最新安全性修補程式，隨時保持虛擬機器安全無虞。 |deny |1.0.0 |
|[未連結的磁碟應加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |此原則會稽核任何未啟用加密的未連結磁碟。 |Audit, Disabled |1.0.0 |
|[虛擬機器應遷移到新的 Azure Resource Manager 資源](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |在您的虛擬機器使用新的 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 ARM 為基礎的部署及治理、受控識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆 |Audit, Deny, Disabled |1.0.0 |
