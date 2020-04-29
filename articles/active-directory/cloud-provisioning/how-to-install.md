---
title: 安裝 Azure AD Connect 雲端佈建代理程式
description: 本文說明如何安裝 Azure AD Connect 雲端布建代理程式。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263344"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安裝 Azure AD Connect 雲端佈建代理程式
本檔會逐步引導您完成 Azure Active Directory （Azure AD） Connect 布建代理程式的安裝程式，以及如何在 Azure 入口網站中一開始進行設定。

>[!IMPORTANT]
>下列安裝指示假設已符合所有[必要條件](how-to-prerequisites.md)。

安裝和設定 Azure AD Connect 布建會在下列步驟中完成：
    
- [安裝代理程式](#install-the-agent)
- [驗證代理程式安裝](#verify-agent-installation)


## <a name="install-the-agent"></a>安裝代理程式
若要安裝代理程式，請遵循下列步驟。

1. 以企業系統管理員許可權登入您將使用的伺服器。
1. 移至 Azure 入口網站。 在左側選取 [ **Azure Active Directory**]。
1. 選取 **[管理布建（預覽）** > ] [**審查所有代理**程式]。
1. 從 Azure 入口網站下載 Azure AD Connect 布建代理程式。

   ![下載內部部署代理程式](media/how-to-install/install9.png)</br>
1. 執行 Azure AD Connect 布建安裝程式（AADConnectProvisioningAgent）。
1. 在 [ **Microsoft Azure AD 連接布建代理程式套件]** 畫面上，接受授權條款並選取 [**安裝**]。

   ![Microsoft Azure AD 連接布建代理程式套件畫面](media/how-to-install/install1.png)</br>

1. 此作業完成之後，[設定向導] 就會啟動。 以 Azure AD 全域管理員帳戶登入。
1. 在 [**連接 Active Directory]** 畫面上，選取 [**新增目錄**]。 然後使用您的 Active Directory 系統管理員帳戶登入。 此作業會新增您的內部部署目錄。 選取 [下一步]  。

   ![連接 Active Directory 畫面](media/how-to-install/install3.png)</br>

1. 在 [設定**完成**] 畫面上，選取 [**確認**]。 此操作會註冊並重新啟動代理程式。

   ![設定完成畫面](media/how-to-install/install4.png)</br>

1. 此作業完成之後，您應該會看到**您的代理程式設定已成功驗證。** 選取 **[** 結束]。

   ![結束按鈕](media/how-to-install/install5.png)</br>
1. 如果您仍然看到初始 Microsoft Azure AD [連線布建**代理程式套件]** 畫面，請選取 [**關閉**]。

## <a name="verify-agent-installation"></a>驗證代理程式安裝
代理程式驗證會在 Azure 入口網站以及執行代理程式的本機伺服器上進行。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證
若要確認 Azure 正在查看代理程式，請遵循下列步驟。

1. 登入 Azure 入口網站。
1. 在左側，選取 [ **Azure Active Directory** > **Azure AD Connect**]。 在中央，選取 [**管理布建（預覽）**]。

   ![Azure 入口網站](media/how-to-install/install6.png)</br>

1.  在 [ **Azure AD 布建（預覽）** ] 畫面上，選取 [**審查所有代理**程式]。

    ![[審查所有代理程式] 選項](media/how-to-install/install7.png)</br>
 
1. 在 [**內部部署布建代理**程式] 畫面上，您會看到您已安裝的代理程式。 確認相關代理程式位於該處，且已標示為 [作用中]  。

   ![內部部署布建代理程式畫面](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>驗證埠
若要確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊，請遵循下列步驟。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試會驗證您的代理程式是否可透過埠443與 Azure 通訊。 開啟瀏覽器，並移至安裝代理程式之伺服器上的先前 URL。

![驗證埠可連線性](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本機伺服器上
若要確認代理程式是否正在執行，請遵循下列步驟。

1.  使用系統管理員帳戶登入伺服器。
1.  藉由流覽至或移至 [**啟動** > ] [**執行** > **services.msc**] 來開啟**服務**。
1.  在 [**服務**] 下，確定**Microsoft Azure AD 連接代理程式更新**程式，並**Microsoft Azure AD connect 布建代理程式***已存在*，且其狀態為執行中。

    ![服務畫面](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理程式已安裝，但必須先進行設定和啟用，才會開始同步處理使用者。 若要設定新的代理程式，請參閱[建立 Azure AD Connect 雲端型布建的新](how-to-configure.md)設定。



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
 
