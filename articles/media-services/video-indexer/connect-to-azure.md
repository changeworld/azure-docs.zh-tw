---
title: 建立連線到 Azure 的影片索引器帳戶
titleSuffix: Azure Media Services
description: 瞭解如何創建連接到 Azure 的視頻索引子帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499927"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>建立連線到 Azure 的影片索引器帳戶

創建視頻索引子帳戶時，您可以選擇免費試用帳戶（在其中獲得一定數量的免費索引分鐘數）或付費選項（不受配額限制）。 通過免費試用，視頻索引子為網站使用者提供長達 600 分鐘的免費索引，為 API 使用者提供長達 2400 分鐘的免費索引。 使用付費選項，您可以創建連接到 Azure 訂閱的視頻索引子帳戶和 Azure 媒體服務帳戶。 您支付已編制索引的分鐘數以及媒體帳戶相關費用。

本文將說明如何建立連結到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。 本主題提供使用自動 (預設) 流程連線到 Azure 的步驟。 也會示範如何手動連線到 Azure (進階)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。

    如果您還沒有 Azure 訂用帳戶，請先註冊 [Azure 免費試用版](https://azure.microsoft.com/free/)。

* Azure 活動目錄 （Azure AD） 域。

    如果您沒有 Azure AD 網域，請使用您的 Azure 訂用帳戶建立此網域。 有關詳細資訊，請參閱在[Azure AD 中管理自訂功能變數名稱](../../active-directory/users-groups-roles/domains-manage.md)

* 具有**應用程式管理員**角色的 Azure AD 域中的使用者。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應是具有工作或學校帳戶的 Azure AD 使用者。 不要使用個人帳戶，如outlook.com、live.com或hotmail.com。

    ![所有 AAD 使用者](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動流程的其他先決條件

* Azure AD 網域的使用者和成員。

    當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是Azure 訂用帳戶中具有「擁有者」**** 角色或同時有「參與者」**** 和「使用者存取系統管理員」**** 角色的成員。 使用者可以添加兩次，具有兩個角色。 一次使用「參與者」角色，另一次使用「使用者存取系統管理員」角色。

    ![存取控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手動流程的其他先決條件

* 使用 Azure 入口網站註冊 EventGrid 資源提供者。

    在 [Azure 入口網站](https://portal.azure.com/)中，前往 [訂用帳戶]**** -> [subscription] -> [ResourceProviders]****。

    搜尋 **Microsoft.Media** 和 **Microsoft.EventGrid**。 如果不是處於「已註冊」狀態，請按一下 [註冊]****。 這需要幾分鐘來完成註冊。

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>連線到 Azure

> [!NOTE]
> 如果 Azure 訂閱使用基於證書的多重要素驗證，則必須在安裝了所需證書的設備上執行以下步驟至關重要。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。

2. 選擇"**創建新帳戶**"按鈕：

    ![創建新的視頻索引子帳戶](./media/create-account/connect-to-azure.png)

3. 當訂用帳戶清單出現時，選取您想要使用的訂用帳戶。

    ![將視頻索引子連接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 從支援的位置選取 Azure 區域：美國西部 2、北歐或東亞。
5. 在 [Azure 媒體服務帳戶]**** 下，選擇其中一個選項：

    * 若要建立新的媒體服務帳戶，請選取 [建立新的資源群組]****。 提供資源群組的名稱。

        Azure 會在您的訂用帳戶中建立新帳戶，包括新的 Azure 儲存體帳戶。 您的新媒體服務帳戶具有預設的初始配置，具有流式處理終結點和 10 S3 保留單位。
    * 若要使用現有的媒體服務帳戶，請選取 [使用現有資源]****。 從帳戶清單中，選取您的帳戶。

        媒體服務帳戶具有的區域必須與您的影片索引器帳戶相同。

        > [!NOTE]
        > 為了最小化索引持續時間和低輸送量，強烈建議將媒體服務帳戶中的[預留單位](../previous/media-services-scale-media-processing-overview.md )的類型和數量調整為**10 S3 預留單位**。 請參閱[使用門戶更改保留單位](../previous/media-services-portal-scale-media-processing.md)。

    * 要手動設定連接，請選擇 **"切換到手動設定**"連結。

        如需詳細資訊，請參閱後續的[手動連線到 Azure](#connect-to-azure-manually-advanced-option) (進階選項) 一節。
6. 完成之後，請選擇 [連線]****。 完成此作業可能需要幾分鐘的時間。

    連線至 Azure 之後，新的影片索引器帳戶會出現在帳戶清單中：

    ![新帳戶](./media/create-account/new-account.png)

7. 流覽到您的新帳戶。

## <a name="connect-to-azure-manually-advanced-option"></a>手動連線到 Azure (進階選項)

如果連線到 Azure 失敗，您可嘗試手動連線來排解疑難問題。

> [!NOTE]
> 強烈建議在同一區域中具有以下三個帳戶：與媒體服務帳戶連接的視頻索引子帳戶以及連接到同一媒體服務帳戶的 Azure 存儲帳戶。

### <a name="create-and-configure-a-media-services-account"></a>建立及設定媒體服務帳戶

1. 使用 [Azure 入口網站](https://portal.azure.com/)來建立 Azure 媒體服務帳戶，如[建立帳戶](../previous/media-services-portal-create-account.md)所示。

    為媒體服務帳戶創建存儲帳戶時，為複製欄位選擇 **"存儲V2"** 科目類型和**異地冗余 （GRS）。**

    ![新的 AMS 帳戶](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 請務必記下媒體服務資源和帳戶名稱。 在下一節中的步驟中，您需要它們。

2. 將[保留單位](../previous/media-services-scale-media-processing-overview.md )的類型和數量調整為您創建的媒體服務帳戶中的**10 S3 預留單位**。 請參閱[使用門戶更改保留單位](../previous/media-services-portal-scale-media-processing.md)。
3. 在視頻索引子 Web 應用中播放視頻之前，必須啟動新媒體服務帳戶的預設**流式處理終結點**。

    在新的媒體服務帳戶中，選擇**流式處理終結點**。 然後選擇流終結點並按"開始"。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account2.png)

4. 對於使用媒體服務 API 進行身份驗證的視頻索引子，需要創建 AD 應用。 下列步驟會引導您完成[利用 Azure 入口網站開始使用 Azure AD 驗證](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 驗證程序：

    1. 在新的媒體服務帳戶中，選取 [API 存取]****。
    2. 選取[服務主體驗證方法](../previous/media-services-portal-get-started-with-aad.md)。
    3. 取得用戶端識別碼和用戶端祕密

        選擇 **"設置**->**鍵**"後，添加 **"說明"，** 按 **"保存**"，然後填充鍵值。

        如果金鑰過期，帳戶擁有者必須聯繫視頻索引子支援以續訂金鑰。

        > [!NOTE]
        > 請務必記下金鑰值和應用程式識別碼。 在下一節中的步驟中，您需要它。

### <a name="connect-manually"></a>手動連線

在 [Video Indexer](https://www.videoindexer.ai/) 頁面的 [將 Video Indexer 連線到 Azure 訂用帳戶]**** 對話方塊中，選取 [切換為手動設定]**** 連結。

在對話方塊中，提供下列資訊︰

|設定|描述|
|---|---|
|影片索引器帳戶區域|影片索引器帳戶區域的名稱。 為了更好的性能和更低的成本，強烈建議指定 Azure 媒體服務資源和 Azure 存儲帳戶所在的區域的名稱。 |
|Azure AD 租用戶|Azure AD 租用戶的名稱，例如 "contoso.onmicrosoft.com"。 租用戶資訊可從 Azure 入口網站擷取。 將游標放在右上角登入的使用者名稱上方。 尋找**網域**右邊的名稱。|
|訂用帳戶識別碼|用來建立此連線的 Azure 訂用帳戶。 訂用帳戶識別碼可從 Azure 入口網站擷取。 選擇左側面板中的所有**服務**，然後搜索"訂閱"。 選取 [訂用帳戶]****，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|媒體服務資源名稱|您在上一節中建立的 Azure 媒體服務帳戶名稱。|
|應用程式識別碼|您在上一節中建立的 Azure AD 應用程式識別碼 (具有所指定媒體服務帳戶的權限)。|
|應用程式金鑰|您在上一節中建立的 Azure AD 應用程式金鑰。 |

## <a name="considerations"></a>考量

以下是 Azure 媒體服務的相關考量：

* 如果您自動連線，您會在 Azure 訂用帳戶中看到新的資源群組、媒體服務帳戶和儲存體帳戶。
* 如果您自動連線，Video Indexer 會將媒體**保留單元**設為 10 個 S3 單元：

    ![媒體服務保留單元](./media/create-account/ams-reserved-units.png)

* 如果連接到現有媒體服務帳戶，視頻索引子不會更改現有媒體**預留單位**配置。

   您可能需要根據計畫負載調整媒體保留單位的類型和數量。 請記住，如果您的負載很高，卻沒有足夠的單元或速度，則影片處理會導致逾時錯誤。

* 如果您連線到新的媒體服務帳戶，Video Indexer 會自動啟動其中的預設**串流端點**：

    ![媒體服務串流端點](./media/create-account/ams-streaming-endpoint.png)

    流式處理終結點具有相當大的啟動時間。 因此，從將帳戶連接到 Azure 到在視頻索引子 Web 應用中資料流和觀看視頻可能需要幾分鐘時間。

* 如果連接到現有媒體服務帳戶，視頻索引子不會更改預設的流式處理終結點配置。 如果沒有正在運行的**流式處理終結點**，則無法觀看來自此媒體服務帳戶或視頻索引子的視頻。

## <a name="next-steps"></a>後續步驟

您可以按照以下說明：[使用 API，](video-indexer-use-apis.md)以程式設計方式與試用帳戶和/或與連接到 Azure 的視頻索引子帳戶進行程式設計交互。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。