---
title: 包含檔案
description: 包含檔案
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476262"
---
## <a name="create-one-or-more-access-reviews"></a>創建一個或多個訪問評論

1. 按一下 **"新建**"以創建新的訪問審閱。

1. 替存取權檢閱命名。 選擇性地提供檢閱的描述。 檢閱者就會看到名稱和描述。

    ![建立存取權檢閱 - 檢閱名稱和描述](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. 設定 [開始日期]****。 根據預設，存取權檢閱會在其建立的相同時間開始，並在一個月後結束。 您可以將存取權檢閱的開始和結束日期變更為未來，並持續任何天數。

    ![開始日期、頻率、持續時間、結束次數、次數和結束日期](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. 要使訪問審核重複進行，將**頻率**設置從**一次**更改為**每週**、**每月**、**每季度**、**每年**或**每半年**。 使用 **"持續時間"** 滑塊或文字方塊定義定期序列的每個審閱將打開多少天供檢閱者輸入。 例如，您可以為每月檢閱設定的最長持續期間為 27 天，以避免重疊的檢閱。

1. 使用 [結束]**** 設定來指定如何結束週期性存取權檢閱系列。 此系列的結束方式有三種：它會持續執行而無限期地啟動檢閱、直到特定日期為止，或是在完成所定義的發生次數之後。 您、其他使用者管理員或其他全域管理員可以在創建後通過在 **"設置"** 中更改日期來停止該系列，以便該日期在該日期結束。

1. 在"**使用者"** 部分中，選擇要查看其成員資格的一個或多個角色。

    ![使用者範圍，以審查角色成員身份](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > 選擇多個角色將創建多個訪問審核。 例如，選擇五個角色將創建五個單獨的訪問審核。

    如果要創建 Azure AD 角色的訪問審閱，則下面將顯示"審閱成員身份清單"的示例。

    ![查看成員身份窗格，列出可以選擇的 Azure AD 角色](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    如果要創建 Azure 資源角色的訪問審閱，下面將顯示"審閱成員身份清單"的示例。

    ![查看成員資格窗格，列出可以選擇的 Azure 資源角色](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. 在 **"檢閱者"** 部分中，選擇一個或多個人員來查看所有使用者。 或者，您可以選擇讓成員檢閱自己的存取權。

    ![所選使用者或成員的檢閱者清單（自我）](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **選定的使用者**- 當您不知道誰需要存取權限時，請使用此選項。 使用此選項，您可以指派資源擁有者或群組管理員完成檢閱。
    - **成員（自我）** - 使用此選項讓使用者查看自己的角色指派。

### <a name="upon-completion-settings"></a>完成時的設定

1. 若要指定檢閱完成後所應執行的動作，請展開 [完成時的設定]**** 區段。

    ![完成後設置為自動應用，應查看不回應](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. 如果您想要對遭拒絕的使用者自動移除存取權，請將 [自動將結果套用至資源]**** 設為 [啟用]****。 如果您想要在檢閱完成時手動套用結果，請將開關設為 [停用]****。

1. 使用 [若檢閱者未回應]**** 清單，指定檢閱者在檢閱期間內未檢閱的使用者將受到何種處置。 此設定並不會影響檢閱者已手動檢閱的使用者。 如果最終的檢閱者決定 [拒絕]，則會移除使用者的存取權。

    - **無變更** - 使用者的存取權保持不變
    - **移除存取權** - 移除使用者的存取權
    - **核准存取權** - 核准使用者的存取權
    - **採納建議** - 採納系統針對應拒絕或核准使用者的持續存取所提出的建議

### <a name="advanced-settings"></a>進階設定

1. 若要指定其他設定，請展開 [進階設定]**** 區段。

    ![顯示建議的高級設置，要求在審批、郵件通知和提醒時說明原因](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. 將 [顯示建議]**** 設為 [啟用]****，會向檢閱者顯示系統根據使用者的存取資訊所做的建議。

1. 將 [需要核准的原因]**** 設為 [啟用]****，會要求檢閱者提供核准原因。

1. 將 [郵件通知]**** 設為 [啟用]****，會讓 Azure AD 在存取權檢閱開始時傳送電子郵件通知給檢閱者，並在檢閱完成時傳送電子郵件通知給管理員。

1. 將 [提醒]**** 設為 [啟用]****，會讓 Azure AD 對尚未完成其檢閱的檢閱者傳送存取權檢閱正在進行中的提醒。
