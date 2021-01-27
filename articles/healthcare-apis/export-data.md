---
title: 在 Azure API for FHIR 上叫用 $export 命令以執行匯出
description: 本文說明如何使用 $export 匯出 FHIR 資料
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920252"
---
# <a name="how-to-export-fhir-data"></a>如何匯出 FHIR 資料


大量匯出功能可讓您根據 [FHIR 規格](https://hl7.org/fhir/uv/bulkdata/export/index.html)，從 FHIR 伺服器匯出資料。 

使用 $export 之前，您會想要確定 Azure API for FHIR 已設定為使用它。 若要設定匯出設定和建立 Azure 儲存體帳戶，請參閱 [[設定匯出資料] 頁面](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

設定匯出 Azure API for FHIR 之後，您可以使用 $export 命令，將資料從服務中匯出。 資料會儲存到您在設定匯出時所指定的儲存體帳戶中。 若要瞭解如何在 FHIR 伺服器中叫用 $export 命令，請參閱 [HL7 FHIR $export 規格](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的相關檔。 

適用于 FHIR 的 Azure API 支援下列層級的 $export：
* [系統](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)： `GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)： `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [一組患者 *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -Azure API for FHIR 會匯出所有相關的資源，但不會匯出群組的特性： `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export``Group/[ID]/$export`如果資源是在多個資源的區間中，或在多個群組中，則可能會匯出重複的資源。

此外，也支援在佇列期間透過 location 標頭所傳回的 URL 來檢查匯出狀態，並取消實際的匯出作業。

## <a name="settings-and-parameters"></a>設定和參數

### <a name="headers"></a>標題
$Export 作業必須設定兩個必要的標頭參數。 這些值是由目前的 [$export 規格](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)所定義。
* **Accept** -application/fhir + json
* **慣用** -回應-非同步

### <a name="query-parameters"></a>查詢參數
Azure API for FHIR 支援下列查詢參數。 這些參數都是選擇性的：

|查詢參數        | 由 FHIR 規格定義？    |  描述|
|------------------------|---|------------|
| \_>outputformat | 是 | 目前支援三個值以配合 FHIR 規格： application/FHIR + ndjson、application/ndjson 或單純 ndjson。 所有匯出作業都會 `ndjson` 傳回，而且傳遞的值不會影響程式程式碼為。 |
| \_自 | 是 | 允許您只匯出自提供時間之後修改過的資源 |
| \_類型 | 是 | 可讓您指定所要包含的資源類型。 例如， \_ type = 患者只會傳回患者資源|
| \_typefilter | 是 | 若要要求更精細的篩選，您可以 \_ 搭配使用 typefilter 與 \_ 型別參數。 _TypeFilter 參數的值是以逗號分隔的 FHIR 查詢清單，可進一步限制結果 |
| \_容器 | 否 |  在設定的儲存體帳戶中，指定應該匯出資料的容器。 如果指定容器，則會將資料匯出至新資料夾中名稱為的容器。 如果未指定容器，則會使用時間戳和作業識別碼將它匯出至新的容器。 |


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 $export 命令匯出 FHIR 資源。 接下來，瞭解如何匯出已取消識別的資料：
 
>[!div class="nextstepaction"]
>[匯出已取消識別的資料](de-identified-export.md)
