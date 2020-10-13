---
title: TLS 1.2 強制-Azure Active Directory 註冊服務
description: 移除 Azure AD 裝置註冊服務的 TLS 1.0 和1.1 支援
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268752"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>針對 Azure AD 註冊服務強制執行 TLS 1。2

Azure Active Directory (Azure AD) 裝置註冊服務是用來以裝置身分識別將裝置連線到雲端。 Azure AD 裝置註冊服務目前支援使用傳輸層安全性 (TLS) 1.2 來與 Azure 進行通訊。 為了確保安全性和最高類別的加密，Microsoft 建議停用 TLS 1.0 和1.1。 本檔將提供有關如何確保用來完成註冊並與 Azure AD 裝置註冊服務通訊的電腦使用 TLS 1.2 的資訊。

TLS 通訊協定1.2 版是一種密碼編譯通訊協定，其設計目的是要提供安全通訊。 TLS 通訊協定的目標主要是提供隱私權和資料完整性。 在 [RFC 5246 (external link) ](https://tools.ietf.org/html/rfc5246)中定義版本1.2 的許多反復專案，TLS 已完成。

目前的連線分析會顯示少量的 TLS 1.1 和1.0 使用方式，但我們會提供此資訊，讓您可以視需要更新任何受影響的用戶端或伺服器，然後才支援 TLS 1.1 和1.0。 如果您在混合式案例中使用任何內部部署基礎結構，或 Active Directory 同盟服務 (AD FS) ，請確定基礎結構可支援使用 TLS 1.2 的輸入和輸出連線。

## <a name="update-windows-servers"></a>更新 Windows 伺服器

若為使用 Azure AD 裝置註冊服務或作為 proxy 的 Windows server，請使用下列步驟來確定已啟用 TLS 1.2：

> [!IMPORTANT]
> 更新登錄之後，您必須重新開機 Windows 伺服器，變更才會生效。

### <a name="enable-tls-12"></a>啟用 TLS 1.2

確定已設定下列登錄字串，如下所示：

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端
  - "DisabledByDefault" = dword：00000000
  - "Enabled" = dword：00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 伺服器
  - "DisabledByDefault" = dword：00000000
  - "Enabled" = dword：00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>更新非 Windows proxy

任何作為裝置和 Azure AD 裝置註冊服務之間 proxy 的電腦都必須確定已啟用 TLS 1.2。 遵循廠商的指導方針，以確保支援。

## <a name="update-ad-fs-servers"></a>更新 AD FS 伺服器

任何用來與 Azure AD 裝置註冊服務通訊的 AD FS 伺服器都必須確定已啟用 TLS 1.2。 如需如何啟用/驗證此設定的詳細資訊，請參閱 [管理 AD FS 的 SSL/TLS 通訊協定和加密套件](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 。

## <a name="client-updates"></a>用戶端更新

由於所有用戶端-伺服器和瀏覽器伺服器組合都必須使用 TLS 1.2 來與 Azure AD 裝置註冊服務連線，因此您可能需要更新這些裝置。

以下是已知無法支援 TLS 1.2 的用戶端。 更新您的用戶端，以確保不中斷的存取。

- Android 4.3 版及更早版本
- Firefox 5.0 版及更早版本
- Windows 7 及更早版本上的 Internet Explorer 8-10 版
- Windows Phone 8.0 上的 Internet Explorer 10
- OS X 10.8.4 及更早版本上的 Safari 版本6.0。4

## <a name="next-steps"></a>後續步驟

[TLS/SSL 總覽 (Schannel SSP) ](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)