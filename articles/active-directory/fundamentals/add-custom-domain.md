---
title: 新增您的自訂網域 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 新增自訂網域的指示。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8035744cd7ff7bb3d8347a7d26796531a666bbd
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796806"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>使用 Azure Active Directory 入口網站新增自訂網域名稱

每個新的 Azure AD 租使用者都隨附一個初始功能變數名稱* \<domainname> onmicrosoft.com*。 您無法變更或刪除初始功能變數名稱，但可以新增貴組織的名稱。 新增自訂功能變數名稱可協助您建立使用者熟悉的使用者名稱，例如*alain \@ contoso.com*。

## <a name="before-you-begin"></a>開始之前

在您可以新增自訂功能變數名稱之前，請先使用網域註冊機構建立您的功能變數名稱。 如需公認的網域註冊機構，請參閱 [ICANN 認可的註冊機構](https://www.icann.org/registrar-reports/accredited-list.html)。

## <a name="create-your-directory-in-azure-ad"></a>在 Azure AD 中建立您的目錄

取得您的網域名稱之後，您可以建立您的第一個 Azure AD 目錄。 使用具有訂用帳戶「**擁有**者」角色的帳戶，登入您目錄的 Azure 入口網站。

遵循[為您的組織建立新的租用戶](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)中的步驟，以建立新的目錄。

>[!IMPORTANT]
>建立租用戶的人員會自動成為該租用戶的全域管理員。 全域管理員可以將其他系統管理員新增至租用戶。

如需訂用帳戶角色的詳細資訊，請參閱[Azure 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。

>[!TIP]
> 如果您計畫將內部部署 Windows Server AD 與 Azure AD 建立同盟，則需要選取 [我打算在執行 Azure AD Connect 工具來同步處理您的目錄時，**將此網域設定為使用我的本機 Active Directory 進行單一登入**]。
>
> 您也需要註冊相同的網域名稱，您選取該名稱以與精靈中 **Azure AD 網域** 步驟中的內部部署目錄同盟。 若要查看該安裝程式的外觀，請參閱[確認為同盟選取的 Azure AD 網域](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)。 如果您沒有 Azure AD Connect 工具，可以在[這裡下載](https://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-your-custom-domain-name-to-azure-ad"></a>將自訂網域名稱新增至 Azure AD

建立您的目錄之後，您可以新增自訂網域名稱。

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從任何頁面搜尋並選取 [Azure Active Directory]。 然後選取 [**自訂功能變數名稱**] [  >  **新增自訂網域**]。

    ![[自訂功能變數名稱] 頁面，顯示 [新增自訂網域]](media/add-custom-domain/add-custom-domain.png)

1. 在 [**自訂功能變數名稱**] 中，輸入您組織的新名稱，在此範例中為*contoso.com*。 選取 [新增網域]。

    ![[自訂功能變數名稱] 頁面，使用 [新增自訂網域] 頁面](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >您必須包括 *.com*、 *.net*或任何其他最上層延伸模組，才能正常運作。

    已新增未驗證的網域。 [ **Contoso.com** ] 頁面隨即出現，顯示您的 DNS 資訊。 請儲存此資訊。 您稍後需要用它來建立 TXT 記錄以設定 DNS。

    ![包含 DNS 項目資訊的 Contoso 頁面](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>將 DNS 資訊新增至網域註冊機構

將自訂網域名稱新增至 Azure AD 之後，您必須回到您的網域註冊機構，並新增從您複製的 TXT 檔案中的 Azure AD DNS 資訊。 為您的網域建立此 TXT 記錄會驗證您的功能變數名稱擁有權。

回到您的網域註冊機構，根據您複製的 DNS 資訊，為您的網域建立新的 TXT 記錄。 將 [存留時間 (TTL) 設定為3600秒 (60 分鐘) ，然後儲存記錄。

>[!IMPORTANT]
>您可以註冊您想要的多個網域名稱。 不過，每個網域會從 Azure AD 取得自己的 TXT 記錄。 當您在網域註冊機構輸入 TXT 檔案資訊時，請務必小心。 如果您不小心輸入錯誤或重複的資訊，則必須等到 TTL)  (60 分鐘後再試一次。

## <a name="verify-your-custom-domain-name"></a>驗證自訂網域名稱

在您註冊自訂功能變數名稱之後，請確定它在 Azure AD 中是有效的。 從您的網域註冊機構到 Azure AD 的傳播可以是瞬間的，也可能需要幾天的時間，視您的網域註冊機構而定。

若要驗證您的自訂功能變數名稱，請遵循下列步驟：

1. 使用目錄的全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

1. 搜尋並從任何頁面中選取 [ *Azure Active Directory* ]，然後選取 [**自訂功能變數名稱**]。

1. 在 [**自訂功能變數名稱**] 中，選取自訂功能變數名稱。 在此範例中，選取 [ **contoso.com**]。

    ![[Fabrikam - 自訂網域名稱] 頁面，已醒目提示 contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. 在 [ **contoso.com** ] 頁面上，選取 [**驗證**] 以確定您的自訂網域已正確註冊，而且對 Azure AD 有效。

    ![包含 DNS 項目資訊和 [驗證] 按鈕的 Contoso 頁面](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

驗證自訂功能變數名稱之後，您可以刪除驗證 TXT 或 MX 檔案。

## <a name="common-verification-issues"></a>常見驗證問題

如果 Azure AD 無法驗證自訂網域名稱，請嘗試下列建議：

- **至少等候一小時，然後再試一次**。 DNS 記錄必須在 Azure AD 驗證網域之後傳播。 此流程可能需要一個小時以上。

- **確定 DNS 記錄正確無誤。** 回到網域註冊機構網站。 請確定專案位於該處，而且符合 Azure AD 所提供的 DNS 專案資訊。

  如果您無法更新註冊機構網站上的記錄，請與具有加入該專案之許可權的使用者共用該專案，並確認它是正確的。

- **確定網域名稱並未在另一個目錄中使用。** 功能變數名稱只能在一個目錄中進行驗證。 如果您的功能變數名稱目前已在另一個目錄中進行驗證，也無法在新的目錄中進行驗證。 若要修正此重複問題，您必須從舊的目錄中刪除網域名稱。 如需如何刪除網域名稱的詳細資訊，請參閱 [管理自訂網域名稱](../users-groups-roles/domains-manage.md)。

- **確定您沒有任何非受控 Power BI 租用戶。** 如果您的使用者已透過自助式註冊啟用 Power BI 並針對貴組織建立非受控租用戶，您就必須使用 PowerShell 以內部或外部管理員身分接管管理。 如需詳細資訊，請參閱[如何以系統管理員身分接管 Azure Active Directory 中非受控目錄](../users-groups-roles/domains-admin-takeover.md)。

## <a name="next-steps"></a>後續步驟

- 將其他全域管理員新增至您的目錄。 如需詳細資訊，請參閱[如何指派角色和系統管理員](active-directory-users-assign-role-azure-portal.md)。

- 將使用者新增至您的網域。 如需詳細資訊，請參閱[如何新增或刪除使用者](add-users-azure-active-directory.md)。

- 在 Azu e AD 中管理網域名稱資訊。 如需詳細資訊，請參閱[管理自訂網域名稱](../users-groups-roles/domains-manage.md)。

- 如果您有想要與 Azure Active Directory 一起使用的內部部署 Windows Server 版本，請參閱[整合內部部署目錄與 Azure Active Directory](../connect/active-directory-aadconnect.md)。
