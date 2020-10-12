---
title: 管理影片索引器帳戶
titleSuffix: Azure Media Services
description: 瞭解如何管理連線到 Azure 的影片索引子帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79499662"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>管理連線到 Azure 的影片索引器帳戶

本文示範如何管理連線到 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引子帳戶。

> [!NOTE]
> 您必須是影片索引器帳戶的擁有者，才能進行本主題中討論的帳戶設定調整。

## <a name="prerequisites"></a>必要條件

將您的影片索引器帳戶連線到 Azure，如[連線到 Azure](connect-to-azure.md) 中所述。

請務必遵循文章中的[必要條件](connect-to-azure.md#prerequisites)並檢閱[考量](connect-to-azure.md#considerations)。

## <a name="examine-account-settings"></a>檢查帳戶設定

本節將探討您影片索引器帳戶的設定。

若要檢視設定：

1. 按一下右上角的使用者圖示，然後選取 [ **設定**]。

    ![影片索引子中的設定](./media/manage-account-connected-to-azure/select-settings.png)

2. 在 [設定]**** 頁面上，選取 [帳戶]**** 索引標籤。

如果您的影片索引子帳戶已連線到 Azure，您會看到下列專案：

* 基礎 Azure 媒體服務帳戶的名稱。
* 執行中和排入佇列的編製索引作業數目。
* 配置的保留單元數目和類型。

如果您的帳戶需要進行一些調整，您會在 [設定] 頁面上看到有關帳戶 **設定** 的相關錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 如需詳細資訊，請參閱之後的[錯誤和警告](#errors-and-warnings)一節。

## <a name="repair-the-connection-to-azure"></a>修復與 Azure 的連線

在 [[影片索引子](https://www.videoindexer.ai/)] 頁面的 [ **Azure 媒體服務的更新連接**] 對話方塊中，系統會要求您提供下列設定的值：

|設定|描述|
|---|---|
|Azure 訂用帳戶識別碼|訂用帳戶識別碼可從 Azure 入口網站擷取。 在左面板中按一下 [ **所有服務** ]，並搜尋「訂用帳戶」。 選取 [訂用帳戶]****，並從訂用帳戶清單中選擇需要的識別碼。|
|Azure 媒體服務資源群組名稱|您在其中建立媒體服務帳戶的資源群組名稱。|
|應用程式識別碼|Azure AD 的應用程式識別碼 (具有您為此影片索引子帳戶所建立之指定媒體服務帳戶) 的許可權。 <br/><br/>若要取得應用程式識別碼，請流覽至 Azure 入口網站。 在媒體服務帳戶下，選擇您的帳戶並移至 [ **API 存取**]。 選取 **[使用服務主體連線到媒體服務 API]**  ->  **Azure AD App**。 複製相關的參數。|
|應用程式金鑰|與您先前指定的媒體服務帳戶相關聯的 Azure AD 應用程式金鑰。 <br/><br/>若要取得應用程式金鑰，請流覽至 Azure 入口網站。 在媒體服務帳戶下，選擇您的帳戶並移至 [ **API 存取**]。 選取 **[使用服務主體連線到媒體服務 API]**  ->  & 秘密**管理應用程式**  ->  **憑證**。 複製相關的參數。|

## <a name="autoscale-reserved-units"></a>自動調整保留單元

[ **設定** ] 頁面可讓您設定媒體保留單元的自動調整 (RU) 。 如果選項為 [開啟]****，您可以配置 RU 的數目上限，並確定該影片索引器可自動停止/啟動 RU。 使用這個選項時，您不需支付閒置時間的額外費用，但也不會等到索引編制負載很高時，等候編制索引作業完成一段很長的時間。

自動調整規模不會低於 1 RU 或超過媒體服務帳戶的預設限制。 若要增加限制，請建立服務要求。 如需配額和限制以及如何開啟支援票證的相關資訊，請參閱 [配額和限制](../../media-services/previous/media-services-quotas-and-limitations.md)。

![自動調整保留單元的影片索引子](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>錯誤和警告

如果您的帳戶需要做一些調整，您會在 [設定]**** 頁面上看到與帳戶設定相關的錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 本節提供有關錯誤和警告訊息的詳細資料。

* EventGrid

    您必須使用 Azure 入口網站註冊 EventGrid 資源提供者。 在 [Azure 入口網站](https://portal.azure.com/) 中，前往 [訂用帳戶]**** > [訂用帳戶] > [資源提供者]**** > [Microsoft.EventGrid]****。 如果未處於 **已註冊** 狀態，請選取 [ **註冊**]。 這需要幾分鐘來完成註冊。

* 串流端點

    請確定基礎媒體服務帳戶有處於已啟動狀態的預設**串流端點**。 否則，您無法從此媒體服務帳戶或影片索引子中觀看影片。

* 媒體保留單元

    您必須在媒體服務資源上配置媒體保留單元，才能編製影片索引。 為了達到最佳的索引編製效能，建議您至少配置 10 個 S3 保留單元。 如需定價資訊，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。

## <a name="next-steps"></a>後續步驟

您可以遵循： [使用 api](video-indexer-use-apis.md)中的指示，以程式設計方式與連線到 Azure 的試用帳戶或影片索引子帳戶互動。

使用您連接到 Azure 時所使用的相同 Azure AD 使用者。
