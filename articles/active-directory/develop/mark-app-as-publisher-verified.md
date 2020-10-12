---
title: 將應用程式標示為發行者已驗證 - Microsoft 身分識別平台 | Azure
description: 說明如何將應用程式標示為發行者已驗證。 當應用程式標示為「發行者已驗證」時，表示發行者已使用完成驗證程序的 Microsoft 合作夥伴網路 (MPN) 帳戶來通過身分驗證，並將此 MPN 帳戶與應用程式註冊相關聯。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 80731421b6a0d3f5bdabf117a7239bafa056e652
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258331"
---
# <a name="mark-your-app-as-publisher-verified"></a>將應用程式標示為發行者已驗證

當應用程式註冊有經過驗證的發行者時，即表示應用程式的發行者已使用其 Microsoft 合作夥伴網路 (MPN) 帳戶來 [驗證](/partner-center/verification-responses) 其身分識別，並已將此 MPN 帳戶與其應用程式註冊相關聯。 本文說明如何完成 [發行者驗證](publisher-verification-overview.md) 程式。

## <a name="quickstart"></a>快速入門
如果已在 Microsoft 合作夥伴網路 (MPN) 中註冊並符合[必要條件](publisher-verification-overview.md#requirements)，就可以立即開始使用： 

1. 使用[多重要素驗證](../fundamentals/concept-fundamentals-mfa-get-started.md)登入[應用程式註冊入口網站](https://aka.ms/PublisherVerificationPreview)

1. 選擇應用程式，然後按一下 [品牌]。 

1. 按一下 [新增 MPN 識別碼以驗證發行者]，然後檢閱所列的需求。

1. 輸入您的 MPN 識別碼，然後按一下 [驗證並儲存]。

如需特定權益、需求和常見問題的詳細資訊，請參閱 [概觀](publisher-verification-overview.md)。


## <a name="mark-your-app-as-publisher-verified"></a>將應用程式標示為發行者已驗證
請確定您已符合 [必要條件](publisher-verification-overview.md#requirements)，然後遵循下列步驟，將應用程式標示為「發行者已驗證」。  

1. 確定您已使用 [多重要素驗證](../fundamentals/concept-fundamentals-mfa-get-started.md) 登入組織 (Azure AD) 帳戶，該帳戶已獲授權可對應用 (程式進行變更) 您要標示為「發行者驗證」，並在合作夥伴中心的 MPN 帳戶上。

    - 在 Azure AD 此使用者必須是下列其中一個 [角色](../users-groups-roles/directory-assign-admin-roles.md)的成員：應用程式系統管理員、雲端應用程式系統管理員、全域管理員。 

    - 在合作夥伴中心內，此使用者必須擁有下列其中一種[角色](/partner-center/permissions-overview)：MPN 管理員、帳戶管理員或全域管理員 (這是在 Azure AD 中主控的共用角色)。 

1. 流覽至應用程式註冊入口網站：  

1. 按一下您想要標示為「發行者已驗證」的應用程式，然後開啟 [品牌] 刀鋒視窗。 

1. 確定已設定應用程式的 [發行者網域](howto-configure-publisher-domain.md) 。 

1. 確定租使用者中的發行者網域或 DNS 驗證的 [自訂網域](../fundamentals/add-custom-domain.md) ，與 MPN 帳戶驗證程式期間使用的電子郵件地址網域相符。

1. 按一下接近頁面底部的 [新增 MPN 識別碼以驗證發行者]。 

1. 輸入您的 [MPN 識別碼]。 此 MPN 識別碼必須用於： 

    - 已完成驗證程序的有效 Microsoft 合作夥伴網路帳戶。  

    - 貴組織的合作夥伴通用帳戶 (PGA)。 

1. 按一下 [確認並儲存]。 

1. 等待要求進行處理，這可能需要幾分鐘的時間。 

1. 如果驗證成功，發行者驗證視窗將會關閉，並返回 [品牌] 刀鋒視窗。 您會在已驗證的 [發行者顯示名稱] 旁看到藍色的已驗證徽章。 

1. 經系統提示同意您應用程式的使用者，會在您順利完成整個程序後開始看到徽章，雖然這可能需要一些時間才能在整個系統中複寫。 

1. 請登入您的應用程式並確定已驗證徽章顯示在同意畫面上，以測試此功能。 如果以已經對應用程式表示同意的使用者身分登入，即可使用 *prompt=consent* 查詢參數來強制執行同意提示。 此參數只適用于測試，且永遠不會對應用程式的要求進行硬式編碼。

1. 視需要針對您想要顯示徽章的任何其他應用程式，重複執行此程序。 您可以使用 Microsoft Graph 更快速地大量執行此動作，而且很快就會提供 PowerShell Cmdlet。 如需詳細資訊，請參閱[進行 Microsoft API Graph 呼叫](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。 

這樣就大功告成了！ 讓我們知道您是否有任何關於程序、結果或一般功能的意見反應。 

## <a name="next-steps"></a>後續步驟
如果您遇到問題，請閱讀[疑難排解資訊](troubleshoot-publisher-verification.md)。
