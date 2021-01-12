---
title: 包含檔案
description: 包含檔案
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121580"
---
| 網域名稱                                          | 輸出連接埠 | 描述                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| 公用雲端： `*.servicebus.windows.net` <br> Azure Government： `*.servicebus.usgovcloudapi.net` <br> 中國： `*.servicebus.chinacloudapi.cn`   | 443            | 必須提供此資訊，自我裝載整合執行階段才能進行互動式撰寫。 |
| 公用雲端： `{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` <br> Azure Government： `{datafactory}.{region}.datafactory.azure.us` <br> 中國： `{datafactory}.{region}.datafactory.azure.cn` | 443            | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 <br>針對公用雲端中新建立的 Data Factory，請從您的自我裝載 Integration Runtime 金鑰（格式為 {datafactory}）中尋找 FQDN。{region}. datafactory。 針對舊版 Data Factory，如果您在自我裝載整合金鑰中看不到 FQDN，請改為使用 *.frontend.clouddatahub.net。 |
| `download.microsoft.com`    | 443            | 自我裝載整合執行階段所需，以用於下載更新。 如果您已停用自動更新，您可以略過設定此網域。 |
| 金鑰保存庫 URL | 443           | Azure Key Vault 如果您將認證儲存在 Key Vault 中，則為必要項。 |
