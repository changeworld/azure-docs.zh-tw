---
title: Azure MFA 伺服器和協力廠商 Vpn-Azure Active Directory
description: Azure MFA 伺服器整合 Cisco、Citrix 和刺柏的逐步設定指南。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d28ecfeb9312a6bf5a0b37e07919f9d781de50ea
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968577"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>使用 Azure MFA 伺服器和協力廠商 VPN 解決方案的先進案例

Azure Multi-Factor Authentication Server (Azure MFA 伺服器) 可以用來與各種協力廠商 VPN 解決方案緊密連接。 本文著重于 Cisco &reg; ASA VPN 應用裝置、Citrix NETSCALER SSL VPN 設備和刺柏網路安全存取/脈衝安全連線安全 SSL vpn 設備。 我們建立了解決這三種常見應用裝置的設定指南。 Azure MFA 伺服器也可以與其他使用 RADIUS、LDAP、IIS 或宣告式驗證的系統整合，以 AD FS。 您可以在 [Azure MFA 伺服器](howto-mfaserver-deploy.md#next-steps)設定中找到更多詳細資料。

> [!IMPORTANT]
> 從2019年7月1日起，Microsoft 不再為新的部署提供 MFA Server。 想要在登入事件期間 (MFA) 要求多重要素驗證的新客戶應該使用雲端式 Azure Multi-Factor Authentication。
>
> 若要開始使用雲端式 MFA，請參閱 [教學課程：使用 Azure 保護使用者登入事件 Multi-Factor Authentication](tutorial-enable-azure-mfa.md)。
>
> 如果您使用以雲端為基礎的 MFA，請參閱 [將您的 VPN 基礎結構與 Azure MFA 整合](howto-mfa-nps-extension-vpn.md)。
>
> 在2019年7月1日前啟用 MFA Server 的現有客戶，可以下載最新版本、未來的更新，並照常產生啟用認證。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN 設備和 Azure MFA 伺服器
Azure MFA 伺服器與 Cisco &reg; ASA vpn 應用裝置整合，以提供 Cisco AnyConnect vpn 登入 &reg; 和入口網站存取的額外安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Cisco ASA VPN 應用裝置與 Azure MFA |
| [Cisco ASA with Anyconnect VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Cisco ASA VPN 應用裝置與 Azure MFA |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 和 Azure MFA 伺服器
Azure MFA 伺服器可與您的 Citrix NetScaler SSL VPN 應用裝置整合，以提供 Citrix NetScaler SSL VPN 登入和入口網站存取的額外安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Citrix NetScaler SSL VPN 與 Azure MFA |
| [Citrix NetScaler SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Citrix NetScaler SSL VPN 應用裝置與 Azure MFA |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>刺柏/脈衝安全 SSL VPN 設備和 Azure MFA 伺服器
Azure MFA 伺服器與您的刺柏/脈衝安全 SSL VPN 應用裝置整合，為刺柏/脈衝安全 SSL VPN 登入和入口網站存取提供額外的安全性。  您可以使用 LDAP 或 RADIUS 通訊協定。  選取下列其中一項以下載詳細的逐步組態指南。

| 組態指南 | 描述 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 整合 Juniper/Pulse Secure SSL VPN 與 Azure MFA |
| [Juniper/Pulse Secure SSL VPN 與 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 整合 Juniper/Pulse Secure SSL VPN 應用裝置與 Azure MFA |

## <a name="next-steps"></a>後續步驟

- [利用 Azure Multi-Factor Authentication 的 NPS 擴充功能強化現有的驗證基礎結構](howto-mfa-nps-extension.md)

- [設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
