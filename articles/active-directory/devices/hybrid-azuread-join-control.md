---
title: 混合 Azure AD 聯接的受控驗證 - Azure AD
description: 瞭解如何對整個組織同時啟用混合 Azure AD 聯接進行受控驗證
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049986"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>混合式 Azure AD 聯結的受控驗證

當所有先決條件都到位時，Windows 設備將自動註冊為 Azure AD 租戶中的設備。 Azure AD 中的這些設備標識的狀態稱為混合 Azure AD 聯接。 有關本文仲介紹的概念的詳細資訊，請參閱[Azure 活動目錄中的裝置管理簡介](overview.md)和[計畫混合 Azure 活動目錄加入實現](hybrid-azuread-join-plan.md)。

組織可能需要對混合 Azure AD 聯接進行受控驗證，然後再在整個組織中同時啟用它。 本文將解釋如何完成對混合 Azure AD 聯接的受控驗證。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>在 Windows 當前設備上對混合 Azure AD 聯接進行受控驗證

對於執行 Windows 桌面作業系統的裝置，支援的版本為 Windows 10 年度更新版 (版本 1607) 或更新版本。 最佳做法是升級至最新版的 Windows 10。

要在當前 Windows 設備上對混合 Azure AD 聯接進行受控驗證，您需要：

1. 如果活動目錄 （AD） 存在，請清除服務連接點 （SCP） 條目
1. 使用群組原則物件 （GPO） 在域加入的電腦上配置 SCP 的用戶端註冊表設置
1. 如果使用 AD FS，還必須使用 GPO 在 AD FS 伺服器上配置 SCP 的用戶端註冊表設置  
1. 您可能還需要在 Azure AD Connect 中[自訂同步選項](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)以啟用設備同步。 


### <a name="clear-the-scp-from-ad"></a>從 AD 中清除 SCP

使用活動目錄服務介面編輯器（ADSI 編輯）修改 AD 中的 SCP 物件。

1. 啟動**ADSI 編輯**桌面應用程式，從和管理工作站或網域控制站作為企業管理員。
1. 連接到域的**配置命名內容**。
1. 流覽到**CN=配置，DC=contoso，DC_com** > **CN_服務** > **CN=設備註冊配置**
1. 按右鍵葉物件**CN=62a0ff2e-97b9-4513-943f-0d221bd30080**並選擇**屬性**
   1. 從**屬性編輯器**視窗中選擇**關鍵字**，然後按一下 **"編輯"**
   1. 選擇**azureADId**和**azureADName**的值（一次一個），然後按一下"**刪除"**
1. 關閉**ADSI 編輯**


### <a name="configure-client-side-registry-setting-for-scp"></a>為 SCP 配置用戶端註冊表設置

使用以下示例創建群組原則物件 （GPO） 以部署在設備註冊表中配置 SCP 條目的註冊表設置。

1. 打開群組原則管理主控台，並在域中創建新的群組原則物件。
   1. 為新創建的 GPO 提供名稱（例如，用戶端SCP）。
1. 編輯 GPO 並找到以下路徑：**電腦配置** > **首選項** > **Windows 設置** > **註冊表**
1. 按右鍵註冊表並選擇**新** > **登錄機碼**
   1. 在 **"常規"** 選項卡上，配置以下內容
      1. 操作：**更新**
      1. 蜂巢 **：HKEY_LOCAL_MACHINE**
      1. 關鍵路徑：**軟體_微軟_視窗\當前版本_CDJ_AAD**
      1. 值名稱：**租戶 Id**
      1. 數值型別 **：REG_SZ**
      1. 值資料：Azure AD 實例的 GUID 或**目錄 ID（** 此值可在**Azure 門戶** > **Azure 活動目錄** > **屬性** > **目錄 ID**中找到 ）
   1. 按一下 [確定]****。
1. 按右鍵註冊表並選擇**新** > **登錄機碼**
   1. 在 **"常規"** 選項卡上，配置以下內容
      1. 操作：**更新**
      1. 蜂巢 **：HKEY_LOCAL_MACHINE**
      1. 關鍵路徑：**軟體_微軟_視窗\當前版本_CDJ_AAD**
      1. 值名稱：**租戶名稱**
      1. 數值型別 **：REG_SZ**
      1. 價值資料 **：如果您使用的**是聯合環境（如 AD FS）。" 例如，**domain name**如果您使用的是託管環境，`contoso.onmicrosoft.com`則已驗證的功能變數名稱或onmicrosoft.com功能變數名稱
   1. 按一下 [確定]****。
1. 關閉新創建的 GPO 的編輯器
1. 將新創建的 GPO 連結到包含屬於受控卷展填充的域聯接電腦的所需 OU

### <a name="configure-ad-fs-settings"></a>配置 AD FS 設置

如果您使用的是 AD FS，則首先需要使用上述說明配置用戶端 SCP，將 GPO 連結到 AD FS 伺服器。 SCP 物件定義設備物件的權威源。 它可以是本地廣告或 Azure AD。 當用戶端 SCP 配置為 AD FS 時，設備物件的源將建立為 Azure AD。

> [!NOTE]
> 如果未能在 AD FS 伺服器上配置用戶端 SCP，則設備標識的源將被視為本地。 然後，ADFS 將在 ADFS 設備註冊的屬性"最大非活動日"中定義的規定期間後，開始從本地目錄中刪除設備物件。 可以使用[獲取 Adfs 設備註冊 Cmdlet 找到 ADFS](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)設備註冊物件。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>在 Windows 低級設備上對混合 Azure AD 聯接進行受控驗證

若要註冊舊版 Windows 裝置，組織必須安裝可在 Microsoft 下載中心取得的[適用於非 Windows 10 電腦的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)。

您可以使用軟體發佈系統 (例如  [Microsoft Endpoint Configuration Manager](/configmgr/)) 來部署此套件。 此套件支援使用 quiet 參數的標準無訊息安裝選項。 組態管理員的目前分支會提供勝過舊版的好處，例如能夠追蹤已完成的註冊。

安裝程式會在系統上建立排定的工作，此工作是在使用者內容中執行。 此工作會在使用者登入 Windows 時觸發。 此工作會在向 Azure AD 進行驗證後，透過使用者認證以無訊息方式向 Azure AD 加入裝置。

要控制設備註冊，應將 Windows 安裝程式包部署到所選的 Windows 低級設備組。

> [!NOTE]
> 如果在 AD 中未配置 SCP，則應遵循與使用群組原則物件 （GPO） 在域聯接的電腦上[配置 SCP 用戶端註冊表設置](#configure-client-side-registry-setting-for-scp)相同的方法。


驗證一切是否按預期工作後，可以使用[Azure AD 配置 SCP，](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)自動將 Windows 的其餘當前和下級設備註冊到 Azure AD。

## <a name="next-steps"></a>後續步驟

[規劃混合式 Azure Active Directory Join 實作](hybrid-azuread-join-plan.md)
