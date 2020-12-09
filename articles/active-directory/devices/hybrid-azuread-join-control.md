---
title: 混合式 Azure AD 聯結的受控驗證-Azure AD
description: 瞭解如何對混合式 Azure AD 聯結進行受控制的驗證，然後一次在整個組織內啟用它
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
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860927"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>混合式 Azure AD 聯結的受控驗證

當所有必要條件都已就緒時，Windows 裝置會自動註冊為您 Azure AD 租使用者中的裝置。 這些裝置身分識別在 Azure AD 中的狀態稱為「混合式 Azure AD 聯結」。 如需本文所涵蓋之概念的詳細資訊，請參閱 Azure Active Directory 中的 [裝置管理簡介](overview.md) ，並 [規劃您的混合式 Azure Active Directory 聯結執行](hybrid-azuread-join-plan.md)。

組織可能會想要對混合式 Azure AD 聯結進行受控制的驗證，然後一次在整個組織內啟用它。 本文將說明如何完成混合式 Azure AD 聯結的受控驗證。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>在 Windows 目前裝置上進行混合式 Azure AD 聯結的受控驗證

對於執行 Windows 桌面作業系統的裝置，支援的版本為 Windows 10 年度更新版 (版本 1607) 或更新版本。 最佳做法是升級至最新版的 Windows 10。

若要在 Windows 目前的裝置上執行混合式 Azure AD 聯結的受控驗證，您需要：

1. 清除服務連接點 (SCP) Active Directory (AD) 中的專案（如果有的話）
1. 使用群組原則物件 (GPO，在加入網域的電腦上設定 SCP 的用戶端登錄設定) 
1. 如果您使用 AD FS，也必須使用 GPO 在 AD FS 伺服器上設定 SCP 的用戶端登錄設定。  
1. 您也可能需要 [自訂](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) Azure AD Connect 中的同步處理選項，以啟用裝置同步處理。 


### <a name="clear-the-scp-from-ad"></a>從 AD 清除 SCP

使用 Active Directory 服務介面編輯器 (ADSI 編輯器) 來修改 AD 中的 SCP 物件。

1. 以企業系統管理員的身分，從和系統管理工作站或網域控制站啟動 **ADSI 編輯器** 桌面應用程式。
1. 連接到網域的設定 **命名內容** 。
1. 流覽至 **CN = Configuration，dc = contoso，dc = com**  >  **CN = Services**  >  **CN = Device Registration Configuration**
1. 以滑鼠右鍵按一下分葉物件 **CN = 62a0ff2e-97b9-4513-943f-0d221bd30080** ，然後選取 [**屬性**]
   1. 從 **屬性編輯器** 視窗中選取 **關鍵字**，然後按一下 [**編輯**]
   1. 選取 **azureADId** 和 **azure ad 名稱** 的值，一次 (一個) 然後按一下 [**移除**]。
1. 關閉 **ADSI 編輯器**


### <a name="configure-client-side-registry-setting-for-scp"></a>設定 SCP 的用戶端登錄設定

使用下列範例來建立群組原則物件 (GPO) 部署登錄設定，以在您的裝置登錄中設定 SCP 專案。

1. 開啟群組原則管理主控台，並在您的網域中建立新的群組原則物件。
   1. 提供新建立的 GPO 名稱 (例如，ClientSideSCP) 。
1. 編輯 GPO，並找出下列路徑：**電腦**  >  **Preferences**  >  **設定喜好設定 Windows Settings**  >  **Registry**
1. 在登錄上按一下滑鼠右鍵，然後選取 [**新增** 登錄  >  **專案**]
   1. 在 [ **一般** ] 索引標籤上，設定下列各項
      1. 動作： **更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 機碼路徑： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱： **TenantId**
      1. 數值型別： **REG_SZ**
      1. 值資料：您 Azure AD 實例的 GUID 或 **目錄識別碼** (此值可在 **Azure 入口網站**  >  **Azure Active Directory**  >  **屬性**  >  **目錄識別碼** 中找到) 
   1. 按一下 [檔案] &gt; [新增] &gt; [專案]
1. 在登錄上按一下滑鼠右鍵，然後選取 [**新增** 登錄  >  **專案**]
   1. 在 [ **一般** ] 索引標籤上，設定下列各項
      1. 動作： **更新**
      1. Hive： **HKEY_LOCAL_MACHINE**
      1. 機碼路徑： **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 值名稱： **TenantName**
      1. 數值型別： **REG_SZ**
      1. 值資料：如果您使用同盟環境（例如 AD FS），則為已驗證的 **功能變數名稱** 。 您已驗證的 **功能變數名稱** 或 onmicrosoft.com 功能變數名稱（例如， `contoso.onmicrosoft.com` 如果您使用受控環境）
   1. 按一下 [檔案] &gt; [新增] &gt; [專案]
1. 針對新建立的 GPO 關閉編輯器
1. 將新建立的 GPO 連結到所需的 OU，內含屬於您所控制推出人口的已加入網域電腦

### <a name="configure-ad-fs-settings"></a>設定 AD FS 設定

如果您使用 AD FS，您必須先將 GPO 連結到 AD FS 伺服器，以使用上述指示來設定用戶端 SCP。 SCP 物件會定義裝置物件的授權來源。 它可以是內部部署或 Azure AD。 當用戶端 SCP 設定為 AD FS 時，會建立裝置物件的來源作為 Azure AD。

> [!NOTE]
> 如果您無法在 AD FS 伺服器上設定用戶端 SCP，則會將裝置身分識別的來源視為內部部署。 ADFS 接著會在 ADFS 裝置註冊的屬性 "MaximumInactiveDays" 中定義約定期限之後，開始刪除內部部署目錄中的裝置物件。 您可以使用 [set-adfsdeviceregistration Cmdlet](/powershell/module/adfs/get-adfsdeviceregistration)找到 ADFS 裝置註冊物件。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>舊版 Windows 裝置上混合式 Azure AD 聯結的受控驗證

若要註冊舊版 Windows 裝置，組織必須安裝可在 Microsoft 下載中心取得的[適用於非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。

您可以使用軟體發佈系統 (例如  [Microsoft Endpoint Configuration Manager](/configmgr/)) 來部署此套件。 此套件支援使用 quiet 參數的標準無訊息安裝選項。 組態管理員的目前分支會提供勝過舊版的好處，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在向 Azure AD 進行驗證後，透過使用者認證以無訊息方式向 Azure AD 加入裝置。

若要控制裝置註冊，您應該將 Windows Installer 套件部署到您所選的 Windows 舊版裝置群組。

> [!NOTE]
> 如果未在 AD 中設定 SCP，則您應該遵循與在已加入網域的電腦上使用群組原則物件 (GPO) 所述的相同方法來設定) 的用戶端登錄 [設定](#configure-client-side-registry-setting-for-scp) 。


確認一切都如預期般運作之後，您可以 [使用 Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)來設定 SCP，以使用 Azure AD 自動註冊其餘的 Windows 目前和下層裝置。

## <a name="next-steps"></a>後續步驟

[規劃混合式 Azure Active Directory Join 實作](hybrid-azuread-join-plan.md)
