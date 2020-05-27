---
title: 將應用程式標示為發行者已驗證 - Microsoft 身分識別平台 | Azure
description: 說明如何將應用程式標示為發行者已驗證。 當應用程式標示為「發行者已驗證」時，表示發行者已使用完成驗證程序的 Microsoft 合作夥伴網路 (MPN) 帳戶來通過身分驗證，並將此 MPN 帳戶與應用程式註冊相關聯。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595687"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>將應用程式標示為發行者已驗證 (預覽)

當應用程式標示為「發行者已驗證」時，表示發行者已使用其 Microsoft 合作夥伴網路 (MPN) 帳戶通過身分驗證，並將此 MPN 帳戶與其應用程式註冊相關聯。 本文說明如何完成[發行者驗證 (預覽)](publisher-verification-overview.md) 程序。

## <a name="quickstart"></a>快速入門
如果已在 Microsoft 合作夥伴網路 (MPN) 中註冊並符合[必要條件](publisher-verification-overview.md#requirements)，就可以立即開始使用： 

1. 瀏覽至[應用程式註冊入口網站](https://aka.ms/PublisherVerificationPreview)預覽版。

1. 選擇應用程式，然後按一下 [品牌]。 

1. 按一下 [新增 MPN 識別碼以驗證發行者]，然後檢閱所列的需求。

1. 輸入您的 MPN 識別碼，然後按一下 [驗證並儲存]。

如需特定權益、需求和常見問題的詳細資訊，請參閱 [概觀](publisher-verification-overview.md)。


## <a name="mark-your-app-as-publisher-verified"></a>將應用程式標示為發行者已驗證
請確定您已符合 [必要條件](publisher-verification-overview.md#requirements)，然後遵循下列步驟，將應用程式標示為「發行者已驗證」。  

1. 確保您已使用組織 (Azure AD) 帳戶登入，該帳戶已獲授權可對您要標示為「發行者已驗證」的應用程式以及合作夥伴中心的 MPN 帳戶進行變更。 

    - 在 Azure AD 中，此使用者必須是應用程式的擁有者，或是具有下列其中一種角色：應用程式管理員、雲端應用程式管理員、全域管理員。 

    - 在合作夥伴中心內，此使用者必須擁有下列其中一種角色：MPN 管理員、帳戶管理員或全域管理員 (這是在 Azure AD 中主控的共用角色)。 

1. 瀏覽至應用程式註冊入口網站預覽版：  

1. 按一下您想要標示為「發行者已驗證」的應用程式，然後開啟 [品牌] 刀鋒視窗。 

1. 確保已正確設定應用程式的發行者網域。 此網域必須： 

    - 新增至 Azure AD 租用戶成為經過 DNS 驗證的自訂網域，  

    - 符合在您的 MPN 帳戶驗證期間所使用的電子郵件地址網域。 

1. 按一下接近頁面底部的 [新增 MPN 識別碼以驗證發行者]。 

1. 輸入您的 [MPN 識別碼]。 此 MPN 識別碼必須用於： 

    - 已完成驗證程序的有效 Microsoft 合作夥伴網路帳戶。  

    - 貴組織的合作夥伴通用帳戶 (PGA)。 

1. 按一下 [確認並儲存]。 

1. 等待要求進行處理，這可能需要幾分鐘的時間。 

1. 如果驗證成功，發行者驗證視窗將會關閉，並返回 [品牌] 刀鋒視窗。 您會在已驗證的 [發行者顯示名稱] 旁看到藍色的已驗證徽章。 

1. 經系統提示同意您應用程式的使用者，會在您順利完成整個程序後開始看到徽章，雖然這可能需要一些時間才能在整個系統中複寫。 

1. 請登入您的應用程式並確定已驗證徽章顯示在同意畫面上，以測試此功能。 如果以已經對應用程式表示同意的使用者身分登入，即可使用 *prompt=consent* 查詢參數來強制執行同意提示。 

1. 視需要針對您想要顯示徽章的任何其他應用程式，重複執行此程序。 您可以使用 Microsoft Graph 更快速地大量執行此動作，而且很快就會提供 PowerShell Cmdlet。 如需詳細資訊，請參閱[進行 Microsoft API Graph 呼叫](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。 

這樣就大功告成了！ 讓我們知道您是否有任何關於程序、結果或一般功能的意見反應。 

## <a name="next-steps"></a>後續步驟
如果您遇到問題，請閱讀[疑難排解資訊](troubleshoot-publisher-verification.md)。