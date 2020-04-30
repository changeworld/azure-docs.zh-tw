---
title: 建立連線到 Azure 的影片索引器帳戶
titleSuffix: Azure Media Services
description: 瞭解如何建立連線到 Azure 的影片索引子帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/22/2020
ms.author: juliako
ms.openlocfilehash: 89360f062f0198a6b0c857395b4956caa1109e82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097152"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>建立連線到 Azure 的影片索引器帳戶

建立影片索引子帳戶時，您可以選擇免費試用帳戶（您可取得特定的免費編制索引分鐘數）或付費選項（您不會受限於配額）。 使用免費試用版，影片索引子最多可為網站使用者提供600分鐘的免費編制索引，以及最多2400分鐘的 API 使用者免費編制索引。 使用付費選項時，您會建立連接到您的 Azure 訂用帳戶的影片索引子帳戶，以及 Azure 媒體服務帳戶。 您需支付已編制索引的分鐘數，以及媒體帳戶相關費用。

本文將說明如何建立連結到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。 本主題提供使用自動 (預設) 流程連線到 Azure 的步驟。 也會示範如何手動連線到 Azure (進階)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。

    如果您還沒有 Azure 訂用帳戶，請先註冊 [Azure 免費試用版](https://azure.microsoft.com/free/)。

* Azure Active Directory （Azure AD）網域。

    如果您沒有 Azure AD 網域，請使用您的 Azure 訂用帳戶建立此網域。 如需詳細資訊，請參閱[管理您 Azure AD 中的自訂功能變數名稱](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 網域中具有**應用程式系統管理員**角色的使用者。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是具有工作或學校帳戶的 Azure AD 使用者。 請勿使用個人帳戶，例如 outlook.com、live.com 或 hotmail.com。

    ![所有 AAD 使用者](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動流程的其他先決條件

* Azure AD 網域的使用者和成員。

    當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是Azure 訂用帳戶中具有「擁有者」**** 角色或同時有「參與者」**** 和「使用者存取系統管理員」**** 角色的成員。 使用者可以使用兩個角色新增兩次。 一次使用「參與者」角色，另一次使用「使用者存取系統管理員」角色。

    ![存取控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手動流程的其他先決條件

* 使用 Azure 入口網站註冊 EventGrid 資源提供者。

    在 [Azure 入口網站](https://portal.azure.com/)中，前往 [訂用帳戶]**** -> [subscription] -> [ResourceProviders]****。

    搜尋 **Microsoft.Media** 和 **Microsoft.EventGrid**。 如果不是處於「已註冊」狀態，請按一下 [註冊]****。 這需要幾分鐘來完成註冊。

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>連線到 Azure

> [!NOTE]
> 如果您的 Azure 訂用帳戶使用以憑證為基礎的多重要素驗證，請務必在已安裝必要憑證的裝置上執行下列步驟。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。

2. 選取 [**建立新帳戶**] 按鈕：

    ![建立新的影片索引子帳戶](./media/create-account/connect-to-azure.png)

3. 當訂用帳戶清單出現時，選取您想要使用的訂用帳戶。

    ![將影片索引子連接到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 從支援的位置選取 Azure 區域：美國西部 2、北歐或東亞。
5. 在 [Azure 媒體服務帳戶]**** 下，選擇其中一個選項：

    * 若要建立新的媒體服務帳戶，請選取 [建立新的資源群組]****。 提供資源群組的名稱。

        Azure 會在您的訂用帳戶中建立新帳戶，包括新的 Azure 儲存體帳戶。 新的媒體服務帳戶具有具有串流端點和10個 S3 保留單元的預設初始設定。
    * 若要使用現有的媒體服務帳戶，請選取 [使用現有資源]****。 從帳戶清單中，選取您的帳戶。

        媒體服務帳戶具有的區域必須與您的影片索引器帳戶相同。

        > [!NOTE]
        > 若要將索引編制持續時間和低輸送量降到最低，強烈建議將您媒體服務帳戶中的[保留單元](../previous/media-services-scale-media-processing-overview.md )類型和數目調整為**10 個 S3 保留單元**。 請參閱[使用入口網站來變更保留單元](../previous/media-services-portal-scale-media-processing.md)。

    * 若要手動設定您的連線，請選取 [**切換為手動**設定] 連結。

        如需詳細資訊，請參閱後續的[手動連線到 Azure](#connect-to-azure-manually-advanced-option) (進階選項) 一節。
6. 完成之後，請選擇 [連線]****。 完成此作業可能需要幾分鐘的時間。

    連線至 Azure 之後，新的影片索引器帳戶會出現在帳戶清單中：

    ![新帳戶](./media/create-account/new-account.png)

7. 流覽至您的新帳戶。

## <a name="connect-to-azure-manually-advanced-option"></a>手動連線到 Azure (進階選項)

如果連線到 Azure 失敗，您可嘗試手動連線來排解疑難問題。

> [!NOTE]
> 強烈建議您在相同區域中有下列三個帳戶：您使用媒體服務帳戶連線的影片索引子帳戶，以及連接到相同媒體服務帳戶的 Azure 儲存體帳戶。

### <a name="create-and-configure-a-media-services-account"></a>建立及設定媒體服務帳戶

1. 使用 [Azure 入口網站](https://portal.azure.com/)來建立 Azure 媒體服務帳戶，如[建立帳戶](../previous/media-services-portal-create-account.md)所示。

    為您的媒體服務帳戶建立儲存體帳戶時，請針對 [帳戶種類] 選取 [ **StorageV2** ]，並針對 [複寫欄位] 選擇 **[異地冗余（GRS）** ]。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 請務必記下媒體服務資源和帳戶名稱。 在下一節中，您將需要這些步驟。

2. 在您建立的媒體服務帳戶中，將[保留單元](../previous/media-services-scale-media-processing-overview.md )的類型和數目調整為**10 個 S3 保留單元**。 請參閱[使用入口網站來變更保留單元](../previous/media-services-portal-scale-media-processing.md)。
3. 您必須先啟動新媒體服務帳戶的預設**串流端點**，才可以在影片索引子 web 應用程式中播放影片。

    在新的媒體服務帳戶中，選取 [**串流端點**]。 然後選取串流端點，然後按 [啟動]。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account2.png)

4. 若要使用媒體服務 API 來驗證影片索引子，必須建立 AD 應用程式。 下列步驟會引導您完成[利用 Azure 入口網站開始使用 Azure AD 驗證](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 驗證程序：

    1. 在新的媒體服務帳戶中，選取 [API 存取]****。
    2. 選取[服務主體驗證方法](../previous/media-services-portal-get-started-with-aad.md)。
    3. 取得用戶端識別碼和用戶端祕密

        在您選取 [**設定**->] [**金鑰**] 之後，請新增 [**描述**]，然後按 [**儲存**] 並填入金鑰值。

        如果金鑰過期，帳戶擁有者就必須聯絡影片索引子支援以更新金鑰。

        > [!NOTE]
        > 請務必記下金鑰值和應用程式識別碼。 您在下一節的步驟中需要用到它。

### <a name="connect-manually"></a>手動連線

在 [Video Indexer](https://www.videoindexer.ai/) 頁面的 [將 Video Indexer 連線到 Azure 訂用帳戶]**** 對話方塊中，選取 [切換為手動設定]**** 連結。

在對話方塊中，提供下列資訊︰

|設定|描述|
|---|---|
|影片索引器帳戶區域|影片索引器帳戶區域的名稱。 為了獲得更好的效能和較低的成本，強烈建議您指定 Azure 媒體服務資源和 Azure 儲存體帳戶所在區域的名稱。 |
|Azure AD 租用戶|Azure AD 租用戶的名稱，例如 "contoso.onmicrosoft.com"。 租用戶資訊可從 Azure 入口網站擷取。 將游標放在右上角登入的使用者名稱上方。 尋找**網域**右邊的名稱。|
|訂用帳戶識別碼|用來建立此連線的 Azure 訂用帳戶。 訂用帳戶識別碼可從 Azure 入口網站擷取。 選取左面板中的 [**所有服務**]，然後搜尋 [訂用帳戶]。 選取 [訂用帳戶]****，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|媒體服務資源名稱|您在上一節中建立的 Azure 媒體服務帳戶名稱。|
|應用程式識別碼|您在上一節中建立的 Azure AD 應用程式識別碼 (具有所指定媒體服務帳戶的權限)。|
|應用程式金鑰|您在上一節中建立的 Azure AD 應用程式金鑰。 |

## <a name="import-your-content-from-the-trial-account"></a>從*試用*帳戶匯入您的內容

[建立新帳戶](#connect-to-azure)時，您可以選擇將您的內容從*試用*帳戶匯入到新帳戶。 如果您核取 [在**Azure 訂用帳戶上建立新帳戶**] 對話方塊中的 [匯*入*] 選項，將會從*試用*帳戶將所有媒體和內容模型自訂複製到新帳戶。

匯入內容的功能對上述的自動化和手動方法有效。

> [!NOTE]
> 內容只能從每個帳戶匯入一次。

## <a name="considerations"></a>考量

以下是 Azure 媒體服務的相關考量：

* 如果您自動連線，您會在 Azure 訂用帳戶中看到新的資源群組、媒體服務帳戶和儲存體帳戶。
* 如果您自動連線，Video Indexer 會將媒體**保留單元**設為 10 個 S3 單元：

    ![媒體服務保留單元](./media/create-account/ams-reserved-units.png)

* 如果您連接到現有的媒體服務帳戶，影片索引子不會變更現有的媒體**保留單元**設定。

   您可能需要根據所規劃的負載，調整媒體保留單元的類型和數目。 請記住，如果您的負載很高，卻沒有足夠的單元或速度，則影片處理會導致逾時錯誤。

* 如果您連線到新的媒體服務帳戶，Video Indexer 會自動啟動其中的預設**串流端點**：

    ![媒體服務串流端點](./media/create-account/ams-streaming-endpoint.png)

    串流端點的啟動時間很長。 因此，您可能需要幾分鐘的時間，從您將帳戶連線至 Azure，直到您的影片可以在影片索引子 web 應用程式中進行串流和監看為止。

* 如果您連接到現有的媒體服務帳戶，影片索引子不會變更預設的串流端點設定。 如果沒有執行中的**串流端點**，您就無法在此媒體服務帳戶或影片索引子中觀看影片。

## <a name="next-steps"></a>後續步驟

您可以遵循：[使用 api](video-indexer-use-apis.md)中的指示，以程式設計方式與您的試用帳戶和/或已連線到 Azure 的影片索引子帳戶進行互動。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。