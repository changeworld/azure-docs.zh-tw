---
title: Azure 快速入門 - 使用 Azure 入口網站從 Key Vault 設定及擷取祕密 | Microsoft Docs
description: 說明如何使用 Azure 入口網站從 Azure Key Vault 設定及擷取祕密的快速入門
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 212e5fb62043c2ffe2b8876249a6aad1d224411d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685846"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站從 Azure Key Vault 設定及擷取祕密

Azure Key Vault 是一項雲端服務，可為祕密提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本快速入門中，您會建立金鑰保存庫，然後用它來儲存祕密。 

如需詳細資訊，請參閱 
- [Key Vault 概觀](../general/overview.md)
- [秘密概觀](about-secrets.md)。

## <a name="prerequisites"></a>先決條件

若要存取 Azure Key Vault，您必須要有 Azure 訂用帳戶。 如果您還沒有訂用帳戶，請先建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，再開始操作。

對密碼的所有存取都會透過 Azure Key Vault 進行。 在本快速入門中，請使用 [Azure 入口網站](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 建立金鑰保存庫。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將秘密新增至保存庫，請依照下列步驟操作：

1. 在 Azure 入口網站中瀏覽至您新的金鑰保存庫
1. 在 Key Vault 設定頁面上，選取 [祕密]。
1. 按一下 [產生/匯入]。
1. 在 [建立祕密] 畫面上選擇下列值：
    - **上傳選項**：手動。
    - **名稱**：輸入祕密的名稱。 秘密名稱在 Key Vault 內必須是唯一的。 其名稱必須是 1-127 個字元的字串，開頭必須是字母，且只能包含 0-9、a-z、A-Z 和 -。 如需命名的詳細資訊，請參閱 [Key Vault 物件、識別碼和版本設定](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning)
    - **值**：輸入祕密的值。 Key Vault API 會以字串的形式接受和傳回秘密值。 
    - 將其他的值保留預設值。 按一下頁面底部的 [新增]  。

一旦收到已成功建立祕密的訊息，即可按一下清單上的祕密。 

如需秘密屬性的詳細資訊，請參閱[關於 Azure Key Vault 秘密](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets)

## <a name="retrieve-a-secret-from-key-vault"></a>從 Key Vault 擷取祕密

如果您按一下目前的版本，您可以看到您在上一個步驟中指定的值。

![祕密屬性](../media/quick-create-portal/current-version-hidden.png)

按一下右側窗格中的 [顯示祕密值] 按鈕，即可看到隱藏的值。 

![顯示祕密值](../media/quick-create-portal/current-version-shown.png)

您也可以使用 [Azure CLI]() 或 [Azure PowerShell]() 來擷取先前建立的秘密。

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。

> [!NOTE]
> 請務必注意，秘密、金鑰、憑證或金鑰保存庫在刪除後，會在可設定的期間 (日曆的 7 到 90 天) 內保持可復原的狀態。 若未指定任何設定，預設復原期間將會設為 90 天。 這可讓使用者有足夠的時間可注意到意外的秘密刪除，並予以因應。 如需關於刪除和復原金鑰保存庫和金鑰保存庫物件的詳細資訊，請參閱 [Azure Key Vault 虛刪除概觀](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存祕密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 請閱讀[針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)
- 請參閱[使用 Key Vault 搭配 App Service Web 應用程式](../general/tutorial-net-create-vault-azure-web-app.md)
- 請參閱[使用 Key Vault 搭配部署至 VM 的應用程式](../general/tutorial-net-virtual-machine.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
