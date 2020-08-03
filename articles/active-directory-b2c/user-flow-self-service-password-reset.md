---
title: Azure Active Directory B2C 中的自助式密碼重設 | Microsoft Docs
description: 示範如何在 Azure Active Directory B2C 中為您的客戶設定自助式密碼重設
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481490"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>設定客戶的自助式密碼重設

自助式密碼重設功能可讓已註冊本機帳戶的客戶自行重設自己的密碼。 這可大幅減輕支援人員的負擔，特別是在您的應用程式具有數百萬名定期使用的客戶時更是如此。 目前僅支援使用已驗證的電子郵件地址作為復原方法。

> [!NOTE]
> 本文適用于在標準登入使用者流程的內容中使用的自助式密碼重設，此功能會使用**本機帳戶**登**入**做為身分識別提供者。 如果您需要從應用程式叫用的可完全自訂密碼重設使用者流程，請參閱 [這篇文章](user-flow-overview.md)。
>
>

依預設，您的目錄並不會開啟自助式密碼重設。 使用下列步驟將其開啟：

1. 以訂用帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。 此為您建立目錄時所用的工作或學校帳戶，或者當時所用的 Microsoft 帳戶。
2. 開啟 **Azure Active Directory** (位於左側導覽列中)。
3. 在 [選項] 分頁上向下滾動，然後選取 [**密碼重設**]。
4. 將 [啟用自助式密碼重設]**** 設為 [全部]****。
5. 按一下頁面頂端的 [儲存]  。 大功告成！

如果要進行測試，請針對所有將本機帳戶作為識別提供者的登入使用者流程使用 [立即執行] 功能。 在本機帳戶登入頁面上 (您輸入電子郵件地址和密碼，或使用者名稱和密碼的頁面)，按一下 [無法存取您的帳戶？]**** 驗證客戶體驗。

> [!NOTE]
> 您可以使用 [公司商標功能](../active-directory/fundamentals/customize-branding.md)自訂自助式密碼重設頁面。
>
>

