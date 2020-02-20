---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474213"
---
對容器的查詢會以用於 `ApiKey`之 Azure 資源的定價層計費。

Azure 認知服務容器未連線至計量/計費端點，因此無法執行授權。 您必須讓容器隨時都能與計量端點進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

### <a name="connect-to-azure"></a>連線到 Azure

容器需要計費引數值才能執行。 這些值讓容器能夠連線到計費端點。 容器會每隔 10 到 15 分鐘回報使用量。 如果容器未在允許的時間範圍內連線到 Azure，容器會繼續執行，但在還原計費端點之前不會提供查詢。 以 10 到 15 分鐘的相同時間間隔嘗試連線 10 次。 如果在嘗試10次後無法連接到計費端點，容器就會停止服務要求。

### <a name="billing-arguments"></a>計費引數

當下列三個選項都提供有效的值時， <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">`docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>命令會啟動容器：

| 選項 | 描述 |
|--------|-------------|
| `ApiKey` | 用來追蹤帳單資訊之認知服務資源的 API 金鑰。<br/>此選項的值必須設定為 `Billing`中指定之已布建資源的 API 金鑰。 |
| `Billing` | 用來追蹤帳單資訊之認知服務資源的端點。<br/>此選項的值必須設定為已佈建 Azure 資源的端點 URI。|
| `Eula` | 表示您接受容器的授權。<br/>此選項的值必須設定為 [**接受**]。 |
