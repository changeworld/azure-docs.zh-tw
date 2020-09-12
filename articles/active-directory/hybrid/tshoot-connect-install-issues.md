---
title: 對 Azure AD Connect 安裝問題進行疑難排解 | Microsoft Docs'
description: 本主題提供對安裝 Azure AD Connect 的問題進行疑難排解的步驟。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275856"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>疑難排解： Azure AD Connect 安裝問題

## <a name="recommended-steps"></a>**建議的步驟**
請確認您適用何種 [Azure AD Connect 安裝類型](./how-to-connect-install-select-installation.md)。 如果您符合快速安裝的條件，則強烈建議您使用快速安裝。 快速安裝可讓您以最基本的選項完成安裝，因此發生問題的可能性較低。 

不過，如果您不符合快速安裝的條件，而且必須執行自訂安裝，則可以依照以下提供的一些最佳做法操作，以避免常見的問題。 為了方便說明，在此僅挑選幾個選項來討論：

* 確定您在要安裝 AAD Connect 的電腦上是系統管理員。 請以相同的系統管理員認證登入電腦。

* 讓下列頁面上的所有選項保留為預設值，但如果您要使用現有的 SQL Server，則 [使用現有的 SQL Server] 除外。 以下是關於如何使用自訂安裝選項的[其他詳細資料](./how-to-connect-install-custom.md)。 

    ![使用現有的 SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 在下列頁面上選擇 [建立新的 AD 帳戶] 選項，以避免現有的帳戶發生任何權限問題。

    ![AD 樹系帳戶](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**常見問題**

* [內部部署 Active Directory 的連接問題](./reference-connect-adconnectivitytools.md)。

* [線上 Azure Active Directory 的連線問題](./tshoot-connect-connectivity.md)。

* [內部部署 Active Directory 的許可權問題](./how-to-connect-configure-ad-ds-connector-account.md)。

## <a name="recommended-documents"></a>**建議的文件**
* [Azure AD Connect 的必要條件](./how-to-connect-install-prerequisites.md)
* [選取要用於 Azure AD Connect 的安裝類型](./how-to-connect-install-select-installation.md)
* [使用快速設定開始使用 Azure AD Connect](./how-to-connect-install-express.md)
* [自訂 Azure AD Connect 安裝](./how-to-connect-install-custom.md)
* [Azure AD Connect：從舊版升級到最新版本](./how-to-upgrade-previous-version.md)
* [Azure AD Connect：什麼是預備伺服器？](./plan-connect-topologies.md#staging-server)
* [什麼是 ADConnectivityTool PowerShell 模組？](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>接下來的步驟
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什麼是混合式身分識別？](whatis-hybrid-identity.md)