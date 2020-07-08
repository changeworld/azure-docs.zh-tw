---
title: 安裝 Azure AD Connect 雲端佈建代理程式
description: 本文說明如何安裝 Azure AD Connect 雲端佈建代理程式。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b59942731c8ca7b29de30e160d8370c9cf76ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807633"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安裝 Azure AD Connect 雲端佈建代理程式
本文件會逐步引導您完成 Azure Active Directory (Azure AD) Connect 佈建代理程式的安裝程序，以及如何在 Azure 入口網站中進行初始設定。

>[!IMPORTANT]
>下列安裝指示假設您已符合所有[必要條件](how-to-prerequisites.md)。

安裝和設定 Azure AD Connect 佈建會在下列步驟中完成：
    
- [安裝代理程式](#install-the-agent)
- [驗證代理程式安裝](#verify-agent-installation)


## <a name="install-the-agent"></a>安裝代理程式
若要安裝代理程式，請遵循下列步驟。

1. 以企業系統管理員權限登入您將使用的伺服器。
1. 登入 Azure 入口網站，然後移至 [ **Azure Active Directory**]。
1. 在左側功能表中，選取 [ **Azure AD Connect**]。
1. 選取 [管理佈建 (預覽)] > [檢閱所有代理程式]。
1. 從 Azure 入口網站下載 Azure AD Connect 佈建代理程式。

   ![下載內部部署代理程式](media/how-to-install/install9.png)</br>
1. 執行 Azure AD Connect 佈建安裝程式 (AADConnectProvisioningAgent.Installer)。
1. 在 [Microsoft Azure AD Connect 佈建代理程式套件] 畫面上接受授權條款，然後選取 [安裝]。

   ![Microsoft Azure AD Connect 佈建代理程式套件畫面](media/how-to-install/install1.png)</br>

1. 此作業完成之後，設定精靈就會隨之啟動。 以 Azure AD 全域管理員帳戶登入。
1. 在 [連線 Active Directory] 畫面上，選取 [新增目錄]。 然後使用您的 Active Directory 系統管理員帳戶登入。 此作業會新增您的內部部署目錄。 選取 [下一步] 。

   ![連線 Active Directory 畫面](media/how-to-install/install3.png)</br>

1. 在 [設定完成] 畫面上，選取 [確認]。 此操作會註冊並重新啟動代理程式。

   ![設定完成畫面](media/how-to-install/install4.png)</br>

1. 此作業完成之後，您應該會看到「**您的代理程式設定已成功驗證」** 的通知。 選取 [結束]。

   ![[結束] 按鈕](media/how-to-install/install5.png)</br>
1. 如果您仍然看到一開始的 [Microsoft Azure AD Connect 佈建代理程式套件] 畫面，請選取 [關閉]。

## <a name="verify-agent-installation"></a>驗證代理程式安裝
代理程式驗證可在 Azure 入口網站中以及執行代理程式的本機伺服器上進行。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證
若要確認 Azure 可看到代理程式，請遵循下列步驟。

1. 登入 Azure 入口網站。
1. 從左側選取 [Azure Active Directory]  > [Azure AD Connect]。 在中間選取 [管理佈建 (預覽)]。

   ![Azure 入口網站](media/how-to-install/install6.png)</br>

1.  在 [Azure AD 佈建 (預覽)] 畫面上，選取 [檢閱所有代理程式]。

    ![檢閱所有代理程式選項](media/how-to-install/install7.png)</br>
 
1. 在 [內部部署佈建代理程式] 畫面上，您會看到已安裝的代理程式。 確認相關代理程式位於該處，且已標示為 [作用中]。

   ![內部部署佈建代理程式畫面](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>在本機伺服器上
若要確認代理程式正在執行中，請遵循下列步驟。

1.  使用管理員帳戶登入伺服器。
1.  瀏覽至 [服務]，或前往 [開始] > [執行] > [Services.msc]，以開啟 [服務]。
1.  在 [服務] 底下，確定 **Microsoft Azure AD Connect 代理程式更新程式**和 **Microsoft Azure AD Connect 佈建代理程式**皆位於該處，且狀態為 [執行中]。

    ![服務畫面](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理程式雖然已安裝，但必須先進行設定和啟用，才會開始同步使用者。 若要設定新的代理程式，請參閱[建立適用於 Azure AD Connect 雲端型佈建的新組態](how-to-configure.md)。



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
 
