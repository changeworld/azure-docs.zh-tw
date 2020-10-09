---
title: '針對 Azure API for FHIR 匯出已取消識別的資料 (預覽) '
description: 本文說明如何設定和使用取消識別的匯出
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: bdbab0e032764d07119402686051d391376cb913
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843795"
---
# <a name="exporting-de-identified-data-preview"></a>匯出已取消識別的資料 (預覽) 

> [!Note] 
> 使用取消識別的匯出時所產生的結果，將會根據像是輸入資料和客戶所選取的功能等因素而有所不同。 Microsoft 無法評估已取消識別的匯出輸出，或判斷客戶的使用案例和合規性需求的 acceptability。 不保證已取消識別的匯出不符合任何特定的法律、法規或合規性需求。

$Export 命令也可以用來從 FHIR 伺服器匯出已取消識別的資料。 它會使用 [FHIR tools for 匿名化](https://github.com/microsoft/FHIR-Tools-for-Anonymization)的匿名化引擎，並在查詢參數中採用匿名化設定詳細資料。 您可以建立自己的匿名化設定檔，或使用 HIPAA 安全港方法的 [範例設定檔](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 作為起點。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|查詢參數            | 範例 |Optionality| 描述|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js開啟|取消識別的匯出所需 |設定檔案的名稱。 請參閱 [這裡](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)的設定檔案格式。 在設定為匯出位置的相同 Azure 儲存體帳戶內，此檔案應該保留在名為 **匿名化** 的容器內。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|取消識別匯出的選擇性|這是設定檔的 Etag。 您可以使用 Azure 儲存體 explorer 從 blob 屬性取得 Etag|

> [!IMPORTANT]
> 原始匯出和已取消識別的匯出都會寫入至指定為匯出設定一部分的相同 Azure 儲存體帳戶。 建議您使用不同的容器對應至不同的已取消識別設定，並在容器層級管理使用者存取。