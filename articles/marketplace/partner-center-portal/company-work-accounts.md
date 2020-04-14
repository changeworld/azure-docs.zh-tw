---
title: 公司工作帳戶和合作夥伴中心
description: 如何檢查您的公司是否設置了 Microsoft 的工作帳戶、創建新的工作帳戶或設置多個工作帳戶以與合作夥伴中心一起使用。
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 3c0ee2f8d5291f7904435dea32d913adeaaf25c5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262361"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帳戶和合作夥伴中心

合作夥伴中心使用公司工作帳戶(也稱為 Azure 活動目錄 (AD) 租戶)來管理多個使用者的帳戶訪問、控制許可權、主機組和應用程式以及維護配置檔數據。 通過將公司的工作電子郵件帳戶域連結到合作夥伴中心帳戶,公司員工可以登錄到合作夥伴中心,使用自己的工作帳戶使用者名和密碼管理市場優惠。

## <a name="check-whether-your-company-already-has-a-work-account"></a>檢查您的公司是否已擁有工作帳戶

如果公司已訂閱了 Microsoft 雲端服務(如 Azure、Microsoft Intune 或 Office 365),則您已有一個工作電子郵件帳戶域(也稱為 Azure 活動目錄租戶),可與合作夥伴中心一起使用。

按照以下步驟檢查:
1. 登錄到中的https://portal.azure.comAzure 管理門戶。
2. 從左邊瀏覽選單中選擇**Azure 的動作目錄**,然後選擇**自訂網域名稱**。
3. 如果您已經有工作帳戶，將會列出您的網域名稱。

如果您的公司還沒有工作帳戶,將在合作夥伴中心註冊過程中為您創建一個工作帳戶。

## <a name="set-up-multiple-work-accounts"></a>設定多個工作帳戶

在決定使用現有工作帳戶之前,請考慮工作帳戶中需要訪問合作夥伴中心的用戶數。 如果工作帳戶中有不需要訪問合作夥伴中心的使用者,則可能需要考慮創建多個工作帳戶,以便僅在特定帳戶上表示需要訪問合作夥伴中心的使用者。

## <a name="create-a-new-work-account"></a>建立新的工作帳戶

要為公司創建新的工作帳戶,請按照以下步驟操作。 您可能需要向對公司的 Microsoft Azure 帳戶具有管理許可權的人員請求説明。

1. 登入[微軟 Azure 門戶](https://portal.azure.com)。
2. 從左邊瀏覽選單中,選擇**Azure 的目錄** -> **使用者**。
3. 選擇 **「新增使用者**」並透過輸入名稱和電子郵件地址建立新的 Azure 工作帳戶。 確保**目錄角色**設定為 **「使用者**」,並在底部選擇 **「顯示密碼**」複選框以查看並記下自動生成的密碼。
4. 選擇 **「建立**」 以儲存新使用者。

使用者帳戶的電子郵寄地址必須是目錄中已驗證的網域名稱。 通過在左側導航功能表中選擇**Azure 活動目錄** -> **自定義網功能變數名稱**,可以列出目錄中的所有已驗證域。

要瞭解有關在 Azure 活動目錄中新增自訂網域的更多內容,請參閱[在 Azure AD 中新增或關聯網域](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)。

## <a name="troubleshoot-work-email-sign-in"></a>排除工作電子郵件登入的故障

如果您在登錄到工作帳戶(也稱為 Azure AD 租戶)時遇到問題,請找到下圖中最適合您的情況的方案,然後按照建議的步驟操作。

![排除工作帳戶登入的故障排除圖](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>後續步驟

- [在合作夥伴中心管理您的商業市場帳戶](./manage-account.md) 
