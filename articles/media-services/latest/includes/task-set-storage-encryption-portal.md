---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013214"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>在儲存體帳戶上設定加密

1. 在 Azure 入口網站畫面頂端的 **搜尋** 欄位中輸入想要加密的儲存體帳戶名稱。  相符項目會出現在搜尋欄位下方。
1. 選取您尋找的儲存體帳戶。 儲存體帳戶畫面隨即出現。
1. 選取 [加密]。
1. 選取 [Microsoft 自控金鑰] 或 [客戶自控金鑰]。

### <a name="use-microsoft-managed-keys"></a>使用 Microsoft 自控金鑰

根據預設，儲存體帳戶中的資料會使用 Microsoft 自控金鑰加密。

### <a name="use-customer-managed-keys"></a>使用客戶管理的金鑰

1. 選取 [客戶自控金鑰]。
1. 選取 [輸入金鑰 URI] 或 [從金鑰保存庫選取]。
    1. 如果您選取 [輸入金鑰 URI]，請在金鑰 URI 欄位中輸入金鑰 URI，然後選取訂用帳戶。 (系統可能已預設選取。)
    1. 如果您選取 [從金鑰保存庫選取]，則接著請選取 [選取金鑰保存庫和金鑰]。 Azure Key Vault 畫面中的選取金鑰隨即出現。
1. 選取您想要使用的 **Key Vault** ，然後選取您在金鑰保存庫中已有的金鑰，或 **建立新的金鑰**。
    1. 如果您選擇建立新的金鑰，請從 **選項** 中選取 [產生] 或 [匯入]。 您只能匯入 RSA 金鑰。
    1. 若要產生新的金鑰，請在 **名稱** 欄位中提供金鑰名稱，然後選取金鑰類型：
        1. RSA - 金鑰大小：2048、3072 或 4096。 這是客戶最常選擇的項目。
        1. EC - 橢圓曲線名稱：P-256、P-384、P-521 或 P-256K
        1. 您可以選擇性地設定金鑰的啟用和到期日期。
        1. 選取 [是] 以啟用金鑰自動輪替。
        1. 選取 [建立]。
    1. 若要匯入金鑰，請按一下 [選取檔案] 欄位中的任何位置，以選取要上傳的檔案。
        1. 在 **名稱** 欄位中提供金鑰名稱。
        1. 您可以選擇性地設定金鑰的啟用和到期日期。
        1. 選取 [是] 以啟用金鑰自動輪替。
        1. 選取 [建立]。
    1. 選取 [選取] 以選取此金鑰來加密您的儲存體帳戶。 系統會將您帶回加密畫面。
1. **重要！** 選取 [儲存] 以儲存加密設定，否則您剛執行的所有工作都會遺失。
