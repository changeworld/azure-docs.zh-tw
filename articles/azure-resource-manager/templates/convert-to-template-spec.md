---
title: 將入口網站範本轉換成範本規格
description: 說明如何將 Azure 入口網站資源庫中的現有範本轉換成範本規格。
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739091"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>將入口網站中的範本資源庫轉換為範本規格

Azure 入口網站提供一種方式，可在您的帳戶中儲存 Azure Resource Manager 範本 (ARM) 範本。 **此功能即將淘汰。** 若要繼續使用此資源庫中的範本，請將其轉換為 [範本規格](template-specs.md)。

本文說明如何將範本庫中現有的範本轉換成範本規格。

在入口網站中，被取代的功能稱為 **範本 (預覽)**。 若要查看是否有任何要轉換的範本，請 [在入口網站中查看範本資源庫](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)。 這些範本具有資源類型 `Microsoft.Gallery/myareas/galleryitems` 。

## <a name="deprecation-of-portal-feature"></a>取代入口網站功能

入口網站中的範本資源庫即將于2021年1月21日淘汰。 您可以繼續使用它，直到2月21日為止。 從2月22日開始，您無法在入口網站資源庫中建立新範本，但是您仍然可以查看和部署現有的範本。

自6月22日起，將會從入口網站中移除此功能，而且將會封鎖所有 API 作業。 您將無法從資源庫查看或部署任何範本。

在6月22日前，您應該遷移任何您想要繼續使用的範本。 您可以使用本文中所示的其中一個方法來遷移範本。 移除此功能之後，您必須開啟支援案例，以取得任何您未遷移的範本。

## <a name="convert-with-powershell-script"></a>使用 PowerShell 腳本進行轉換

若要簡化範本資源庫中的轉換範本，請使用 Azure 快速入門範本存放庫中的 PowerShell 腳本。 當您執行腳本時，您可以為每個範本建立新的範本規格，或下載可建立範本規格的範本。腳本不會刪除範本庫中的範本。

1. 複製 [遷移腳本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1)。 以名稱 *Migrate-GalleryItems.ps1* 儲存本機複本。
1. 若要建立新的範本規格，請提供 `-ResourceGroupName` 和 `-Location` 參數的值。 

   設定 `ItemsToExport` 為 `MyGalleryItems` 以匯出您的範本。 將它設定為， `AllGalleryItems` 以匯出您有權存取的所有範本。

   下列範例會針對名為 **migratedRG** 的資源群組中的每個範本，建立新的範本規格。 此腳本會建立資源群組（如果不存在的話）。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. 若要下載可用來建立範本規格的範本，請不要提供資源群組或位置的值。 請改為指定 `-ExportToFile` 。 範本與您在資源庫中的範本不同。 相反地，它包含範本規格資源，可建立範本的範本規格。

   下列範例會下載範本，而不建立範本規格。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   若要瞭解如何部署範本以建立範本規格，請參閱 [快速入門：建立和部署範本規格 (預覽) ](quickstart-create-template-specs.md)。

如需腳本及其參數的詳細資訊，請參閱 [從範本資源庫範本建立 TemplateSpecs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)。

## <a name="manually-convert-through-portal"></a>透過入口網站手動轉換

您可以從資源庫手動將範本複製到新的範本規格。

1. 在入口網站中開啟 [範本 (預覽) ](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) 。
1. 選取要遷移的範本。
1. 選取 [ **視圖範本**]。
1. 複製範本內容。
1. 在入口網站的搜尋列中，搜尋 **範本規格**。 選取該選項。
1. 選取 [建立範本規格]。
1. 提供名稱、訂用帳戶、資源群組、位置和版本的值。
1. 選取 **[下一步：編輯範本]**。
1. 針對範本的內容，貼上您從範本庫複製的範本。
1. 選取 [檢閱 + 建立]  。
1. 驗證成功完成後，請選取 [ **建立**]。

如果您需要與組織中的其他使用者共用範本規格，請 [將角色型存取控制設定](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) 為需要存取的群組或使用者。

## <a name="next-steps"></a>後續步驟

若要深入瞭解範本規格，請參閱 [建立和部署範本規格](template-specs.md)。
