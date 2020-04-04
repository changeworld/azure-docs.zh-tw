---
title: Azure MFA 伺服器和第三方 VPN - Azure 活動目錄
description: Azure MFA 伺服器的分步配置指南,以便與思科、Citrix 和瞻聯網路集成。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652854"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>使用 Azure MFA 伺服器和第三方 VPN 解決方案的進階機制

Azure 多重身份驗證伺服器(Azure MFA 伺服器)可用於與各種第三方 VPN 解決方案無縫連接。 本文重點介紹 Cisco&reg; ASA VPN 設備、Citrix NetScaler SSL VPN 設備以及瞻博網路安全訪問/脈衝安全連接安全 SSL VPN 設備。 我們建立了解決這三種常見應用裝置的設定指南。 Azure MFA 伺服器還可以與大多數使用 RADIUS、LDAP、IIS 或基於聲明的 AD FS 身份驗證的其他系統集成。 您可以在[Azure MFA 伺服器配置中找到](howto-mfaserver-deploy.md#next-steps)更多詳細資訊。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起,Microsoft 將不再為新部署提供 MFA 伺服器。 希望用戶進行多重身份驗證的新客戶應使用基於雲的 Azure 多重身份驗證。 在 7 月 1 日之前啟動 MFA 伺服器的現有客戶將能夠像往常一樣下載最新版本、將來的更新並生成啟動認證。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>思科 ASA VPN 裝置和 Azure MFA 伺服器
Azure MFA 伺服器與您的&reg;Cisco ASA VPN&reg;設備整合,為思科 AnyConnect VPN 登錄和門戶訪問提供額外的安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Cisco ASA VPN 應用裝置與 Azure MFA |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Cisco ASA VPN 應用裝置與 Azure MFA |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 與 Azure MFA 伺服器
Azure MFA 伺服器與您的 Citrix NetScaler SSL VPN 裝置整合,為 Citrix NetScaler SSL VPN 登入和門戶造訪提供額外的安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Citrix NetScaler SSL VPN 與 Azure MFA |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Citrix NetScaler SSL VPN 應用裝置與 Azure MFA |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>瞻比器/脈衝安全 SSL VPN 裝置和 Azure MFA 伺服器
Azure MFA 伺服器與您的瞻飛器/脈衝安全 SSL VPN 設備整合,為瞻飛器/脈衝安全 SSL VPN 登錄和門戶訪問提供額外的安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Juniper/Pulse Secure SSL VPN 與 Azure MFA |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Juniper/Pulse Secure SSL VPN 應用裝置與 Azure MFA |

## <a name="next-steps"></a>後續步驟

- [利用 Azure Multi-Factor Authentication 的 NPS 擴充功能強化現有的驗證基礎結構](howto-mfa-nps-extension.md)

- [設定 Azure 多重身份驗證設定](howto-mfa-mfasettings.md)
