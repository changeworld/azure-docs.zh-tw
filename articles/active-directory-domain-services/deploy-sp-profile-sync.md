---
title: 使用 Azure AD DS 啟用共用點使用者設定檔服務 |微軟文檔
description: 瞭解如何配置 Azure 活動目錄域服務託管域以支援 SharePoint 伺服器的設定檔同步
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613862"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>配置 Azure 活動目錄域服務以支援 SharePoint 伺服器的使用者設定檔同步

SharePoint 伺服器包括用於同步使用者設定檔的服務。 此功能允許將使用者設定檔存儲在中心位置，並跨多個 SharePoint 網站和伺服器場訪問。 要配置 SharePoint Server 使用者設定檔服務，必須在 Azure 活動目錄域服務 （Azure AD DS） 託管域中授予適當的許可權。 有關詳細資訊，請參閱[SharePoint 伺服器 中的使用者設定檔同步](https://technet.microsoft.com/library/hh296982.aspx)。

本文介紹如何配置 Azure AD DS 以允許 SharePoint 伺服器使用者設定檔同步服務。

## <a name="before-you-begin"></a>開始之前

要完成本文，您需要以下資源和特權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要，請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要，請完成教程以創建[管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。
* 使用者設定檔同步服務的 SharePoint 服務帳戶。
    * 如果需要，請參閱[計畫共用點伺服器中的管理和服務帳戶][sharepoint-service-account]。

## <a name="service-accounts-overview"></a>服務帳戶概述

在 Azure AD DS 託管域中，名為**AAD DC 服務帳戶**的安全性群組作為*使用者*組織單元 （OU） 的一部分存在。 下列權限會委派給此安全性群組的成員：

- **複製根**DSE 上的目錄更改許可權。
- 複製 *"配置*命名內容"（`cn=configuration`容器）上的**目錄更改**許可權。

**AAD DC 服務帳戶**安全性群組也是內建群組**前 Windows 2000 相容訪問**的成員。

添加到此安全性群組時，SharePoint Server 使用者設定檔同步服務的服務帳戶將被授予正常工作所需的許可權。

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>啟用對 SharePoint 伺服器使用者設定檔同步的支援

SharePoint Server 的服務帳戶需要足夠的許可權來複製對目錄的更改，並讓 SharePoint 伺服器使用者設定檔同步正常工作。 要提供這些許可權，請將用於 SharePoint 使用者設定檔同步的服務帳戶添加到**AAD DC 服務帳戶**組。

從 Azure AD DS 管理 VM 中，完成以下步驟：

> [!NOTE]
> 要編輯 Azure AD DS 託管域中的組成員身份，必須登錄到*屬於 AAD DC 管理員組的*使用者帳戶。

1. 在"開始"螢幕中，選擇 **"管理工具**"。 在本教程中顯示了用於[創建管理 VM][tutorial-create-management-vm]的可用管理工具的清單。
1. 要管理組成員身份，請從管理工具清單中選擇**活動目錄管理中心**。
1. 在左側窗格中，選擇 Azure AD DS 託管域，如*aaddscontoso.com*。 將顯示現有非統組織和資源的清單。
1. 選擇 **"使用者**OU"，然後選擇*AAD DC 服務帳戶*安全性群組。
1. 選擇 **"成員**"，然後選擇 **"添加..."**
1. 輸入 SharePoint 服務帳戶的名稱，然後選擇 **"確定**"。 在下面的示例中，SharePoint 服務帳戶名為*spadmin*：

    ![將 SharePoint 服務帳戶添加到 AAD DC 服務帳戶安全性群組](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱為[SharePoint 伺服器中的設定檔同步授予活動目錄域服務許可權](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
