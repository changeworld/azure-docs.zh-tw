---
title: 如何使用 Azure 事件方格將事件從 Auth0 傳送至 Azure
description: 如何使用 Azure 事件方格結束從 Auth0 到 Azure 服務的事件。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 950b17611ba4932cc986d1b6488b904f29fdf04b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690314"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>整合 Azure 事件方格與 Auth0

本文說明如何藉由建立事件方格合作夥伴主題來連接您的 Auth0 與 Azure 帳戶。

如需 Auth0 支援之事件的完整清單，請參閱 [Auth0 事件類型代碼](https://auth0.com/docs/logs/references/log-event-type-codes)

## <a name="send-events-from-auth0-to-azure-event-grid"></a>將事件從 Auth0 傳送至 Azure 事件方格
若要將 Auth0 事件傳送至 Azure：

1. 啟用事件格線資源提供者
1. 在 Auth0 儀表板中設定 Auth0 合作夥伴主題
1. 在 Azure 中啟用合作夥伴主題
1. 訂閱來自 Auth0 的事件

如需這些概念的詳細資訊，請參閱事件方格[概念](concepts.md)。

### <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者
若您先前未曾使用過事件方格，您將必須註冊事件方格資源提供者。 如果您先前曾使用過事件方格，請跳到下一節。

在 Azure 入口網站中：
1. 選取左側功能表上的 [訂用帳戶]
1. 選取您要用於事件方格的訂用帳戶
1. 在左側功能表的 [設定] 底下，選取 [資源提供者]
1. 尋找 Microsoft.EventGrid
1. 選取 [註冊]
1. 重新整理以確定狀態變更為 [已註冊]

### <a name="set-up-an-auth0-partner-topic"></a>設定 Auth0 合作夥伴主題
在整合的過程中，您必須將 Auth0 設定為事件來源 (此步驟須在 [Auth0 儀表板](https://manage.auth0.com/)中執行)。

1. 登入 [Auth0 儀表板](https://manage.auth0.com/)。
1. 瀏覽至 [記錄] > [串流]。
1. 按一下 [+ 建立串流]。
1. 選取 [Azure 事件方格]，然後為新的串流輸入唯一的名稱。
1. 提供您的 Azure 訂用帳戶識別碼、Azure 區域和資源群組名稱，以建立事件來源。 
1. 按一下 [儲存]。

### <a name="activate-your-auth0-partner-topic-in-azure"></a>在 Azure 中啟用您的 Auth0 合作夥伴主題
在 Azure 中啟用 Auth0 主題，可讓事件從 Auth0 流向 Azure。

1. 登入 Azure 管理入口網站。
1. 在頂端搜尋 `Partner Topics`，然後按一下服務底下的 `Event Grid Partner Topics`。
1. 按一下與您在 Auth0 儀表板中建立的串流相符的主題。
1. 確認 `Source` 欄位符合您的 Auth0 帳戶。
1. 按一下 [啟用]。

### <a name="subscribe-to-auth0-events"></a>訂閱 Auth0 事件

#### <a name="create-an-event-handler"></a>建立事件處理常式
若要測試您的合作夥伴主題，您將需要事件處理常式。 請移至您的 Azure 訂用帳戶，並啟動可作為[事件處理常式](event-handlers.md)的服務，例如 [Azure 函式](custom-event-to-function.md)。

#### <a name="subscribe-to-your-auth0-partner-topic"></a>訂閱您的 Auth0 合作夥伴主題
訂閱 Auth0 合作夥伴主題，可向事件方格指出您想要將 Auth0 事件送往何處。

1. 在 Auth0 整合的 [合作夥伴主題] 刀鋒視窗上，選取頂端的 [+ 事件訂閱]。
1. 在 [建立事件訂閱] 頁面上：
    1. 輸入事件訂閱的名稱。
    1. 選取您已建立的 Azure 服務或 WebHook 作為端點類型。
    1. 依照特定服務的指示操作。
    1. 按一下 [建立]。

## <a name="testing"></a>測試
您的 Auth0 合作夥伴主題與 Azure 的整合應已可供使用。

### <a name="verify-the-integration"></a>確認整合
若要確認整合是否如預期運作：

1. 登入 Auth0 儀表板。
1. 瀏覽至 [記錄] > [串流]。
1. 按一下您的事件方格串流。
1. 在串流上，按一下 [健康情況] 索引標籤。串流應處於作用中狀態，且只要未出現任何錯誤，即表示串流正在運作中。

嘗試[叫用任何會觸發事件發佈的 Auth0 動作](https://auth0.com/docs/logs/references/log-event-type-codes)，以查看事件流程。

## <a name="delivery-attempts-and-retries"></a>傳遞嘗試和重試
Auth0 事件會透過串流機制傳遞至 Azure。 每個事件在 Auth0 中受到觸發時即會傳送。 如果事件方格無法接收事件，Auth0 將重試最多三次，以傳遞事件。 若仍無法傳遞，Auth0 會在其系統中記錄傳遞失敗。

## <a name="next-steps"></a>後續步驟

- [Auth0 合作夥伴主題](auth0-overview.md)
- [合作夥伴主題概觀](partner-topics-overview.md)
- [變成事件方格合作夥伴](partner-onboarding-overview.md)