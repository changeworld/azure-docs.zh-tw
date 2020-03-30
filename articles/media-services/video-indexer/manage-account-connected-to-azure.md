---
title: 管理影片索引器帳戶
titleSuffix: Azure Media Services
description: 瞭解如何管理連接到 Azure 的視頻索引子帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499662"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>管理連線到 Azure 的影片索引器帳戶

本文演示如何管理連接到 Azure 訂閱和 Azure 媒體服務帳戶的視頻索引子帳戶。

> [!NOTE]
> 您必須是影片索引器帳戶的擁有者，才能進行本主題中討論的帳戶設定調整。

## <a name="prerequisites"></a>Prerequisites

將您的影片索引器帳戶連線到 Azure，如[連線到 Azure](connect-to-azure.md) 中所述。

請務必遵循文章中的[必要條件](connect-to-azure.md#prerequisites)並檢閱[考量](connect-to-azure.md#considerations)。

## <a name="examine-account-settings"></a>檢查帳戶設定

本節將探討您影片索引器帳戶的設定。

若要檢視設定：

1. 按一下右上角的使用者圖示，然後選擇 **"設置**"。

    ![視頻索引子中的設置](./media/manage-account-connected-to-azure/select-settings.png)

2. 在 [設定]**** 頁面上，選取 [帳戶]**** 索引標籤。

如果視頻索引子帳戶已連接到 Azure，您將看到以下事項：

* 基礎 Azure 媒體服務帳戶的名稱。
* 執行中和排入佇列的編製索引作業數目。
* 已分配保留單位的數量和類型。

如果您的帳戶需要一些調整，您將在 **"設置"** 頁上看到有關帳戶配置的相關錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 如需詳細資訊，請參閱之後的[錯誤和警告](#errors-and-warnings)一節。

## <a name="repair-the-connection-to-azure"></a>修復與 Azure 的連接

在"**更新到**[視頻索引子](https://www.videoindexer.ai/)"頁面的 Azure 媒體服務對話方塊中，系統會要求您提供以下設置的值：

|設定|描述|
|---|---|
|Azure 訂用帳戶識別碼|訂用帳戶識別碼可從 Azure 入口網站擷取。 按一下左側面板中的所有**服務**並搜索"訂閱"。 選取 [訂用帳戶]****，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|應用程式識別碼|為此視頻索引子帳戶創建的 Azure AD 應用程式 ID（具有指定媒體服務帳戶的許可權）。 <br/><br/>要獲取應用 ID，可以導航到 Azure 門戶。 在媒體服務帳戶下，選擇您的帳戶並轉到**API 訪問**。 選擇**使用服務主體** -> **Azure AD 應用**連接到媒體服務 API 。 複製相關參數。|
|應用程式金鑰|與上面指定的媒體服務帳戶關聯的 Azure AD 應用程式金鑰。 <br/><br/>要獲取應用金鑰，可以導航到 Azure 門戶。 在媒體服務帳戶下，選擇您的帳戶並轉到**API 訪問**。  -> 選擇選擇**使用服務主體連接到媒體服務 API****管理應用程式** -> **證書&機密**。 複製相關參數。|

## <a name="autoscale-reserved-units"></a>自動縮放預留單位

"**設置"** 頁使您能夠設置媒體保留單位 （RU） 的自動縮放。 如果選項為 [開啟]****，您可以配置 RU 的數目上限，並確定該影片索引器可自動停止/啟動 RU。 使用此選項，您不會為閒置時間支付額外的費用，也不會等待索引作業在索引負載較高時完成很長時間。

自動縮放不會低於 1 RU 或超過媒體服務帳戶的預設限制。 要增加限制，請創建服務請求。 如需配額和限制以及如何開啟支援票證的相關資訊，請參閱 [配額和限制](../../media-services/previous/media-services-quotas-and-limitations.md)。

![自動縮放保留單位 視頻索引子](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>錯誤和警告

如果您的帳戶需要做一些調整，您會在 [設定]**** 頁面上看到與帳戶設定相關的錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 本節提供有關錯誤和警告訊息的詳細資料。

* EventGrid

    您必須使用 Azure 入口網站註冊 EventGrid 資源提供者。 在 [Azure 入口網站](https://portal.azure.com/) 中，前往 [訂用帳戶]**** > [訂用帳戶] > [資源提供者]**** > [Microsoft.EventGrid]****。 如果未處於 **"已註冊"** 狀態，請選擇"**註冊**"。 這需要幾分鐘來完成註冊。

* 串流端點

    請確定基礎媒體服務帳戶有處於已啟動狀態的預設**串流端點**。 否則，您無法在此媒體服務帳戶或視頻索引子中觀看視頻。

* 媒體保留單位

    您必須在媒體服務資源上配置媒體保留單元，才能編製影片索引。 為了達到最佳的索引編製效能，建議您至少配置 10 個 S3 保留單元。 如需定價資訊，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。

## <a name="next-steps"></a>後續步驟

您可以按照以下說明：[使用 API，](video-indexer-use-apis.md)以程式設計方式與連接到 Azure 的試用帳戶或視頻索引子帳戶進行交互。

使用連接到 Azure 時使用的相同 Azure AD 使用者。
