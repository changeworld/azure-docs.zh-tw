---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481409"
---
事件中樞透過分割取用者模式來提供訊息串流，每位取用者只會讀取訊息串流的特定子集 (即資料分割)。 此模式能水平擴充事件處理規模，並提供佇列和主題缺少的其他串流導向功能。

資料分割是經過排序且保存在事件中樞內的事件序列。 當較新的事件送達時，系統會將它們加入序列的結尾。 您可以將資料分割視為一種「認可記錄」。

![事件中樞](./media/event-hubs-partitions/partition.png)

事件中樞會將資料保留一段設定的保留時間，這段時間在事件中樞的所有資料分割上都一樣。 事件會隨著時間經過而到期，因此您無法明確地予以刪除。 資料分割各自獨立，只包含其本身的資料序列，通常會以不同的速度成長。

![事件中樞](./media/event-hubs-partitions/multiple-partitions.png)

資料分割數目是在建立時指定，值必須介於 2 到 32 之間。 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。 資料分割是一種資料組織機制，與取用端應用程式所需的下游平行處理原則有關。 事件中樞內的資料分割數目，與您預期有的並行讀取器數目直接相關。 您可以連絡事件中樞小組，將資料分割數目提高到 32 個以上。

您可能希望將其設置為創建時的最高值，即 32。 請記住，具有多個分區將導致發送到多個分區的事件而不保留順序，除非您將發送方配置為僅發送到 32 個分區中的單個分區，使其餘 31 個分區變得冗余。 在前一種情況下，您必須讀取所有 32 個分區的事件。 在後一種情況下，除了在事件處理器主機上必須進行的額外配置外，沒有明顯的額外費用。

雖然可以識別並直接傳送至資料分割，但是不建議直接傳送至資料分割。 相反，您可以使用[事件發佈器](../articles/event-hubs/event-hubs-features.md#event-publishers)部分中引入的更高級別構造。 

資料分割中會填入一連串事件資料，內含事件本文、使用者定義屬性包，以及中繼資料，例如在資料分割中的位移和串流序列中的編號。

我們建議您平衡 1：1 輸送量單位和分區，以實現最佳比例。 單個分區具有最多一個輸送量單位的保證入口和出口。 雖然您可能能夠在分區上實現更高的輸送量，但性能不保證。 因此，我們強烈建議事件中心中的分區數大於或等於輸送量單位數。

給定您計畫所需的總輸送量，您知道所需的輸送量單位數和最小分區數，但應該有多少分區？ 根據要實現的下游並行性以及未來的輸送量需求選擇分區數。 事件中心內的分區數不收取任何費用。

如需資料分割和取捨可用性和可靠性的詳細資訊，請參閱[事件中樞程式設計指南](../articles/event-hubs/event-hubs-programming-guide.md#partition-key)和[事件中樞的可用性和一致性](../articles/event-hubs/event-hubs-availability-and-consistency.md)文章。
