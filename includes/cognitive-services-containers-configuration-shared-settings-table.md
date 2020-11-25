---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006863"
---
容器具有下列設定：

|必要|設定|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|追蹤帳單資訊。|
|否|[ApplicationInsights](#applicationinsights-setting)|可將 [Azure 應用程式 Insights](/azure/application-insights) 遙測支援新增至您的容器。|
|是|[Billing](#billing-configuration-setting)|指定 Azure 上服務資源的端點 URI。|
|是|[Eula](#eula-setting)| 表示您已接受容器的授權。|
|否|[Fluentd](#fluentd-settings)|將記錄檔和（選擇性）計量資料寫入至 Fluentd 伺服器。|
|否|HTTP Proxy|設定 HTTP proxy 以發出輸出要求。|
|否|[Logging](#logging-settings)|提供適用於容器的 ASP.NET Core 記錄支援。 |
|否|[Mounts](#mount-settings)|從主機電腦讀取和寫入容器中的資料，並將其從容器寫入至主機電腦。|