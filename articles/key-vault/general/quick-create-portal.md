---
title: 快速入門 - 使用 Azure 入口網站建立 Azure Key Vault
description: 說明如何使用 Azure 入口網站建立 Azure Key Vault 的快速入門
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: b5f4f3d2b3eda9f00049cee26ae95850d65257d6
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778864"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站來建立金鑰保存庫

Azure Key Vault 是一項雲端服務，可為[金鑰](../keys/index.yml)、[祕密](../secrets/index.yml)和[憑證](../certificates/index.yml)提供安全的存放區。 如需有關 Key Vault 的詳細資訊，請參閱[關於 Azure Key Vault](overview.md)；如需可以儲存在金鑰保存庫中項目的詳細資訊，請參閱[關於金鑰、祕密和憑證](about-keys-secrets-certificates.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本快速入門中，您會使用 [Azure 入口網站](https://portal.azure.com)建立金鑰保存庫。 

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vault"></a>建立保存庫

1. 從 Azure 入口網站功能表或 **首頁**，選取 [建立資源]。
2. 在 [搜尋] 方塊中輸入 **Key Vault**。
3. 從結果清單中，選擇 [Key Vault]。
4. 在 [金鑰保存庫] 區段上選擇 [建立]。
5. 在 [建立金鑰保存庫] 區段上提供下列資訊：
    - **Name**：唯一名稱是必要項。 在本快速入門中，我們使用 **Contoso-vault2**。 
    - 訂用帳戶：選擇訂用帳戶。
    - 在 [資源群組] 底下，選擇 [新建]，然後輸入資源群組名稱。
    - 在 [位置] 下拉式功能表中選擇位置。
    - 將其他的選項保留預設值。
6. 提供上述資訊之後，請選取 [建立]  。

請記下下列兩個屬性：

* **保存庫名稱**：在此範例中是 **Contoso-Vault2**。 您將在其他步驟中使用此名稱。
* **保存庫 URI**：在此範例中是 https://contoso-vault2.vault.azure.net/ 。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行作業的帳戶。

![Key Vault 建立完成後的輸出](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用 Azure 入口網站建立了 Key Vault。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](overview.md)
- 參閱 [Azure Key Vault 開發人員指南](developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](best-practices.md)
