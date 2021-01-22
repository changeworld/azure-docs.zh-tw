---
title: 概念-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的概念。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682374"
---
# <a name="event-grid-concepts"></a>Event Grid 概念

本文說明 Azure 事件方格中的主要概念。

## <a name="events"></a>事件

事件是完整說明系統中發生內容的最小量資訊。 每個事件都有一般資訊，例如：事件來源、事件發生的時間，以及唯一識別碼。 每個事件也有只與特定事件類型相關的特定資訊。 大小上限為 1 MB 之事件的支援目前為預覽狀態。

如需事件中所包含的屬性，請參閱 [Azure 事件方格事件架構](event-schemas.md)。

## <a name="publishers"></a>發行者

發行者是決定將事件傳送至 Event Grid 的使用者或組織。 您可以從您自己的應用程式發行事件。

## <a name="event-sources"></a>事件來源

事件來源是事件發生的地點。 每個事件來源都與一或多個事件類型相關。 例如，Azure 儲存體是 Blob 建立事件的事件來源。 您的應用程式是您定義之自訂事件本身的事件來源。 事件來源負責將事件傳送至事件方格。

## <a name="topics"></a>主題

Event Grid 主題提供來源傳送事件的端點。 發行者會建立 Event Grid 主題，並決定事件來源是否需要一個主題或多個主題。 主題可用於相關事件的集合。 若要回應某些類型的事件，訂閱者會決定要訂閱的主題。

設計應用程式時，您可以彈性地決定要建立多少個主題。 對於大型解決方案，請為每個類別的相關事件建立一個自訂主題。 例如，請考慮使用應用程式來傳送與修改使用者帳戶和處理訂單有關的事件。 任何事件處理常式不太需要兩種類別的事件。 建立兩個自訂主題，並讓事件處理常式訂閱其感興趣的自訂主題。 對於小型解決方案，您可能會想要將所有事件傳送至單一主題。 事件訂閱者可以篩選出他們想要的事件類型。

請參閱 [REST API 檔](api.md) ，以瞭解如何管理事件方格中的主題。

## <a name="event-subscriptions"></a>事件訂閱

訂閱會告知事件方格您有興趣接收的主題事件。 您建立訂閱時，可提供處理事件的端點。 您可以篩選傳送至端點的事件。 

請參閱 [REST API 檔](api.md) ，以瞭解如何在事件方格中管理訂閱。

## <a name="event-handlers"></a>事件處理常式

從 Event Grid 的觀點而言，事件處理常式是傳送事件的位置。 處理常式會採取進一步的動作來處理事件。 事件方格支援數個處理常式類型。 您可以使用支援的 Azure 服務或您自己的 webhook 做為處理常式。 視處理常式類型而定，Event Grid 依照不同的機制來保證事件的傳遞。 如果目的地事件處理常式是 HTTP webhook，則會重試此事件，直到處理程式傳回的狀態碼為止 `200 – OK` 。 針對 edge 中樞，如果在未發生任何例外狀況的情況下傳遞事件，則會將其視為成功。

## <a name="security"></a>安全性

Event Grid 提供訂閱主題和發佈主題的安全性。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

## <a name="event-delivery"></a>事件傳遞

如果事件方格無法確認訂閱者端點是否收到事件，則會重新傳遞事件。 如需詳細資訊，請參閱 [Event Grid 訊息傳遞與重試](delivery-retry.md)。

## <a name="batching"></a>批次處理

在使用自訂主題時，事件必須一律發佈在陣列中。 針對低輸送量案例，陣列只會有一個值。 針對大量使用案例，建議您在每次發佈時一併批次處理數個事件，以達到更高的效率。 批次最多可達 1 MB。 每個事件的 (預覽) 不應大於 1 MB。