---
title: 混合式身分識別所需的連接埠和通訊協定 - Azure | Microsoft Docs
description: 本頁面為針對 Azure AD Connect 必須開啟之連接埠的技術參考頁面
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331097"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>混合式身分識別所需的連接埠和通訊協定
下列文件是關於實作混合式身分識別解決方案之連接埠和通訊協定的技術參考。 請使用下圖並參閱對應的資料表。

![何謂 Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>表 1 - Azure AD Connect 和內部部署 AD
此表說明 Azure AD Connect 伺服器與內部部署 AD 之間通訊所需的連接埠和通訊協定。

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |在目的地樹系的 DNS 查閱。 |
| Kerberos |88 (TCP/UDP) |AD 樹系的 Kerberos 驗證。 |
| MS-RPC |135 （TCP） |繫結至 AD 樹系時，用於 Azure AD Connect 精靈的初始設定期間，也可以用於密碼同步處理期間。 |
| LDAP |389 (TCP/UDP) |用於從 AD 匯入資料。 資料會使用「Kerberos 簽章及密封」加密。 |
| SMB | 445 （TCP） |由無縫 SSO 用於在 AD 樹系中建立電腦帳戶。 |
| LDAP/SSL |636 (TCP/UDP) |用於從 AD 匯入資料。 資料的傳輸經過簽署和加密。 僅在使用 TLS 時使用。 |
| RPC |49152- 65535（隨機高 RPC 埠）（TCP） |繫結至 AD 樹系時，用於 Azure AD Connect 的初始設定期間，以及用於密碼同步處理期間。 如需詳細資訊，請參閱 [KB929851](https://support.microsoft.com/kb/929851)、[KB832017](https://support.microsoft.com/kb/832017) 和 [KB224196](https://support.microsoft.com/kb/224196)。 |
|WinRM  | 5985 （TCP） |僅當通過 Azure AD 連接嚮導安裝帶有 gMSA 的 AD FS 時才使用|
|AD DS Web 服務 | 9389 （TCP） |僅當通過 Azure AD 連接嚮導安裝帶有 gMSA 的 AD FS 時才使用 |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>表 2 - Azure AD Connect 和 Azure AD
此表說明 Azure AD Connect 伺服器與 Azure AD 之間通訊所需的連接埠和通訊協定。

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| HTTP |80 （TCP） |用於下載 CRL（憑證撤銷清單）以驗證 TLS/SSL 憑證。 |
| HTTPS |443（TCP） |用來與 Azure AD 同步處理。 |

有關需要在防火牆中打開的 URL 和 IP 位址的清單，請參閱[Office 365 URL 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)以及[排除 Azure AD 連接連接的疑難排解](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard)。

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>表 3 - Azure AD Connect 和 AD FS 同盟伺服器/WAP
此表說明 Azure AD Connect 伺服器與 AD FS 同盟/WAP 伺服器之間通訊所需的連接埠和通訊協定。  

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| HTTP |80 （TCP） |用於下載 CRL（憑證撤銷清單）以驗證 TLS/SSL 憑證。 |
| HTTPS |443（TCP） |用來與 Azure AD 同步處理。 |
| WinRM |5985 |WinRM 接聽程式 |

## <a name="table-4---wap-and-federation-servers"></a>表 4 - WAP 和同盟伺服器
此表說明同盟伺服器與 WAP 伺服器之間通訊所需的連接埠和通訊協定。

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| HTTPS |443（TCP） |用於進行驗證。 |

## <a name="table-5---wap-and-users"></a>表 5 - WAP 和使用者
此表說明使用者與 WAP 伺服器之間通訊所需的連接埠和通訊協定。

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| HTTPS |443（TCP） |用於裝置驗證。 |
| TCP |49443 (TCP) |用於憑證驗證。 |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>表 6a 和 6b - 傳遞驗證搭配單一登入 (SSO) 與密碼雜湊同步處理搭配單一登入 (SSO)
下列表格說明 Azure AD Connect 與 Azure AD 之間通訊所需的連接埠和通訊協定。

### <a name="table-6a---pass-through-authentication-with-sso"></a>表 6a - 傳遞驗證搭配 SSO
|通訊協定|連接埠號碼|描述
| --- | --- | ---
|HTTP|80|為安全性驗證 (例如 SSL) 啟用輸出 HTTP 流量。 為了讓連接器自動更新功能正確運作，也需要如此。
|HTTPS|443| 啟用輸出 HTTPS 流量來支援某些作業，例如啟用和停用功能、註冊連接器、下載連接器更新和處理所有使用者登入要求。

此外，Azure AD Connect 也必須能夠對 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)直接建立 IP 連線。

### <a name="table-6b---password-hash-sync-with-sso"></a>表 6b - 密碼雜湊同步處理搭配 SSO

|通訊協定|連接埠號碼|描述
| --- | --- | ---
|HTTPS|443| 啟用 SSO 註冊 (只有在 SSO 註冊程序才需要)。

此外，Azure AD Connect 也必須能夠對 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)直接建立 IP 連線。 同樣地，這只在 SSO 註冊程序中才需要。

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a 和 7b - 適用於 (AD FS/Sync) 和 Azure AD 的 Azure AD Connect Health 代理程式
下表說明在 Azure AD Connect Health 代理程式與 Azure AD 之間通訊所需的端點、連接埠和通訊協定

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a - 適用於 (AD FS/Sync) 和 Azure AD 的 Azure AD Connect Health 代理程式的連接埠和通訊協定
此表說明 Azure AD Connect Health 代理程式與 Azure AD 之間通訊所需的下列輸出連接埠和通訊協定。  

| 通訊協定 | 連接埠 | 描述 |
| --- | --- | --- |
| HTTPS |443（TCP） |輸出 |
| Azure 服務匯流排 |5671 （TCP） |輸出 |

Azure 服務匯流排埠 5671 不再需要最新版本的代理。 最新的 Azure AD 連接運行狀況代理版本僅需要端口 443。

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - 適用於 (AD FS/Sync) 和 Azure AD 之 Azure AD Connect Health 代理程式的端點
如需端點的清單，請參閱 [Azure AD Connect Health 代理程式的＜需求＞一節](how-to-connect-health-agent-install.md#requirements)。

