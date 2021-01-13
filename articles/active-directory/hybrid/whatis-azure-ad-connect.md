---
title: 什麼是 Azure AD Connect 和 Connect Health。 | Microsoft Docs
description: 了解用來搭配 Azure AD 進行同步處理及監視您內部部署環境的工具。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8e1af1848405441088796d2e3b42e7b52eedba8
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065111"
---
# <a name="what-is-azure-ad-connect"></a>何謂 Azure AD Connect？

Azure AD Connect 是一種 Microsoft 工具，其設計目的是要符合並完成混合式身分識別的目標。  它可提供下列功能：
     
- [密碼雜湊同步處理](whatis-phs.md) - 一種將使用者內部部署 AD 密碼的雜湊與 Azure AD 同步的登入方法。
- [傳遞驗證](how-to-connect-pta.md) - 一種登入方法，可讓使用者在內部部署環境與雲端中使用相同的密碼，但不需要額外的同盟環境基礎結構。
- [同盟整合](how-to-connect-fed-whatis.md) - 同盟是 Azure AD Connect 的選用組件，可用來以內部部署 AD FS 基礎結構設定混合式環境。 它也提供 AD FS 管理功能，例如憑證更新及額外的 AD FS 伺服器部署。
- [同步處理](how-to-connect-sync-whatis.md) - 負責建立使用者、群組及其他物件。  此外，也確保您內部部署使用者和群組的身分識別資訊與雲端相符。  此同步處理也包括密碼雜湊。
- [狀況監控](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) - Azure AD Connect Health 可以提供健全監控，並在 Azure 入口網站中提供檢視此活動的中央位置。 


![何謂 Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>何謂 Azure AD Connect Health？

Azure Active Directory (Azure AD) Connect Health 可為您的內部部署身分識別基礎結構提供健全的監視功能。 其可讓您維持與 Microsoft 365 和 Microsoft Online Services 的可靠連線。  藉由為您的關鍵身分識別元件提供監視功能，便得以達成此可靠性。 此外，它還可讓您輕鬆存取這些元件的相關關鍵資料點。

這些資訊會呈現在 [Azure AD Connect Health 入口網站](https://aka.ms/aadconnecthealth)中。 請使用 Azure AD Connect Health 入口網站來檢視警示、效能監視、使用情況分析等資訊。 Azure AD Connect Health 就像功能濾鏡，可讓您集中監控重要身分識別元件的健康狀態。

![Azure AD Connect Health 是什麼](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>為何要使用 Azure AD Connect？
將內部部署目錄與 Azure AD 整合可提供一個通用身分識別來存取雲端和內部部署資源，讓使用者變得更有生產力。 使用者和組織可以利用：

* 使用者可以使用單一身分識別來存取內部部署應用程式和雲端服務，例如 Microsoft 365。
* 單一工具即可提供輕鬆進行同步處理和登入的部署經驗。
* 提供您案例的最新功能。 Azure AD Connect 會取代舊版的身分識別整合工具，如 DirSync 和 Azure AD Sync。如需詳細資訊，請參閱 [混合式身分識別目錄整合工具比較](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>為何使用 Azure AD Connect Health？
搭配 Azure AD 時，使用者會更具生產力，因為會有一個可同時存取雲端和內部部署資源的通用身分識別。 確保環境可靠以便讓使用者能夠存取這些資源，已變成一項挑戰。  Azure AD Connect Health 可協助您監視及深入了解內部部署身分識別基礎結構，藉此確保此環境的可靠性。 使用方式相當簡單，您只需將代理程式安裝在各個內部部署身分識別伺服器中即可。

在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上，適用於 AD FS 的 Azure AD Connect Health 支援 AD FS 2.0。 它也支援監視可驗證外部網路存取的 AD FS Proxy 或 Web 應用程式 Proxy 伺服器。 藉由輕鬆且快速地安裝健康情況代理程式，適用於 AD FS 的 Azure AD Connect Health 可提供您一組重要功能。

重要優勢和最佳做法：

|主要權益|最佳做法|
|-----|-----|
|強化的安全性|[外部網路鎖定趨勢](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[失敗的登入報告](how-to-connect-health-adfs-risky-ip.md)</br>[在符合隱私權規範中](reference-connect-health-user-privacy.md)|
|取得所有[重大 ADFS 系統問題](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)的警示|伺服器設定和可用性</br>[效能和連線能力](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>定期維護|
|容易部署及管理|[快速代理程式安裝](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>代理程式自動升級至最新版</br>幾分鐘內就可在入口網站中取得資料|
豐富的[使用計量](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|應用程式使用量排名</br>網路位置與 TCP 連線</br>每一部伺服器的權杖要求數|
|良好的使用者體驗|來自 Azure 入口網站的儀表板方式</br>[透過電子郵件警示](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>使用 Azure AD Connect 的授權需求

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 的授權需求
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)
- [安裝 Azure AD Connect Health 代理程式](how-to-connect-health-agent-install.md)
