---
title: 安裝 Azure AD Connect 雲端佈建代理程式
description: 本文說明如何安裝 Azure AD Connect 雲端佈建代理程式。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3960b8afeb9d7ecc80aa49fc13eee4977fa5494
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173965"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安裝 Azure AD Connect 雲端佈建代理程式
本文件會逐步引導您完成 Azure Active Directory (Azure AD) Connect 佈建代理程式的安裝程序，以及如何在 Azure 入口網站中進行初始設定。

>[!IMPORTANT]
>下列安裝指示假設您已符合所有[必要條件](how-to-prerequisites.md)。

安裝和設定 Azure AD Connect 佈建會在下列步驟中完成：

- [群組受管理的服務帳戶](#group-managed-service-accounts) 
- [安裝代理程式](#install-the-agent)
- [驗證代理程式安裝](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>群組受管理的服務帳戶
群組受管理的服務帳戶是受控網域帳戶，可提供自動密碼管理、簡化的服務主體名稱 (SPN) 管理、將管理委派給其他系統管理員的能力，以及將這項功能延伸到多部伺服器。  Azure AD Connect Cloud Sync 支援並建議使用群組受管理的服務帳戶來執行代理程式。  如需 gMSA 的詳細資訊，請參閱 [群組受管理的服務帳戶](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>升級現有的代理程式以使用 gMSA 帳戶
若要升級現有的代理程式以使用在安裝期間建立的 gMSA 帳戶，只要執行 AADConnectProvisioningAgent.msi，即可將代理程式服務更新為最新版本。  這會將服務升級為最新版本。  現在再次執行安裝精靈，並在出現提示時提供認證以建立帳戶。



## <a name="install-the-agent"></a>安裝代理程式
若要安裝代理程式，請遵循下列步驟。

 1. 以企業系統管理員權限登入您將使用的伺服器。
 2. 登入 Azure 入口網站，然後移至 **Azure Active Directory**。
 3. 在左側功能表中，選取 [ **Azure AD Connect**]。
 4. 選取 [管理佈建 (預覽)] > [檢閱所有代理程式]。
 5. 從 Azure 入口網站下載 Azure AD Connect 佈建代理程式。
   ![下載內部部署代理程式](media/how-to-install/install-9.png)</br>
 6. 執行 Azure AD Connect 布建安裝程式 AADConnectProvisioningAgent.msi。
 7. 在 [Microsoft Azure AD Connect 佈建代理程式套件] 畫面上接受授權條款，然後選取 [安裝]。
   ![Microsoft Azure AD Connect 佈建代理程式套件畫面](media/how-to-install/install-1.png)</br>
 8. 此作業完成之後，設定精靈就會隨之啟動。 以 Azure AD 全域管理員帳戶登入。
 9. 在 [ **設定服務帳戶] 畫面** 上，選取 [ **建立 gMSA** ] 或 [ **使用自訂 gMSA**]。  如果您允許代理程式建立帳戶，它就會命名為 provAgentgMSA $。 如果您指定 [ **使用自訂 gMSA** ]，系統會提示您提供此帳戶。
 10. 輸入網域系統管理員認證，以建立將用來執行代理程式服務的群組受管理的服務帳戶。 按 [下一步]  。  
   ![建立 gMSA](media/how-to-install/install-12.png)</br>
 11. 在 [連線 Active Directory] 畫面上，選取 [新增目錄]。 然後使用您的 Active Directory 系統管理員帳戶登入。 此作業會新增您的內部部署目錄。 
 12. （選擇性）您可以選取 [ **選取網域控制站優先權** ] 和 [排序網域控制站清單]，以管理代理程式將使用之網域控制站的喜好設定。   按一下 [確定]。
  ![訂購網域 controlllers](media/how-to-install/install-2a.png)</br>
 13. 選取 [下一步] 。
  ![連線 Active Directory 畫面](media/how-to-install/install-3a.png)</br>
 14.  在 [ **代理程式安裝** ] 畫面上，確認設定和將建立的帳戶，然後按一下 [ **確認**]。
  ![確認 settings](media/how-to-install/install-11.png)</br>
 15. 完成此作業之後，您應該會看到 **代理程式安裝已完成。** 選取 [結束]。
  ![設定完成畫面](media/how-to-install/install-4a.png)</br>
1. 如果您仍然看到一開始的 [Microsoft Azure AD Connect 佈建代理程式套件] 畫面，請選取 [關閉]。

## <a name="verify-agent-installation"></a>驗證代理程式安裝
代理程式驗證可在 Azure 入口網站中以及執行代理程式的本機伺服器上進行。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證
若要確認 Azure 可看到代理程式，請遵循下列步驟。

1. 登入 Azure 入口網站。
1. 從左側選取 [Azure Active Directory]  > [Azure AD Connect]。 在中間選取 [管理佈建 (預覽)]。

   ![Azure 入口網站](media/how-to-install/install-6.png)</br>

1.  在 [Azure AD 佈建 (預覽)] 畫面上，選取 [檢閱所有代理程式]。

    ![檢閱所有代理程式選項](media/how-to-install/install-7.png)</br>
 
1. 在 [內部部署佈建代理程式] 畫面上，您會看到已安裝的代理程式。 確認相關代理程式位於該處，且已標示為 [作用中]。

   ![內部部署佈建代理程式畫面](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>在本機伺服器上
若要確認代理程式正在執行中，請遵循下列步驟。

1.  使用管理員帳戶登入伺服器。
1.  瀏覽至 [服務]，或前往 [開始] > [執行] > [Services.msc]，以開啟 [服務]。
1.  在 [服務] 底下，確定 **Microsoft Azure AD Connect 代理程式更新程式** 和 **Microsoft Azure AD Connect 佈建代理程式** 皆位於該處，且狀態為 [執行中]。

    ![服務畫面](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>代理程式雖然已安裝，但必須先進行設定和啟用，才會開始同步使用者。 若要設定新的代理程式，請參閱[建立適用於 Azure AD Connect 雲端型佈建的新組態](how-to-configure.md)。




## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
