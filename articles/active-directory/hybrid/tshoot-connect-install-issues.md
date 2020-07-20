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
ms.openlocfilehash: 6da21e9aa3b3b4cafec71a4d1881b9eb32b4dedc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85356214"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>疑難排解： Azure AD Connect 安裝問題

## <a name="recommended-steps"></a>**建議的步驟**
請確認您適用何種 [Azure AD Connect 安裝類型](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)。 如果您符合快速安裝的條件，則強烈建議您使用快速安裝。 快速安裝可讓您以最基本的選項完成安裝，因此發生問題的可能性較低。 

不過，如果您不符合快速安裝的條件，而且必須執行自訂安裝，則可以依照以下提供的一些最佳做法操作，以避免常見的問題。 為了方便說明，在此僅挑選幾個選項來討論：

* 確定您在要安裝 AAD Connect 的電腦上是系統管理員。 請以相同的系統管理員認證登入電腦。

* 讓下列頁面上的所有選項保留為預設值，但如果您要使用現有的 SQL Server，則 [使用現有的 SQL Server] 除外。 以下是關於如何使用自訂安裝選項的[其他詳細資料](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)。 

    ![使用現有的 SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* 在下列頁面上選擇 [建立新的 AD 帳戶] 選項，以避免現有的帳戶發生任何權限問題。

    ![AD 樹系帳戶](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**常見問題**

* 內部[部署 Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools)的連線問題。

* [線上 Azure Active Directory 的連線問題](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity)。

* 內部[部署 Active Directory 的許可權問題](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account)。

## <a name="recommended-documents"></a>**建議的文件**
* [Azure AD Connect 的必要條件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [選取要用於 Azure AD Connect 的安裝類型](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [使用快速設定開始使用 Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [自訂 Azure AD Connect 安裝](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect：從舊版升級到最新版本](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect：什麼是預備伺服器？](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [什麼是 ADConnectivityTool PowerShell 模組？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什麼是混合式身分識別？](whatis-hybrid-identity.md)





