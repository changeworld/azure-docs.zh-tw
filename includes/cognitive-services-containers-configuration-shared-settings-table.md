---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484095"
---
容器具有以下配置設置：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|跟蹤帳單資訊。|
|否|[應用洞察](#applicationinsights-setting)|啟用將[Azure 應用程式見解](https://docs.microsoft.com/azure/application-insights)遙測支援添加到容器。|
|是|[計費](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將日誌和指標資料寫入 Fluentd 伺服器，並可選擇使用。|
|否|HTTP 代理|配置用於發出出站請求的 HTTP 代理。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|將資料從主機讀取和寫入容器，從容器讀取和寫入主機。|
