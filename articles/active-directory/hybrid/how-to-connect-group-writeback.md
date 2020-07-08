---
title: Azure AD Connect：群組回寫
description: 本文說明 Azure AD Connect 中的群組回寫。
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
ms.openlocfilehash: d2a41dcf9c224e9e4a9a280078432e0b57e16c2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359410"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 群組回寫

群組回寫可讓客戶利用雲端群組來滿足其混合式需求。 如果您使用 [Office 365 群組]  功能，就可以在內部部署的 Active Directory 中顯示這些群組。 只有當您的內部部署 Active Directory 中有 Exchange 時，**才**可以使用此選項。

## <a name="pre-requisites"></a>必要條件
必須符合下列必要條件，才能啟用群組回寫。
- Azure Active Directory Premium 租使用者的授權。
- 您的 Exchange 內部部署組織與 Office 365 之間已設定的混合式部署，並確認其運作正常。
- 已安裝支援的 Exchange 內部部署版本
- 使用 Azure Active Directory Connect 設定單一登入 

## <a name="enable-group-writeback"></a>啟用群組回寫
若要啟用群組回寫，請使用下列步驟：

1. 開啟 Azure AD Connect wizard，選取 [**設定**]，然後按 **[下一步]**。
2. 選取 **[自訂同步處理選項**]，然後按 **[下一步]**。
3. 在 [連線**至 Azure AD]** 頁面上，輸入您的認證。 按 [下一步] 。
4. 在 [**選用功能**] 頁面上，確認您先前設定的選項仍為選取狀態。
5. 選取 [**群組回寫**]，然後按 **[下一步]**。
6. 在 [**回寫] 頁面**上，選取 Active Directory 的組織單位（OU），將從 Office 365 同步處理的物件儲存到您的內部部署組織，然後按 **[下一步]**。
7. 在 [**準備好**設定] 頁面上，按一下 [**設定**]。
8. 當 wizard 完成時，按一下 [設定完成] 頁面**上的 [** 結束]。
9. 在 Azure Active Directory Connect 伺服器上開啟 Windows PowerShell，然後執行下列命令。

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

如需設定 Office 365 群組的詳細資訊，請參閱[使用內部部署 Exchange 混合設定 Microsoft 365 群組](https://docs.microsoft.com/exchange/hybrid-deployment/set-up-office-365-groups#enable-group-writeback-in-azure-ad-connect)。

## <a name="disabling-group-writeback"></a>停用群組回寫
若要停用群組回寫，請使用下列步驟： 


1. 啟動 [Azure Active Directory Connect wizard]，然後流覽至 [其他工作] 頁面。 選取 [**自訂同步處理選項**] 工作並按 **[下一步]**。
2. 在 [**選用功能**] 頁面上，取消核取 [群組回寫]。  您會收到一則警告，告訴您該群組將會被刪除。  按一下 [是] 。
   >[!IMPORTANT]
   > 停用群組回寫會導致在下一次同步處理週期中，從本機 Active Directory 刪除此功能先前所建立的任何群組。 

   ![取消核取核取方塊](media/how-to-connect-group-writeback/group2.png)
  
3. 按 [下一步] 。
4. 按一下 [設定]****。

 >[!NOTE]
 > 停用群組回寫會將 [完整匯入] 和 [完整同步處理] 旗標設定為 Azure Active Directory 連接器上的 [true]，使規則變更在下一個同步處理週期中傳播，並刪除先前已回寫至您 Active Directory 的群組。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。
