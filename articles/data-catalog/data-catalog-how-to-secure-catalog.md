---
title: 如何保護對 Azure 資料目錄的存取
description: 本文說明如何保護 Azure 資料目錄中的資料目錄和資料資產。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 6a429c09b6f8082c95e29bcea62d27ec4fb46fd3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017299"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>如何保護對資料目錄及資料資產的存取

> [!IMPORTANT]
> 只有在標準版「Azure 資料目錄」中才有提供此功能。

「Azure 資料目錄」可讓您指定誰可以存取資料目錄，以及他們可以對目錄中的中繼資料執行哪些作業 (註冊、標註、取得擁有權)。 

## <a name="catalog-users-and-permissions"></a>目錄使用者和權限

將資料目錄的存取權授與使用者或群組並設定權限：

1. 在[您資料目錄的首頁](https://www.azuredatacatalog.com)，按一下工具列上的 [設定]。

   ![Azure 資料目錄首頁設定] 按鈕](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. 在 [設定] 頁面中，展開 [目錄使用者] 區段。

   ![Azure 資料目錄使用者新增按鈕](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. 按一下 [新增] 。

4. 在與此目錄關聯的 Azure Active Directory (AAD) 中輸入完整的「使用者名稱」或「安全性群組」的名稱。 如果您要新增多個使用者或群組，請使用逗號 (`,’) 作為分隔符號。

   ![Azure 資料目錄使用者-使用者或群組](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. 按 **ENTER** 或 **TAB** 來移出文字方塊。 

6. 確認所有權限 ([標註]、[註冊] 及 [取得擁有權]) 皆已預設指派給這些使用者或群組。 也就是說，使用者或群組可以[註冊資料資產]( data-catalog-how-to-register.md)、[標註資料資產]( data-catalog-how-to-annotate.md)，以及[取得資料資產的擁有權]( data-catalog-how-to-manage.md)。 

   ![Azure 資料目錄使用者-預設許可權](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. 若只想將目錄的讀取存取權授與使用者或群組，請將該使用者或群組的 [標註] 選項取消選取。 當您這麼做時，該使用者或群組便無法標註目錄中的資料資產，但可以檢視它們。 

8. 若要拒絕使用者或群組註冊資料資產，請將該使用者或群組的 [註冊] 選項取消選取。

9. 若要拒絕使用者取得資料資產的擁有權，請將該使用者或群組的 [取得擁有權] 選項取消選取。 

10. 若要從目錄使用者中刪除某個使用者/群組，請按一下清單底部該使用者/群組的 [x]。 

   ![Azure 資料目錄目錄使用者-刪除使用者 X 圖示](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > 建議您將安全性群組新增到目錄使用者中，而不要直接新增使用者並指派權限。 然後，將使用者新增到符合其角色及所需目錄存取權的安全性群組中。

## <a name="special-considerations"></a>特殊考量

- 指派給安全性群組的權限是附加的。 假設使用者位於兩個群組中。 一個群組具有標註權限，而另一個群組沒有標註權限。 那麼，使用者會具有標註權限。 
- 明確指派給使用者的權限會覆寫指派給使用者所屬群組的權限。 在上述範例中，假設您已將使用者明確新增到目錄使用者中，而未指派標註權限。 那麼，儘管該使用者所屬群組具有標註權限，他也無法標註資料資產。

## <a name="next-steps"></a>後續步驟

- [開始使用 Azure 資料目錄](data-catalog-get-started.md)
