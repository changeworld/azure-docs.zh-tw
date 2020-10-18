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
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 7e97dc6ad4c6591a470e19050d79230f9ea7fd43
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164785"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>建立連線到 Azure 的影片索引器帳戶

建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的影片索引子帳戶會連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶。 您需支付已編製索引的分鐘數，以及媒體帳戶相關費用。

本文將說明如何建立連結到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。 本主題提供使用自動 (預設) 流程連線到 Azure 的步驟。 也會示範如何手動連線到 Azure (進階)。

如果您要從 *試用版* 移至 *付費* 影片索引子帳戶，您可以選擇將所有的影片和模型自訂複製到新的帳戶，如 [從試用帳戶匯入內容](#import-your-content-from-the-trial-account) 一節中所述。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。

    如果您還沒有 Azure 訂用帳戶，請先註冊 [Azure 免費試用版](https://azure.microsoft.com/free/)。

* Azure Active Directory (Azure AD) 網域。

    如果您沒有 Azure AD 網域，請使用您的 Azure 訂用帳戶建立此網域。 如需詳細資訊，請參閱 [在您的 Azure AD 中管理自訂功能變數名稱](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 網域中具有 **應用程式系統管理員** 角色的使用者。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是具有工作或學校帳戶的 Azure AD 使用者。 請勿使用個人帳戶，例如 outlook.com、live.com 或 hotmail.com。

    ![所有 AAD 使用者](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動流程的其他先決條件

* Azure AD 網域的使用者和成員。

    當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是Azure 訂用帳戶中具有「擁有者」**** 角色或同時有「參與者」**** 和「使用者存取系統管理員」**** 角色的成員。 使用者可以使用兩個角色新增兩次。 一次使用「參與者」角色，另一次使用「使用者存取系統管理員」角色。 如需詳細資訊，請參閱 [查看使用者對 Azure 資源的存取權](https://docs.microsoft.com/azure/role-based-access-control/check-access)。

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

2. 選取 [ **建立新帳戶** ] 按鈕：

    ![建立新的影片索引子帳戶](./media/create-account/connect-to-azure.png)

3. 當訂用帳戶清單出現時，選取您想要使用的訂用帳戶。

    ![將影片索引子連線到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 從支援的位置選取 Azure 區域：美國西部 2、北歐或東亞。
5. 在 [Azure 媒體服務帳戶]**** 下，選擇其中一個選項：

    * 若要建立新的媒體服務帳戶，請選取 [建立新的資源群組]****。 提供資源群組的名稱。

        Azure 會在您的訂用帳戶中建立新帳戶，包括新的 Azure 儲存體帳戶。 新的媒體服務帳戶具有具有串流端點和10個 S3 保留單元的預設初始設定。
    * 若要使用現有的媒體服務帳戶，請選取 [使用現有資源]****。 從帳戶清單中，選取您的帳戶。

        媒體服務帳戶具有的區域必須與您的影片索引器帳戶相同。

        > [!NOTE]
        > 為了將索引持續時間和低輸送量降至最低，強烈建議您將媒體服務帳戶中的 [保留單元](../previous/media-services-scale-media-processing-overview.md ) 類型和數目調整為 **10 個 S3 保留單元**。 請參閱 [使用入口網站變更保留單位](../previous/media-services-portal-scale-media-processing.md)。

    * 若要手動設定您的連線，請選取 [ **切換至手動** 設定] 連結。

        如需詳細資訊，請參閱後續的[手動連線到 Azure](#connect-to-azure-manually-advanced-option) (進階選項) 一節。
6. 完成之後，請選擇 [連線]****。 完成此作業可能需要幾分鐘的時間。

    連線至 Azure 之後，新的影片索引器帳戶會出現在帳戶清單中：

    ![新帳戶](./media/create-account/new-account.png)

7. 流覽至您的新帳戶。

## <a name="connect-to-azure-manually-advanced-option"></a>手動連線到 Azure (進階選項)

如果連線到 Azure 失敗，您可嘗試手動連線來排解疑難問題。

> [!NOTE]
> 強烈建議在相同區域中具有下列三個帳戶：您要使用媒體服務帳戶連接的影片索引子帳戶，以及連線至相同媒體服務帳戶的 Azure 儲存體帳戶。

### <a name="create-and-configure-a-media-services-account"></a>建立及設定媒體服務帳戶

1. 使用 [Azure 入口網站](https://portal.azure.com/)來建立 Azure 媒體服務帳戶，如[建立帳戶](../previous/media-services-portal-create-account.md)所示。

    為您的媒體服務帳戶建立儲存體帳戶時，請針對 [複寫] 欄位選取 [ **StorageV2** ] 做為 [帳戶類型] 和 [ **異地複寫 (GRS) ** 。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 請務必記下媒體服務資源和帳戶名稱。 在下一節中，您將需要這些步驟。

2. 在您建立的媒體服務帳戶中，將 [保留單元](../previous/media-services-scale-media-processing-overview.md ) 的類型和數目調整為 **10 個 S3 保留單元** 。 請參閱 [使用入口網站變更保留單位](../previous/media-services-portal-scale-media-processing.md)。
3. 在影片索引子 web 應用程式中播放影片之前，您必須啟動新媒體服務帳戶的預設 **串流端點** 。

    在新的媒體服務帳戶中，選取 [ **串流端點**]。 然後選取串流端點，再按 [開始]。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account2.png)

4. 若要讓影片索引子使用媒體服務 API 進行驗證，則必須建立 AD 應用程式。 下列步驟會引導您完成[利用 Azure 入口網站開始使用 Azure AD 驗證](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 驗證程序：

    1. 在新的媒體服務帳戶中，選取 [API 存取]****。
    2. 選取[服務主體驗證方法](../previous/media-services-portal-get-started-with-aad.md)。
    3. 取得用戶端識別碼和用戶端祕密

        選取 [**設定** -> **金鑰**]、[**新增描述**] 之後，請按下 [**儲存**]，然後填入金鑰值。

        如果金鑰已過期，帳戶擁有者將必須聯絡影片索引子支援以更新金鑰。

        > [!NOTE]
        > 請務必記下金鑰值和應用程式識別碼。 您將在下一節的步驟中需要它。

### <a name="connect-manually"></a>手動連線

在 [Video Indexer](https://www.videoindexer.ai/) 頁面的 [將 Video Indexer 連線到 Azure 訂用帳戶]**** 對話方塊中，選取 [切換為手動設定]**** 連結。

在對話方塊中，提供下列資訊︰

|設定|描述|
|---|---|
|影片索引器帳戶區域|影片索引器帳戶區域的名稱。 為了獲得更佳的效能和較低的成本，強烈建議您指定 Azure 媒體服務資源和 Azure 儲存體帳戶所在區域的名稱。 |
|Azure AD 租用戶|Azure AD 租用戶的名稱，例如 "contoso.onmicrosoft.com"。 租用戶資訊可從 Azure 入口網站擷取。 將游標放在右上角登入的使用者名稱上方。 尋找**網域**右邊的名稱。|
|訂用帳戶識別碼|用來建立此連線的 Azure 訂用帳戶。 訂用帳戶識別碼可從 Azure 入口網站擷取。 在左面板中選取 [ **所有服務** ]，並搜尋「訂用帳戶」。 選取 [訂用帳戶]****，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|媒體服務資源名稱|您在上一節中建立的 Azure 媒體服務帳戶名稱。|
|應用程式識別碼|您在上一節中建立的 Azure AD 應用程式識別碼 (具有所指定媒體服務帳戶的權限)。|
|應用程式金鑰|您在上一節中建立的 Azure AD 應用程式金鑰。 |

## <a name="import-your-content-from-the-trial-account"></a>從 *試用* 帳戶匯入內容

[建立新帳戶](#connect-to-azure)時，您可以選擇將您的內容從*試用*帳戶匯入至新的帳戶。 如果您核取 [**在 Azure 訂用帳戶上建立新帳戶**] 對話方塊中的 [匯*入*] 選項，將會從*試用*帳戶將所有媒體和內容模型自訂複製到新的帳戶。

匯入內容的功能對上述的自動化和手動方法都有效。

> [!NOTE]
> 每個帳戶只能匯入一次內容。

## <a name="considerations"></a>考量

以下是 Azure 媒體服務的相關考量：

* 如果您自動連線，您會在 Azure 訂用帳戶中看到新的資源群組、媒體服務帳戶和儲存體帳戶。
* 如果您自動連線，Video Indexer 會將媒體**保留單元**設為 10 個 S3 單元：

    ![媒體服務保留單元](./media/create-account/ams-reserved-units.png)

* 如果您連接到現有的媒體服務帳戶，影片索引子不會變更現有的媒體 **保留單元** 設定。

   您可能需要根據所規劃的負載，調整媒體保留單元的類型和數目。 請記住，如果您的負載很高，卻沒有足夠的單元或速度，則影片處理會導致逾時錯誤。

* 如果您連線到新的媒體服務帳戶，Video Indexer 會自動啟動其中的預設**串流端點**：

    ![媒體服務串流端點](./media/create-account/ams-streaming-endpoint.png)

    串流端點具有相當大的啟動時間。 因此，在您將帳戶連線至 Azure 時，可能需要幾分鐘的時間，才能在影片索引子 web 應用程式中串流和監看您的影片。

* 如果您連接到現有的媒體服務帳戶，影片索引子不會變更預設的串流端點設定。 如果沒有執行中的 **串流端點**，您就無法從此媒體服務帳戶或影片索引子中觀看影片。

## <a name="next-steps"></a>後續步驟

您可以遵循中的指示，以程式設計方式與連線到 Azure 的試用帳戶和/或您的影片索引子帳戶互動： [使用 api](video-indexer-use-apis.md)。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。
