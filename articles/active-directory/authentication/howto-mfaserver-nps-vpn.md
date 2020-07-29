---
title: Azure MFA Server 和協力廠商 Vpn-Azure Active Directory
description: Azure MFA Server 與 Cisco、Citrix 和刺柏整合的逐步設定指南。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652854"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>使用 Azure MFA Server 和協力廠商 VPN 解決方案的先進案例

Azure 多因素驗證服務器（Azure MFA Server）可以用來與各種協力廠商 VPN 解決方案順暢地連接。 本文著重于 Cisco &reg; ASA VPN 應用裝置、Citrix NETSCALER SSL VPN 應用裝置，以及網路安全存取/脈衝安全 Connect 安全 SSL VPN 設備。 我們建立了解決這三種常見應用裝置的設定指南。 Azure MFA Server 也可以與大部分其他使用 RADIUS、LDAP、IIS 或宣告式驗證的系統進行整合，以 AD FS。 您可以在[AZURE MFA Server](howto-mfaserver-deploy.md#next-steps)設定中找到更多詳細資料。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 不再為新的部署提供 MFA 伺服器。 希望要求使用者使用多重要素驗證的新客戶應該使用雲端式 Azure Multi-Factor Authentication。 在 7 月 1 日前啟用 MFA 伺服器的現有客戶，將能夠下載最新版本及未來的更新，並如常產生啟用認證。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN 應用裝置和 Azure MFA Server
Azure MFA Server 會與您的 Cisco &reg; ASA VPN 應用裝置整合，以提供 Cisco AnyConnect vpn 登入 &reg; 和入口網站存取的額外安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | Description |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Cisco ASA VPN 應用裝置與 Azure MFA |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Cisco ASA VPN 應用裝置與 Azure MFA |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 與 Azure MFA Server
Azure MFA Server 會與您的 Citrix NetScaler SSL VPN 應用裝置整合，以提供 Citrix NetScaler SSL VPN 登入和入口網站存取的額外安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Citrix NetScaler SSL VPN 與 Azure MFA |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Citrix NetScaler SSL VPN 應用裝置與 Azure MFA |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>刺柏/脈衝安全 SSL VPN 應用裝置和 Azure MFA Server
Azure MFA Server 會與您的刺柏/脈衝安全 SSL VPN 應用裝置整合，以提供更高的安全，以進行刺柏/脈衝安全 SSL VPN 登入和入口網站存取。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Juniper/Pulse Secure SSL VPN 與 Azure MFA |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Juniper/Pulse Secure SSL VPN 應用裝置與 Azure MFA |

## <a name="next-steps"></a>後續步驟

- [利用 Azure Multi-Factor Authentication 的 NPS 擴充功能強化現有的驗證基礎結構](howto-mfa-nps-extension.md)

- [設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
