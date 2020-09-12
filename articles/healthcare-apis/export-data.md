---
title: 在 Azure API for FHIR 上叫用 $export 命令以執行匯出
description: 本文說明如何設定和使用取消識別的匯出
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482312"
---
# <a name="how-to-export-fhir-data"></a>如何匯出 FHIR 資料

如需設定匯出設定和建立 Azure 儲存體帳戶的相關內容，請參閱 [這裡](configure-export-data.md)。

## <a name="exporting-fhir-resources-using-export-command"></a>使用 $export 命令匯出 FHIR 資源

設定匯出 Azure API for FHIR 之後，我們現在可以使用 $export 命令，將資料從服務匯出至我們在設定匯出時所指定的儲存體帳戶。 若要瞭解如何在 FHIR 伺服器中叫用 $export 命令，請參閱 $export 規格的相關檔 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 。 

Azure API for FHIR 中的 $export 命令會採用選擇性的_ \_ 容器_參數，可用來指定已設定之儲存體帳戶內的容器，該容器會將資料匯出至其中。

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> 請注意，目前 Azure API for FHIR 僅支援 $export 規格中定義的系統層級匯出 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 。 此外，只有_ \_ 因為_目前支援查詢參數。

## <a name="exporting-de-identified-data-preview"></a>匯出已取消識別的資料 (預覽) 

$Export 命令也可以用來從 FHIR 伺服器匯出已取消識別的資料。 它會使用 [FHIR tools for 匿名化](https://github.com/microsoft/FHIR-Tools-for-Anonymization)的匿名化引擎，並在查詢參數中採用匿名化設定詳細資料。 您可以建立自己的匿名化設定檔，或使用 HIPAA 安全港方法的 [範例設定檔](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 作為起點。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|查詢參數            | 範例 |Optionality| 描述|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js開啟|取消識別的匯出所需 |設定檔案的名稱。 請參閱 [這裡](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)的設定檔案格式。 在設定為匯出位置的相同 Azure 儲存體帳戶內，此檔案應該保留在名為 **匿名化** 的容器內。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|取消識別匯出的選擇性|這是設定檔的 Etag。 您可以使用 blob 屬性中的 Azure 儲存體總管來取得 Etag|

> [!IMPORTANT]
> 請注意，原始匯出和取消識別的匯出都會寫入至指定為匯出設定一部分的相同 Azure 儲存體帳戶。 建議您使用不同的容器對應至不同的已取消識別設定，並在容器層級管理使用者存取。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解如何使用 $export 命令匯出 FHIR 資源，包括已取消識別的資料。 接下來，您可以設定匯出資料：
 
>[!div class="nextstepaction"]
>[設定匯出資料](configure-export-data.md)
