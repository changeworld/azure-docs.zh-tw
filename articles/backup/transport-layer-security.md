---
title: Azure 備份中的傳輸層安全性
description: 瞭解如何啟用 Azure 備份使用加密通訊協定傳輸層安全性 (TLS) ，在透過網路傳輸時保護資料的安全。
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327112"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure 備份中的傳輸層安全性

傳輸層安全性 (TLS) 是一種加密通訊協定，可在透過網路傳輸時保持資料安全。 Azure 備份會使用傳輸層安全性來保護所傳輸之備份資料的隱私權。 本文說明啟用 TLS 1.2 通訊協定的步驟，以提供比舊版更好的安全性。

## <a name="earlier-versions-of-windows"></a>舊版 Windows

如果電腦執行的是舊版 Windows，則必須安裝下面所述的對應更新，並且必須套用知識庫文章中記載的登錄變更。

|作業系統  |知識庫文章 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2、Windows 7、Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>更新將會安裝必要的通訊協定元件。 安裝之後，您必須進行上述知識庫文章中所述的登錄機碼變更，才能正確地啟用所需的通訊協定。

## <a name="verify-windows-registry"></a>確認 Windows 登錄

### <a name="configuring-schannel-protocols"></a>設定 SChannel 通訊協定

下列登錄機碼可確保在 SChannel 元件層級啟用 TLS 1.2 通訊協定：

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>所顯示的值預設會在 Windows Server 2012 R2 和更新版本中設定。 針對這些版本的 Windows，如果登錄機碼不存在，則不需要建立它們。

### <a name="configuring-net-framework"></a>設定 .NET Framework

下列登錄機碼會設定 .NET Framework 以支援強式加密。 您可以在這裡閱讀更多有關設定 [.NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)的資訊。

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-enable-tls-12"></a>為什麼要啟用 TLS 1.2？

TLS 1.2 比先前的密碼編譯通訊協定更安全，例如 SSL 2.0、SSL 3.0、TLS 1.0 和 TLS 1.1。 Azure 備份服務已完全支援 TLS 1.2。

### <a name="what-determines-the-encryption-protocol-used"></a>如何判斷使用的加密通訊協定？

用戶端和伺服器所支援的最高通訊協定版本會進行協商，以建立加密的交談。 如需 TLS 交握通訊協定的詳細資訊，請參閱 [使用 Tls 建立安全會話](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)。

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>未啟用 TLS 1.2 的影響為何？

為了提高通訊協定降級攻擊的安全性，Azure 備份開始以分階段方式停用早于1.2 的 TLS 版本。 這是跨服務的長期轉移的一部分，不允許舊版通訊協定和加密套件連接。 Azure 備份的服務和元件完全支援 TLS 1.2。 不過，缺少必要更新或某些自訂設定的 Windows 版本仍可防止提供 TLS 1.2 通訊協定。 這可能會造成失敗，包括但不限於下列一或多項：

- 備份和還原作業可能會失敗。
- 備份元件連線失敗，並出現錯誤 10054 (遠端主機已強制關閉現有的連線) 。
- 與 Azure 備份相關的服務不會如往常般停止或啟動。

## <a name="additional-resources"></a>其他資源

- [傳輸層安全性通訊協定](/windows/win32/secauthn/transport-layer-security-protocol)
- [確保在部署的作業系統間支援 TLS 1。2](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework 的傳輸層安全性 (TLS) 最佳做法](/dotnet/framework/network-programming/tls)