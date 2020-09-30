---
title: 在 Azure API for FHIR 上叫用 $export 命令以執行匯出
description: 本文說明如何設定和使用取消識別的匯出
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ee7ba96a7cc8789e1a949db80bc84c63b28f4518
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531661"
---
# <a name="how-to-export-fhir-data"></a>如何匯出 FHIR 資料

大量匯出功能可讓您根據 [FHIR 規格] (，從 FHIR 伺服器匯出資料 https://hl7.org/fhir/uv/bulkdata/export/index.html) 。 

使用 $export 之前，您會想要確定 Azure API for FHIR 已設定為使用它。 若要設定匯出設定和建立 Azure 儲存體帳戶，請參閱 [[設定匯出資料] 頁面](configure-export-data.md)。

## <a name="using-export-command"></a>使用 $export 命令

設定匯出 Azure API for FHIR 之後，您可以使用 $export 命令，將資料從服務中匯出。 資料會儲存到您在設定匯出時所指定的儲存體帳戶中。 若要瞭解如何在 FHIR 伺服器中叫用 $export 命令，請閱讀 [$export 規格](https://hl7.org/Fhir/uv/bulkdata/export/index.html)的相關檔。 

Azure API for FHIR 中的 $export 命令會採用選擇性的_ \_ 容器_參數，該參數會指定已設定之儲存體帳戶內應匯出資料的容器。 如果指定容器，則會將資料匯出至新資料夾中名稱為的容器。 如果未指定任何容器，則會將資料匯出至名稱為的新容器

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>支援的案例

Azure API for FHIR 支援系統、患者和群組層級的 $export。 針對群組匯出，我們會匯出所有相關的資源，但不會匯出群組的特性。

> [!Note] 
> 如果資源是在多個資源的區間中，或在多個群組中，$export 會匯出重複的資源。

此外，也支援在佇列期間透過 location 標頭所傳回的 URL 來檢查匯出狀態，並取消實際的匯出作業。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 $export 命令匯出 FHIR 資源。 接下來，您可以複習我們支援的功能
 
>[!div class="nextstepaction"]
>[支援的功能](fhir-features-supported.md)
