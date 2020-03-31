---
title: 概念 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: IoT 邊緣事件網格中的概念。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992544"
---
# <a name="event-grid-concepts"></a>Event Grid 概念

本文說明 Azure 事件方格中的主要概念。

## <a name="events"></a>事件

事件是完整說明系統中發生內容的最小量資訊。 每個事件都有一般資訊，例如：事件來源、事件發生的時間，以及唯一識別碼。 每個事件也有只與特定事件類型相關的特定資訊。 當前預覽了對大小為 1 MB 的事件的支援。

有關事件中包含的屬性，請參閱[Azure 事件網格事件架構](event-schemas.md)。

## <a name="publishers"></a>發行者

發行者是決定將事件傳送至 Event Grid 的使用者或組織。 您可以從您自己的應用程式發行事件。

## <a name="event-sources"></a>事件來源

事件來源是事件發生的地點。 每個事件來源都與一或多個事件類型相關。 例如，Azure 儲存體是 Blob 建立事件的事件來源。 您的應用程式是您定義之自訂事件本身的事件來源。 事件來源負責將事件傳送至 Event Grid。

## <a name="topics"></a>主題

Event Grid 主題提供來源傳送事件的端點。 發行者會建立 Event Grid 主題，並決定事件來源是否需要一個主題或多個主題。 主題可用於相關事件的集合。 若要回應某些類型的事件，訂閱者會決定要訂閱的主題。

設計應用程式時，您可以靈活地決定要創建的主題數。 對於大型解決方案，請為每個類別的相關事件建立一個自訂主題。 例如，請考慮使用應用程式來傳送與修改使用者帳戶和處理訂單有關的事件。 任何事件處理常式不太需要兩種類別的事件。 建立兩個自訂主題，並讓事件處理常式訂閱其感興趣的自訂主題。 對於小型解決方案，您可能會想要將所有事件傳送至單一主題。 事件訂閱者可以篩選出他們想要的事件類型。

有關如何在事件網格中管理主題的[REST API 文檔](api.md)。

## <a name="event-subscriptions"></a>事件訂閱

訂閱會告知事件方格您有興趣接收的主題事件。 您建立訂閱時，可提供處理事件的端點。 您可以篩選傳送至端點的事件。 

有關如何在事件網格中管理訂閱的[REST API 文檔](api.md)。

## <a name="event-handlers"></a>事件處理常式

從 Event Grid 的觀點而言，事件處理常式是傳送事件的位置。 處理常式採取進一步操作來處理事件。 事件方格支援數個處理常式類型。 可以使用受支援的 Azure 服務或您自己的 Web 掛鉤作為處理常式。 視處理常式類型而定，Event Grid 依照不同的機制來保證事件的傳遞。 如果目標事件處理常式是 HTTP Web 掛鉤，則當處理常式返回 狀態碼 時，將重試`200 – OK`該事件。 對於邊緣中心，如果事件毫無例外地傳遞，則認為它成功。

## <a name="security"></a>安全性

Event Grid 提供訂閱主題和發佈主題的安全性。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

## <a name="event-delivery"></a>事件傳遞

如果事件方格無法確認訂閱者端點是否收到事件，則會重新傳遞事件。 如需詳細資訊，請參閱 [Event Grid 訊息傳遞與重試](delivery-retry.md)。

## <a name="batching"></a>批次處理

在使用自訂主題時，事件必須一律發佈在陣列中。 對於低輸送量方案，陣列將只有一個值。 對於大批量用例，我們建議您為每個發佈將多個事件批次處理在一起，以實現更高的效率。 批次最多可達 1 MB。 每個事件仍不應大於 1 MB（預覽）。