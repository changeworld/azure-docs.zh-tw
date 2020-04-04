---
title: 刪除 Azure 活動目錄網域服務 |微軟文件
description: 瞭解如何使用 Azure 門戶關閉或移除 Azure 活動目錄域服務託管域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655473"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>使用 Azure 門戶移除 Azure 的目錄網域服務託管域

如果不再需要託管域,可以刪除 Azure 活動目錄域服務 (Azure AD DS) 實例。 沒有關閉或暫時禁用 Azure AD DS 託管域的選項。 刪除 Azure AD DS 託管域不會刪除 Azure AD 租戶或以其他方式對 Azure AD 租戶產生不利影響。 本文介紹如何使用 Azure 門戶刪除 Azure AD DS 託管域。

> [!WARNING]
> **刪除是永久性的,無法撤銷。**
> 刪除 Azure AD DS 託管域時,將執行以下步驟:
>   * 受控網域的網域控制站會解除佈建，並從虛擬網路中移除。
>   * 受控網域上的資料會永久刪除。 此資料包括您創建的自定義 O、GPO、自定義 DNS 記錄、服務主體、GMSA 等。
>   * 聯結至受控網域的機器會失去與網域間的信任關係，且必須從網域中解除聯結。
>       * 您無法使用公司 AD 認證登入這些電腦。 相反,您必須對電腦使用本地管理員憑據。

## <a name="delete-the-managed-domain"></a>刪除託管域

要刪除 Azure AD DS 託管域,請完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**Azure AD 網域服務**。
1. 選擇 Azure AD DS 託管網域的名稱,如*aaddscontoso.com*。
1. 在 [概觀]**** 頁面上，按一下 [刪除]****。 要確認刪除,請再次鍵入託管域的功能變數名稱,然後選擇 **"刪除**"。

刪除 Azure AD DS 託管域可能需要 15-20 分鐘或更長時間。

## <a name="next-steps"></a>後續步驟

請考慮[共享][feedback]有關您希望在 Azure AD DS 中查看的功能的回饋。

如果要再次開始使用 Azure AD DS,請參考[建立及設定 Azure 的目錄網域服務實體 。][create-instance]

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
