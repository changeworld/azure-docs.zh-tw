---
title: 混合式 Azure AD 聯結的受控驗證-Azure AD
description: 瞭解如何對混合式 Azure AD 聯結進行受控制的驗證，然後在整個組織內一次啟用
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66b216e5e511d2d80378ee7e2d124dccbc7abcb7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85252707"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>混合式 Azure AD 聯結的受控驗證

當所有必要條件都已就緒時，Windows 裝置會自動在您的 Azure AD 租使用者中註冊為裝置。 這些裝置身分識別在 Azure AD 中的狀態稱為混合式 Azure AD 聯結。 如需本文中所涵蓋概念的詳細資訊，請參閱[Azure Active Directory 中的裝置管理簡介](overview.md)和[規劃混合式 Azure Active Directory 聯結執行](hybrid-azuread-join-plan.md)。

組織可能會想要對混合式 Azure AD 聯結進行受控制的驗證，然後一次在整個組織中啟用它。 本文將說明如何完成混合式 Azure AD 聯結的受控制驗證。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>在 Windows 目前裝置上控制混合式 Azure AD 聯結的驗證

對於執行 Windows 桌面作業系統的裝置，支援的版本為 Windows 10 年度更新版 (版本 1607) 或更新版本。 最佳做法是升級至最新版的 Windows 10。

若要對 Windows 目前裝置上的混合式 Azure AD 聯結進行受控制的驗證，您需要：

1. 清除 Active Directory （AD）中的服務連接點（SCP）專案（如果存在）
1. 使用群組原則物件（GPO），在已加入網域的電腦上設定 SCP 的用戶端登錄設定
1. 如果您使用 AD FS，您也必須使用 GPO 在 AD FS 伺服器上設定 SCP 的用戶端登錄設定  
1. 您可能也需要[自訂](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)Azure AD Connect 中的同步處理選項，以啟用裝置同步處理。 


### <a name="clear-the-scp-from-ad"></a>從 AD 清除 SCP

使用 Active Directory Services 介面編輯器（ADSI 編輯器）來修改 AD 中的 SCP 物件。

1. 從和系統管理工作站或網域控制站，啟動**ADSI 編輯器**桌面應用程式，做為企業系統管理員。
1. 連接到您網域的設定**命名內容**。
1. 流覽至**CN = Configuration，dc = contoso，dc = com**  >  **CN = Services**  >  **CN = 裝置註冊**設定
1. 以滑鼠右鍵按一下分葉物件**CN = 62a0ff2e-97b9-4513-943f-0d221bd30080** ，然後選取 [**屬性**]
   1. 從 [**屬性編輯器**] 視窗中選取 [**關鍵字**]，然後按一下 [**編輯**]
   1. 選取**azureADId**和**azureADName**的值（一次一個），然後按一下 [**移除**]
1. 關閉**ADSI 編輯器**


### <a name="configure-client-side-registry-setting-for-scp"></a>設定 SCP 的用戶端登錄設定

使用下列範例建立群組原則物件（GPO）來部署登錄設定，以在裝置的登錄中設定 SCP 專案。

1. 開啟群組原則管理主控台，然後在您的網域中建立新的群組原則物件。
   1. 提供新建立的 GPO 名稱（例如，ClientSideSCP）。
1. 編輯 GPO，並找出下列路徑：**電腦**  >  **Preferences**  >  **設定喜好設定 Windows Settings**  >  **Registry**
1. 以滑鼠右鍵按一下登錄，然後選取 [**新增**登錄  >  **專案**]
   1. 在 [**一般**] 索引標籤上，設定下列各項
      1. 動作：**更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 金鑰路徑： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱： **TenantId**
      1. 數值型別： **REG_SZ**
      1. 值資料：您 Azure AD 實例的 GUID 或**目錄識別碼**（此值可在**Azure 入口網站**  >  **Azure Active Directory**  >  **屬性**  >  **目錄識別碼**中找到）
   1. 按一下 [檔案] &gt; [新增] &gt; [專案] 
1. 以滑鼠右鍵按一下登錄，然後選取 [**新增**登錄  >  **專案**]
   1. 在 [**一般**] 索引標籤上，設定下列各項
      1. 動作：**更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 金鑰路徑： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱： **TenantName**
      1. 數值型別： **REG_SZ**
      1. 數值資料：如果您使用同盟環境（例如 AD FS），您已驗證的**功能變數名稱**。 您已驗證的**功能變數名稱**或 onmicrosoft.com 功能變數名稱（例如， `contoso.onmicrosoft.com` 如果您使用受控環境）
   1. 按一下 [檔案] &gt; [新增] &gt; [專案] 
1. 關閉新建立之 GPO 的編輯器
1. 將新建立的 GPO 連結到所需的 OU，其中包含屬於您受控制之推出擴展的已加入網域電腦

### <a name="configure-ad-fs-settings"></a>設定 AD FS 設定

如果您使用 AD FS，您必須先將 GPO 連結到您的 AD FS 伺服器，使用上述指示設定用戶端 SCP。 SCP 物件會定義裝置物件的授權來源。 它可以是內部部署或 Azure AD。 當用戶端 SCP 設定為 AD FS 時，裝置物件的來源會建立為 Azure AD。

> [!NOTE]
> 如果您無法在 AD FS 伺服器上設定用戶端 SCP，裝置身分識別的來源會被視為內部部署。 ADFS 接著會在 ADFS 裝置註冊的屬性 "MaximumInactiveDays" 中定義約定期間之後，開始從內部部署目錄中刪除裝置物件。 您可以使用[set-adfsdeviceregistration Cmdlet](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)來尋找 ADFS 裝置註冊物件。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>在舊版 Windows 裝置上控制混合式 Azure AD 聯結的驗證

若要註冊舊版 Windows 裝置，組織必須安裝可在 Microsoft 下載中心取得的[適用於非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。

您可以使用軟體發佈系統 (例如  [Microsoft Endpoint Configuration Manager](/configmgr/)) 來部署此套件。 此套件支援使用 quiet 參數的標準無訊息安裝選項。 組態管理員的目前分支會提供勝過舊版的好處，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在向 Azure AD 進行驗證後，透過使用者認證以無訊息方式向 Azure AD 加入裝置。

若要控制裝置註冊，您應該將 Windows Installer 套件部署到您所選取的舊版 Windows 裝置群組。

> [!NOTE]
> 如果未在 AD 中設定 SCP，則您應該遵循使用群組原則物件（GPO）加入網域的電腦上，[針對 scp 設定客戶](#configure-client-side-registry-setting-for-scp)端登錄設定所述的相同方法。


確認所有專案都如預期般運作之後，您就可以[使用 Azure AD Connect 設定 SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)，自動向 Azure AD 註冊其餘的 Windows 目前和下層裝置。

## <a name="next-steps"></a>後續步驟

[規劃混合式 Azure Active Directory 加入實作](hybrid-azuread-join-plan.md) \(機器翻譯\)
