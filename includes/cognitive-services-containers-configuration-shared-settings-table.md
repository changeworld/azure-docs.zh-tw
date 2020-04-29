---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484095"
---
容器具有下列設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可將[Azure 應用程式 Insights](https://docs.microsoft.com/azure/application-insights)遙測支援新增至您的容器。|
|是|[計費](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄和（選擇性）計量資料寫入至 Fluentd 伺服器。|
|否|HTTP Proxy|設定 HTTP proxy 以進行輸出要求。|
|否|[記錄](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|從主機電腦將資料讀取和寫入至容器，並將其從容器還原至主機電腦。|
