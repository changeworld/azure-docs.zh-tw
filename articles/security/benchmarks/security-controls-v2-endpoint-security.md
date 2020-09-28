---
title: Azure 安全性基準測試 V2-端點安全性
description: Azure 安全性基準測試 V2 端點安全性
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 452b1a33520309ae696ab318c034f0186c993fdc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402917"
---
# <a name="security-control-v2-endpoint-security"></a>安全性控制 V2：端點安全性

端點安全性涵蓋端點偵測和回應中的控制項。 這包括針對 Azure 環境中的端點，使用端點偵測和回應 (EDR) 和反惡意程式碼服務。

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用端點偵測和回應 (EDR) 

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| ES-1 | 8.1 | SI-2，SI-3，SC-3 |

針對伺服器和用戶端啟用端點偵測和回應 (EDR) 功能，並與 SIEM 和安全性作業進程整合。

Microsoft Defender 進階威脅防護將 EDR 功能提供為 enterprise endpoint security 平臺的一部分，以防止、偵測、調查及回應 Advanced 威脅。 

- [Microsoft Defender 進階威脅防護總覽](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [適用于 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [適用于非 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反惡意程式碼軟體

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| ES-2 | 8.1 | SI-2，SI-3，SC-3 |

使用支援即時和定期掃描的集中管理端點反惡意程式碼解決方案

Azure 資訊安全中心可以自動識別您的虛擬機器使用許多常用的反惡意程式碼解決方案，並報告 endpoint protection 執行狀態並提出建議。 

適用于 Azure 雲端服務的 Microsoft Antimalware 是 Windows 虛擬機器 (Vm) 的預設反惡意程式碼。 針對 Linux Vm，請使用協力廠商反惡意程式碼解決方案。  此外，您也可以使用 Azure 資訊安全中心的資料服務威脅偵測，來偵測上傳至 Azure 儲存體帳戶的惡意程式碼。 

- [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](../fundamentals/antimalware.md)

- [支援的端點保護解決方案](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3：確保反惡意程式碼軟體和簽章已更新

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| ES-3 | 8.2 | SI-2，SI-3 |

確保以快速且一致的方式更新反惡意程式碼簽章。 

遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有端點都是最新的簽章。 Microsoft Antimalware 預設會自動安裝最新的簽章和引擎更新。 若為 Linux，請使用協力廠商反惡意程式碼解決方案。

- [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../fundamentals/antimalware.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [基礎結構和端點安全性](/azure/cloud-adoption-framework/organize/cloud-security)

- [威脅情報](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Azure 資訊安全中心中的 Endpoint protection 評定和建議](../../security-center/security-center-endpoint-protection.md)
