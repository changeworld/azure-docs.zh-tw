---
title: 在 Azure 入口網站中建立影片索引器帳戶
titleSuffix: Azure Media Services
description: 本文將說明如何在 Azure 入口網站中建立影片索引器帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: dc844392d64178cb9000db15af39f923521efc19
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838387"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>建立連線到 Azure 的影片索引器帳戶

建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的 Video Indexer 帳戶會連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶。 您需支付已編製索引的分鐘數，以及媒體帳戶相關費用。 

本文將說明如何建立連結到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。 本主題提供使用自動 (預設) 流程連線到 Azure 的步驟。 也會示範如何手動連線到 Azure (進階)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。

    如果您還沒有 Azure 訂用帳戶，請先註冊 [Azure 免費試用版](https://azure.microsoft.com/free/)。

* Azure Active Directory (AD) 網域。

    如果您沒有 Azure AD 網域，請使用您的 Azure 訂用帳戶建立此網域。 如需詳細資訊，請參閱[管理 Azure Active Directory 中的自訂網域名稱](../../active-directory/users-groups-roles/domains-manage.md)

* Azure AD 網域中具有**應用程式系統管理員**角色的使用者。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

    此使用者應該是具有公司或學校帳戶 (非個人帳戶) 的 Azure AD 使用者，個人帳戶是指 outlook.com、live.com 或 hotmail.com 等。

    ![所有 AAD 使用者](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動流程的其他先決條件

Azure AD 網域的使用者和成員。 當您將影片索引器帳戶連線到 Azure 時，會用到此成員。

此使用者應該是Azure 訂用帳戶中具有「擁有者」角色或同時有「參與者」和「使用者存取系統管理員」角色的成員。 使用者能夠以 2 個角色新增 2 次。 一次使用「參與者」角色，另一次使用「使用者存取系統管理員」角色。

![存取控制](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手動流程的其他先決條件

使用 Azure 入口網站註冊 EventGrid 資源提供者。

在 [Azure 入口網站](https://portal.azure.com/)中，前往 [訂用帳戶] -> [subscription] -> [ResourceProviders]。 

搜尋 **Microsoft.Media** 和 **Microsoft.EventGrid**。 如果不是處於「已註冊」狀態，請按一下 [註冊]。 這需要幾分鐘來完成註冊。

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>連線到 Azure

> [!NOTE]
> 如果您的 Azure 訂用帳戶使用以憑證為基礎的多重要素驗證，請務必在已安裝必要憑證的裝置上執行下列步驟。

1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。

2. 按一下 [**建立新帳戶**] 按鈕：

    ![連線到 Azure](./media/create-account/connect-to-azure.png)

3. 當訂用帳戶清單出現時，選取您想要使用的訂用帳戶。

    ![將影片索引器連線到 Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. 從支援的位置選取 Azure 區域：美國西部 2、歐洲北部或東亞。
5. 在 [Azure 媒體服務帳戶] 下，選擇其中一個選項：

    * 若要建立新的媒體服務帳戶，請選取 [建立新的資源群組]。 提供資源群組的名稱。

        Azure 會在您的訂用帳戶中建立新帳戶，包括新的 Azure 儲存體帳戶。 新媒體服務帳戶的預設初始設定會有一個串流端點和 10 個 S3 保留單元。
    * 若要使用現有的媒體服務帳戶，請選取 [使用現有資源]。 從帳戶清單中，選取您的帳戶。

        媒體服務帳戶具有的區域必須與您的影片索引器帳戶相同。 

        > [!NOTE]
        > 若要達到最少的索引編製持續時間及低輸送量，強烈建議將媒體服務帳戶中的[保留單元](../previous/media-services-scale-media-processing-overview.md )類型與數目調整為 **10 個 S3 保留單元**。 請參閱[使用入口網站來變更保留單元](../previous/media-services-portal-scale-media-processing.md)。

    * 若要手動設定您的連線，請按一下 [切換為手動設定] 連結。

        如需詳細資訊，請參閱後續的[手動連線到 Azure](#connect-to-azure-manually-advanced-option) (進階選項) 一節。
6. 完成之後，請選擇 [連線]。 完成此作業可能需要幾分鐘的時間。 

    連線至 Azure 之後，新的影片索引器帳戶會出現在帳戶清單中：

    ![新帳戶](./media/create-account/new-account.png)

7. 流覽至您的新帳戶

## <a name="connect-to-azure-manually-advanced-option"></a>手動連線到 Azure (進階選項)

如果連線到 Azure 失敗，您可嘗試手動連線來排解疑難問題。

> [!NOTE]
> 強烈建議在相同的區域中有下列三個帳戶：您要與媒體服務帳戶相連的 Video Indexer 帳戶，以及連線到相同媒體服務帳戶的 Azure 儲存體帳戶。

### <a name="create-and-configure-a-media-services-account"></a>建立及設定媒體服務帳戶

1. 使用 [Azure 入口網站](https://portal.azure.com/)來建立 Azure 媒體服務帳戶，如[建立帳戶](../previous/media-services-portal-create-account.md)所示。

    為您的媒體服務帳戶建立儲存體帳戶時，請針對 [帳戶種類] 選取 [ **StorageV2** ]，並針對 [複寫欄位] 選擇 **[異地冗余（GRS）** ]。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > 請務必記下媒體服務資源和帳戶名稱。 您在下一節的步驟中需要用到它。

2. 將您建立的媒體服務帳戶中的[保留單元](../previous/media-services-scale-media-processing-overview.md )類型與數目調整為 **10 個 S3 保留單元**。 請參閱[使用入口網站來變更保留單元](../previous/media-services-portal-scale-media-processing.md)。
3. 您必須先啟動新媒體服務帳戶的預設**串流端點**，才能在 Video Indexer Web 應用程式中播放您的影片。

    在新的媒體服務帳戶中，按一下 [串流端點]。 選取 [串流端點]，然後按 [啟動]。

    ![新的 AMS 帳戶](./media/create-account/create-ams-account2.png)

4. 為了讓 Video Indexer 向媒體服務 API 進行驗證，則必須建立 AD 應用程式。 下列步驟會引導您完成[利用 Azure 入口網站開始使用 Azure AD 驗證](../previous/media-services-portal-get-started-with-aad.md)中所述的 Azure AD 驗證程序：

    1. 在新的媒體服務帳戶中，選取 [API 存取]。
    2. 選取[服務主體驗證方法](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)。
    3. 如[取得用戶端識別碼和用戶端祕密](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret)一節所述，取得用戶端識別碼和用戶端祕密。

        選取 [設定]->[金鑰]、新增 [描述]、按 [儲存] 之後，就會填入金鑰值。

        如果金鑰過期，則帳戶擁有者必須連絡 Video Indexer 支援小組以更新金鑰。

        > [!NOTE]
        > 請務必記下金鑰值和應用程式識別碼。 您在下一節的步驟中需要用到它。

### <a name="connect-manually"></a>手動連線

在 [Video Indexer](https://www.videoindexer.ai/) 頁面的 [將 Video Indexer 連線到 Azure 訂用帳戶] 對話方塊中，選取 [切換為手動設定] 連結。

在對話方塊中，提供下列資訊︰

|設定|描述|
|---|---|
|影片索引器帳戶區域|影片索引器帳戶區域的名稱。 為了達到較佳的效能和較低的成本，強烈建議您指定 Azure 媒體服務資源和 Azure 儲存體帳戶所在區域的名稱。 |
|Azure Active Directory (AAD) 租用戶|Azure AD 租用戶的名稱，例如 "contoso.onmicrosoft.com"。 租用戶資訊可從 Azure 入口網站擷取。 將游標放在右上角登入的使用者名稱上方。 尋找**網域**右邊的名稱。|
|訂用帳戶識別碼|用來建立此連線的 Azure 訂用帳戶。 訂用帳戶識別碼可從 Azure 入口網站擷取。 按一下左側面板中的 [所有服務]，然後搜尋「訂用帳戶」。 選取 [訂用帳戶]，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|媒體服務資源名稱|您在上一節中建立的 Azure 媒體服務帳戶名稱。|
|應用程式識別碼|您在上一節中建立的 Azure AD 應用程式識別碼 (具有所指定媒體服務帳戶的權限)。|
|應用程式金鑰|您在上一節中建立的 Azure AD 應用程式金鑰。 |

## <a name="considerations"></a>考量

以下是 Azure 媒體服務的相關考量：

* 如果您自動連線，您會在 Azure 訂用帳戶中看到新的資源群組、媒體服務帳戶和儲存體帳戶。
* 如果您自動連線，Video Indexer 會將媒體**保留單元**設為 10 個 S3 單元：

    ![媒體服務保留單元](./media/create-account/ams-reserved-units.png)

* 如果您連線到現有的媒體服務帳戶，Video Indexer 則不會變更現有的媒體**保留單元**設定。

   您可能需要根據所規劃的負載，調整媒體保留單元的類型和數量。 請記住，如果您的負載很高，卻沒有足夠的單元或速度，則影片處理會導致逾時錯誤。

* 如果您連線到新的媒體服務帳戶，Video Indexer 會自動啟動其中的預設**串流端點**：

    ![媒體服務串流端點](./media/create-account/ams-streaming-endpoint.png)

    串流端點有相當多的啟動時間。 因此，從您將帳戶連線至 Azure 算起，直到可以在 Video Indexer Web 應用程式中串流處理及觀看您的影片，可能需要幾分鐘的時間。

* 如果您連線到現有的媒體服務帳戶，則 Video Indexer 不會變更預設的串流端點設定。 如有沒有正在執行的**串流端點**，您將無法從此媒體服務帳戶或影片索引器中觀看影片。

## <a name="next-steps"></a>後續步驟

若要以程式設計方式與連線到 Azure 的試用帳戶和/或 Video Indexer 帳戶互動，請遵循以下指示：[使用 API](video-indexer-use-apis.md)。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。


