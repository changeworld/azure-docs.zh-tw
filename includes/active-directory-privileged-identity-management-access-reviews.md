---
title: 包含檔案
description: 包含檔案
services: active-directory
author: barclayn
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: cbcd4b459faa3bf67f591cc7afab0bf0027062e1
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842365"
---
## <a name="create-one-or-more-access-reviews"></a>建立一或多個存取權檢閱

1. 按一下 [新增]以建立新的存取權檢閱。

1. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. 設定 [開始日期]。 根據預設，存取權檢閱會在其建立的相同時間開始，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

    ![開始日期、頻率、持續時間、結束、次數和結束日期](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. 若要進行週期性存取權檢閱，請將 [頻率] 設定從 [單次] 變更為 [每週]、[每月]、[每季]、[每年] 或 [每半年]。 使用 [持續時間] 滑桿或文字方塊，定義週期性系列的每次檢閱將開放檢閱者輸入的天數。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。

1. 使用 [結束] 設定來指定如何結束週期性存取權檢閱系列。 此系列的結束方式有三種：它會持續執行而無限期地啟動檢閱、直到特定日期為止，或是在完成所定義的發生次數之後。 您、其他使用者管理員或其他全域管理員皆可以變更 [設定] 中的日期，以在系列建立之後予以停止，使其於該日期結束。

1. 在 [使用者] 區段中，選取您要檢閱其成員資格的一或多個角色。

    ![檢閱角色成員資格所屬的使用者範圍](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - 此處選取的角色包括[永久和符合資格的角色](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)。
    > - 選取一個以上的角色將會建立多個存取權檢閱。 例如，選取五個角色將會建立五個不同的存取權檢閱。

    如果您要建立 **Azure AD 角色** 的存取權檢閱，下圖顯示檢閱成員資格清單的範例。

    ![檢閱成員資格窗格，其中列出您可選取的 Azure AD 角色](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    如果您要建立 **Azure 資源角色** 的存取權檢閱，下圖顯示檢閱成員資格清單的範例。

    ![檢閱成員資格窗格，其中列出您可選取的 Azure 資源角色](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. 在 [檢閱者] 區段中，選取一或多個人員來檢閱所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。

    ![所選使用者或成員 (自我) 的檢閱者清單](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **選取的使用者**- 如果您不知道誰需要存取權，請使用此選項。 使用此選項，您可以指派資源擁有者或群組管理員完成檢閱。
    - **成員 (自我)** - 若要讓使用者檢閱自己的角色指派，請使用此選項。
    - **(Preview) 管理員** -使用此選項可讓使用者的經理審核其角色指派。 選取 (Preview) Manager 時，您也可以選擇指定回溯審核者。 當使用者未在目錄中指定管理員時，系統會要求回溯審核者審核使用者。

### <a name="upon-completion-settings"></a>完成時的設定

1. 若要指定檢閱完成後所應執行的動作，請展開 [完成時的設定] 區段。

    ![[完成時的設定] 設為自動套用且不得回應檢閱](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. 如果您想要對遭拒絕的使用者自動移除存取權，請將 [自動將結果套用至資源] 設為 [啟用]。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]。

1. 使用 [若檢閱者未回應] 清單，指定檢閱者在檢閱期間內未檢閱的使用者將受到何種處置。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

    - **無變更** - 使用者的存取權保持不變
    - **移除存取權** - 移除使用者的存取權
    - **核准存取權** - 核准使用者的存取權
    - **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

### <a name="advanced-settings"></a>進階設定

1. 若要指定其他設定，請展開 [進階設定] 區段。

    ![顯示建議、需要核准的原因、郵件通知和提醒的進階設定](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. 將 [顯示建議] 設為 [啟用]，會向檢閱者顯示系統根據使用者的存取資訊所做的建議。

1. 將 [需要核准的原因] 設為 [啟用]，會要求檢閱者提供核准原因。

1. 將 [郵件通知] 設為 [啟用]，會讓 Azure AD 在存取權檢閱開始時傳送電子郵件通知給檢閱者，並在檢閱完成時傳送電子郵件通知給管理員。

1. 將 [提醒] 設為 [啟用]，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。
1. 傳送給審核者之電子郵件的內容會根據審核詳細資料自動產生，例如審核名稱、資源名稱、到期日等。如果您需要一種方式來傳達額外的資訊，例如其他指示或連絡人資訊，您可以在 **評論者電子郵件的其他內容** 中指定這些詳細資料，該電子郵件會包含在傳送給指派審核者的邀請和提醒電子郵件中。 以下反白顯示的區段是將顯示這項資訊的位置。

    ![傳送給審核者之電子郵件的內容醒目顯示](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)