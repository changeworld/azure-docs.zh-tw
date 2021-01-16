---
title: Azure AD Connect：群組回寫
description: 本文描述 Azure AD Connect 中的群組回寫。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246905"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 群組回寫

群組回寫可讓客戶利用雲端群組來滿足其混合式需求。 如果您使用 Microsoft 365 群組功能，則您可以在內部部署 Active Directory 中表示這些群組。 只有當您的內部部署 Active Directory 中有 Exchange 時， **才能** 使用此選項。

## <a name="pre-requisites"></a>必要條件
必須符合下列先決條件，才能啟用群組回寫。
- Azure Active Directory Premium 您租使用者的授權。
- 在您的 Exchange 內部部署組織之間設定的混合式部署，Microsoft 365 並確認其正常運作。
- 已安裝支援的 Exchange 內部部署版本
- 使用 Azure Active Directory Connect 設定單一登入 

## <a name="enable-group-writeback"></a>啟用群組回寫
若要啟用群組回寫，請使用下列步驟：

1. 開啟 Azure AD Connect wizard、選取 [ **設定** ]，然後按 **[下一步**]。
2. 選取 [ **自訂同步處理選項** ]，然後按 **[下一步]**。
3. 在 [ **連接到 Azure AD]** 頁面上，輸入您的認證。 按 [下一步] 。
4. 在 [ **選用功能** ] 頁面上，確認仍然選取您先前設定的選項。
5. 選取 [ **群組回寫** ]，然後按一下 **[下一步]**。
6. 在 [ **回寫] 頁面** 上，選取 (OU) 的 Active Directory 組織單位，以將從 Microsoft 365 同步處理的物件儲存到您的內部部署組織，然後按 **[下一步]**。
7. 在 [ **準備** 設定] 頁面上，按一下 [ **設定**]。
8. 當嚮導完成時，請按一下 [設定完成] 頁面 **上的 [** 結束]。
9. 在 Azure Active Directory Connect 伺服器上以系統管理員身分開啟 Windows PowerShell，然後執行下列命令。

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

如需設定 Microsoft 365 群組的詳細資訊，請參閱 [使用內部部署 Exchange 混合設定 Microsoft 365 群組](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)。

## <a name="disabling-group-writeback"></a>停用群組回寫
若要停用群組回寫，請使用下列步驟： 


1. 啟動 Azure Active Directory Connect wizard，然後流覽至 [其他工作] 頁面。 選取 [ **自訂同步處理選項** ] 工作並按 **[下一步]**。
2. 在 [ **選用功能** ] 頁面上，取消核取 [群組回寫]。  您將會收到一則警告，告知您將刪除群組。  按一下 [是]  。
   >[!IMPORTANT]
   > 停用群組回寫會導致在下一個同步處理週期中，從您的本機 Active Directory 刪除這項功能先前建立的任何群組。 

   ![取消核取方塊](media/how-to-connect-group-writeback/group2.png)
  
3. 按 [下一步] 。
4. 按一下 [設定]  。

 >[!NOTE]
 > 停用群組回寫會將 Azure Active Directory 連接器上的「完整匯入」和「完整同步處理」旗標設為「true」，使規則變更在下一個同步處理迴圈中傳播，並刪除先前已回寫至 Active Directory 的群組。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
