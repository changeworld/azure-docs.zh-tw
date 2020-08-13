---
title: 大量邀請 B2B 共同作業使用者的教學課程 - Azure AD
description: 在此教學課程中，您會了解如何使用 PowerShell 和 CSV 檔案，對外部 Azure AD B2B 共同作業使用者傳送大量邀請。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87906812"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>教學課程：大量邀請 Azure AD B2B 共同作業使用者

如果您使用 Azure Active Directory (Azure AD) B2B 共同作業來與外部合作夥伴合作，您便可以同時邀請多位來賓使用者存取您的組織資源。 在此教學課程中，您會了解如何使用 Azure 入口網站對外部使用者傳送大量邀請。 具體而言，您會執行下列操作：

> [!div class="checklist"]
> * 使用**大量邀請使用者**來準備包含使用者資訊和邀請偏好的逗號分隔值 (.csv) 檔案
> * 將 .csv 檔案上傳至 Azure AD
> * 確認是否已將使用者新增至目錄

如果您沒有 Azure Active Directory，請在開始之前[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="understand-the-csv-template"></a>了解 CSV 範本

請下載並填入大量上傳 CSV 範本，以順利大量邀請 Azure AD 來賓使用者。 您下載的 CSV 範本可能會如下列範例所示：

![上傳的試算表，以及說明每個資料列和資料行之用途和值的圖說文字](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV 範本結構

下載的 CSV 範本中的資料列如下所示：

- **版本號碼**：包含版本號碼的第一個資料列必須包含在上傳 CSV 中。
- **資料行標題**：資料行標題的格式為 &lt;*項目名稱*&gt; [PropertyName] &lt;*必要或空白*&gt;。 例如： `Email address to invite [inviteeEmail] Required` 。 某些舊版的範本可能會略有不同。
- **範例資料列**：我們在範本中隨附了每個資料行可接受之值的範例資料列。 您必須移除範例資料列，並將其取代為您自己的項目。

### <a name="additional-guidance"></a>其他指導方針

- 上傳範本的前兩個資料列不可移除或修改，否則將無法處理上傳。
- 首先會列出必要的資料行。
- 我們不建議您將新的資料行新增至範本。 您額外新增的任何資料行都會被忽略，且不會進行處理。
- 我們建議您盡可能頻繁下載最新版的 CSV 範本。

## <a name="prerequisites"></a>Prerequisites

您需要有兩個以上可接收邀請的測試電子郵件帳戶。 這些帳戶必須來自您的組織外部。 您可以使用任何類型的帳戶，包括社交帳戶，例如 gmail.com 或 outlook.com 地址。

## <a name="invite-guest-users-in-bulk"></a>大量邀請來賓使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 底下選取 [使用者] > [大量邀請]。
4. 在 [大量邀請使用者] 頁面上選取 [下載]，以取得具有邀請屬性的有效 .csv 範本。

    ![大量邀請的下載按鈕](media/tutorial-bulk-invite/bulk-invite-button.png)

5. 開啟 .csv 範本，並為每個來賓使用者新增一行。 必要值為：

   * **要邀請的電子郵件地址** - 將接收邀請的使用者

   * **重新導向 URL** - 受邀使用者接受邀請之後將前往的目標 URL

    ![已輸入來賓使用者的 CSV 檔案範例](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > 請勿在 [自訂邀請訊息] 中使用逗號，因為這樣會使訊息無法成功進行剖析。

6. 儲存檔案。
7. 在 [大量邀請使用者] 頁面上的 [上傳您的 csv 檔案] 底下，瀏覽至該檔案。 當您選取檔案時，就會開始驗證 .csv 檔案。 
8. 當檔案內容經過驗證後，您會看到「檔案已成功上傳」。 如果發生錯誤，您必須修正這些錯誤，才能提交作業。
9. 當您的檔案通過驗證時，請選取 [提交] 以啟動可新增邀請的 Azure 大量作業。 
10. 若要查看作業狀態，請選取 [按一下這裡以查看每項作業的狀態]。 或者，您可以在 [活動] 區段中選取 [大量作業結果]。 如需大量作業中每行項目的詳細資料，請選取 [# 項成功]、[# 項失敗] 或 [要求總數] 資料行底下的值。 如果發生失敗，將會列出失敗的原因。

    ![大量作業結果的範例](media/tutorial-bulk-invite/bulk-operation-results.png)

11. 當作業完成時，您會看到大量作業成功的通知。

## <a name="verify-guest-users-in-the-directory"></a>驗證目錄中的來賓使用者

在 Azure 入口網站中或使用 PowerShell 來檢查您新增的來賓使用者是否存在目錄中。

### <a name="view-guest-users-in-the-azure-portal"></a>在 Azure 入口網站中檢視來賓使用者

1. 使用組織中的「使用者管理員」帳戶來登入 Azure 入口網站。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 底下選取 [使用者]。
4. 在 [顯示] 底下，選取 [僅來賓使用者]，並確認您新增的使用者已列出。

### <a name="view-guest-users-with-powershell"></a>使用 PowerShell 檢視來賓使用者

執行以下命令：

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

您應該會看到系統以 *emailaddress*#EXT#\@*domain*格式的使用者主體名稱 (UPN) 列出您所邀請的使用者。 例如 *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*，其中 contoso.onmicrosoft.com 是您傳送邀請時的來源組織。

## <a name="clean-up-resources"></a>清除資源

若不再需要測試使用者帳戶，您可以在 Azure 入口網站中的 [使用者] 頁面上，將其從目錄中刪除，只要選取來賓使用者旁的核取方塊，然後選取[刪除] 即可。 

或者，您可以執行下列 PowerShell 命令來刪除使用者帳戶：

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

例如： `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已對組織外部的來賓使用者傳送大量邀請。 接著，將了解邀請兌換程序如何運作。

> [!div class="nextstepaction"]
> [了解 Azure AD B2B 共同作業邀請兌換程序](redemption-experience.md)
