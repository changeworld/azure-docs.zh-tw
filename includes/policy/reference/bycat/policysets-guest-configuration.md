---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 212e9b1826d0245675b066ded00b03e8cff1fb96
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487036"
---
|名稱 |描述 |原則 |版本 |
|---|---|---|---|
|[稽核未安裝指定應用程式的 Linux VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_InstalledApplicationLinux.json) |此方案會部署原則需求，並針對未安裝指定應用程式的 Linux 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.1.0 |
|[稽核已安裝指定應用程式的 Linux VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_NotInstalledApplicationLinux.json) |此方案會部署原則需求，並針對已安裝指定應用程式的 Linux 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.1.0 |
|[稽核密碼安全性設定不安全的 VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettings.json) |此方案會部署原則需求，並針對使用不安全密碼安全性設定的虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |18 |1.1.0 |
|[稽核未啟用 Windows 序列主控台的 Windows Server VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsSerialConsole.json) |此方案會部署原則需求，並對未啟用 Windows 序列主控台的 Windows Server 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核其中 Administrators 群組包含了任一指定成員的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude.json) |此方案會部署原則需求，並稽核其中 Administrators 群組包含任一指定成員的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核其中 Administrators 群組不包含任何指定成員的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude.json) |此方案會部署原則需求，並稽核其中 Administrators 群組不包含所有指定成員的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核其中 Administrators 群組不只包含指定成員的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers.json) |此方案會部署原則需求，並稽核其中 Administrators 群組不只包含指定成員的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核 DSC 設定不符合規範的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDscConfiguration.json) |此方案會部署原則需求，並對其中 Desired State Configuration (DSC) 設定不相容的 Windows VM 進行稽核。 此原則僅適用於具有 WMF 4 和更新版本的機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核 Log Analytics 代理程式未如預期連線的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection.json) |此計劃會部署原則需求，並稽核 Log Analytics 代理程式未連線至指定工作區的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核遠端主機連線狀態與指定狀態不符的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsRemoteConnection.json) |此方案會部署原則需求，並稽核遠端主機連線狀態與指定狀態不符的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未安裝與「執行」指定服務的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsServiceStatus.json) |此方案會部署原則需求，並稽核未安裝與「執行」指定服務的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未加入指定網域的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDomainMembership.json) |此方案會部署原則需求，並針對未加入指定網域的 Windows 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未設定為指定時區的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsTimeZone.json) |此方案會部署原則需求，並針對未設定為指定時區的 Windows 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核憑證即將在指定天數內到期的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_CertificateExpiration.json) |此方案會部署原則需求，並稽核憑證會在指定天數內過期的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核信任根憑證中不包含指定憑證的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot.json) |此方案會部署原則需求，並稽核信任的根憑證授權單位憑證存放區 (Cert:\LocalMachine\Root) 中，不包含指定憑證的 Windows VM。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未安裝指定應用程式的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_InstalledApp.json) |此方案會部署原則需求，並針對未安裝指定應用程式的 Windows 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未在指定天數內重新啟動的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_MachineLastBootUpTime.json) |此方案會部署原則需求，並稽核未在指定天數內重新啟動的 Windows 虛擬機器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核已安裝指定應用程式的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_NotInstalledApp.json) |此方案會部署原則需求，並針對已安裝指定應用程式的 Windows 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核正在等候重新開機的 Windows VM](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPendingReboot.json) |此方案會部署原則需求，並針對重新開機作業擱置的 Windows 虛擬機器進行稽核。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[稽核未使用安全通訊協定的 Windows 網頁伺服器](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_TLS.json) |此方案會部署原則需求，並稽核未使用安全通訊協定 (TLS 1.1 或 TLS 1.2) 的 Windows 網頁伺服器。 如需有關客體設定原則的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol) |2 |1.0.0 |
|[部署必要條件，以在虛擬機器上啟用來賓設定原則](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |此計畫會新增系統指派的受控識別，並將適合平台的客體設定延伸模組部署到可由客體設定原則監視的虛擬機器。 這是所有客體設定原則的必要條件，必須先指派給原則指派範圍，才能使用任何客體設定原則。 如需有關客體設定的詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |4 |1.0.0-preview |
|[Windows 電腦應符合 Azure 安全性基準的需求](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |此方案使用不符合 Azure 安全性基準的設定來稽核 Windows 電腦。 如需詳細資訊，請參閱 [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
