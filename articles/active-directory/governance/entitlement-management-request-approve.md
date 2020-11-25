---
title: 核准或拒絕存取要求-Azure AD 權利管理
description: 瞭解如何使用我的存取權入口網站，在 Azure Active Directory 權利管理中核准或拒絕存取套件的要求。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfbd5e6f190e0600c31aa17e9b1a75f08e213df4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973570"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中核准或拒絕存取要求

您可以使用 Azure AD 權利管理來設定原則，以要求存取套件的核准，然後選擇一或多個核准者。 本文說明指定的核准者可以如何核准或拒絕存取套件的要求。

## <a name="open-request"></a>開啟要求

核准或拒絕存取要求的第一個步驟是尋找並開啟待核准的存取要求。 有兩種方式可開啟存取要求。

**先決條件角色：** 人員

1. 尋找要求您核准或拒絕要求的 Microsoft Azure 電子郵件。 以下是範例電子郵件：

    ![核准存取套件電子郵件的要求](./media/entitlement-management-shared/approver-request-email.png)

1. 按一下 [ **核准或拒絕要求** ] 連結以開啟存取要求。

1. 登入我的存取權入口網站。

如果您沒有電子郵件，可以依照下列步驟，找到等待您核准的存取要求。

1. 登入我的存取權入口網站 [https://myaccess.microsoft.com](https://myaccess.microsoft.com) 。  適用于美國政府的 (，我的存取權入口網站連結中的網域將會是 `myaccess.microsoft.us` 。 ) 

1. 在左側功能表中，按一下 [ **核准** ] 以查看正在等待核准的存取要求清單。

1. 在 [ **擱置** 中] 索引標籤上，尋找要求。

## <a name="view-requestors-answers-to-questions-preview"></a> (Preview 查看要求者對問題的解答) 

1. 流覽至我的存取權中的 [ **核准** ] 索引標籤。

1. 移至您想要核准的要求，然後按一下 [ **詳細資料**]。 如果您已準備好進行決策，您也可以按一下 [ **核准** ] 或 [ **拒絕** ]。

1. 按一下 [ **要求詳細資料**]。

     ![我的存取權入口網站-存取要求-按一下要求詳細資料](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. 要求者提供的資訊將位於面板底部。

     ![螢幕擷取畫面顯示要求的詳細資料](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. 根據要求者提供的資訊，您可以接著核准或拒絕要求。 請參閱核准或拒絕要求指引中的步驟。

## <a name="approve-or-deny-request"></a>核准或拒絕要求

在您開啟等待核准的存取要求之後，您可以看到可協助您進行核准或拒絕決策的詳細資料。

**先決條件角色：** 人員

1. 按一下 [檢視] 連結以開啟 [存取要求] 窗格。

1. 按一下 [ **詳細資料** ] 以查看存取要求的詳細資料。

    這些詳細資料包括使用者的名稱、組織、存取開始和結束日期（如有提供）、商務理由、提交要求的時間，以及要求將到期的時間。

1. 按一下 [ **核准** ] 或 [ **拒絕**]。

1. 如有必要，請輸入原因。

    ![螢幕擷取畫面顯示您接受或拒絕要求的頁面。](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. 按一下 [提交] 以提交您的決定。

    如果原則設定有多個核准者，只有一位核准者需要決定待核准。 核准者提交其對存取要求的決策之後，要求便會完成，而且其他核准者也無法再核准或拒絕要求。 其他核准者可以在其我的存取權入口網站中查看要求決策和決策 maker。 目前只支援單一階段核准。

    如果未設定的核准者都無法核准或拒絕存取要求，則要求會在設定的要求持續時間之後過期。 使用者會收到其存取要求已過期，且需要重新提交存取要求的通知。

## <a name="next-steps"></a>後續步驟

- [要求存取存取套件](entitlement-management-request-access.md)
- [要求進程和電子郵件通知](entitlement-management-process.md)
