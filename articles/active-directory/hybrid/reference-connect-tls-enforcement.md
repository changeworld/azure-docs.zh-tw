---
title: Azure AD Connect：針對 Azure Active Directory Connect 強制執行 TLS 1.2| Microsoft Docs
description: 本文章列出 Azure AD Connect 和 Azure AD Sync 的所有版本
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff5c75785622b43e66b808009c4674d4b2f2b50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "78300843"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>針對 Azure AD Connect 強制執行 TLS 1.2

傳輸層安全性 (TLS) 通訊協定 1.2 版是加密通訊協定，其設計旨在提供安全的通訊。  TLS 通訊協定的目標主要是提供隱私權和資料完整性。  TLS 已使用 [RFC 5246](https://tools.ietf.org/html/rfc5246) 中定義的 1.2 版通過許多反覆項目。  Azure Active Directory Connect 1.2.65.0 版和更新版本現在完全支援只使用 TLS 1.2 來與 Azure 進行通訊。  這份文件將提供如何強制執行 Azure AD Connect 伺服器只使用 TLS 1.2 的相關資訊。

## <a name="update-the-registry"></a>更新登錄
為了強制 Azure AD Connect 伺服器只使用 TLS 1.2，您必須更新 Windows 伺服器的登錄。  設定 Azure AD Connect 伺服器上的下列登錄機碼。

>[!IMPORTANT]
>更新登錄之後，您必須重新啟動 Windows 伺服器，變更才會生效。


### <a name="enable-tls-12"></a>啟用 TLS 1.2
- [HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\Microsoft \\ 。NETFramework\v4.0.30319
  - "SystemDefaultTlsVersions" = dword：00000001
  - "SchUseStrongCrypto" = dword： .0000001
- [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \\ 。NETFramework\v4.0.30319
  - "SystemDefaultTlsVersions" = dword：00000001
  - "SchUseStrongCrypto"=dword:00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 伺服器]
  - "Enabled" = dword：00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 伺服器]
  - "DisabledByDefault" = dword：00000000 
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端]
  - "Enabled" = dword：00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端]
  - "DisabledByDefault" = dword：00000000

### <a name="powershell-script-to-enable-tls-12"></a>啟用 TLS 1.2 的 PowerShell 指令碼
您可以使用下列 PowerShell 指令碼，在 Azure AD Connect 伺服器上啟用 TLS 1.2。

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been enabled.'
```

### <a name="disable-tls-12"></a>停用 TLS 1.2
- [HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\Microsoft \\ 。NETFramework\v4.0.30319
  - "SystemDefaultTlsVersions" = dword：00000000
  - "SchUseStrongCrypto" = dword：0000000
- [HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \\ 。NETFramework\v4.0.30319
  - "SystemDefaultTlsVersions" = dword：00000000
  - "SchUseStrongCrypto" = dword：00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 伺服器]
  - "Enabled" = dword：00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 伺服器]
  - "DisabledByDefault" = dword：00000001
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端]
  - "Enabled" = dword：00000000
- [HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端]
  - "DisabledByDefault" = dword：00000001 

### <a name="powershell-script-to-disable-tls-12"></a>停用 TLS 1.2 的 PowerShell 指令碼
您可以使用下列 PowerShell 指令碼，在 Azure AD Connect 伺服器上停用 TLS 1.2。

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been disabled.'
```

## <a name="next-steps"></a>後續步驟
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
