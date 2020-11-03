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
ms.openlocfilehash: 080e2daf5065c0762fb039a84e62580e5c915ddb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735164"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站從 Azure Key Vault 設定及擷取祕密

Azure Key Vault 是一項雲端服務，可為祕密提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本快速入門中，您會建立金鑰保存庫，然後用它來儲存祕密。 如需 Key Vault 的詳細資訊，您可以檢閱[概觀](../general/overview.md)。

如需祕密的詳細資訊，請參閱[關於祕密](about-secrets.md)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vault"></a>建立保存庫

1. 從 Azure 入口網站功能表或 **首頁** ，選取 [建立資源]。
2. 在 [搜尋] 方塊中輸入 **Key Vault** 。
3. 從結果清單中，選擇 [Key Vault]。
4. 在 [金鑰保存庫] 區段上選擇 [建立]。
5. 在 [建立金鑰保存庫] 區段上提供下列資訊：
    - **Name** ：唯一名稱是必要項。 在本快速入門中，我們使用 **Contoso-vault2** 。 
    - 訂用帳戶：選擇訂用帳戶。
    - 在 [資源群組] 底下，選擇 [新建]，然後輸入資源群組名稱。
    - 在 [位置] 下拉式功能表中選擇位置。
    - 將其他的選項保留預設值。
6. 提供上述資訊之後，請選取 [建立]。

請記下下列兩個屬性：

* **保存庫名稱** ：在此範例中是 **Contoso-Vault2** 。 您將在其他步驟中使用此名稱。
* **保存庫 URI** ：在此範例中是 https://contoso-vault2.vault.azure.net/ 。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

您也可使用 Azure CLI 和 PowerShell 來建立 Key Vault：
- [使用 PowerShell 建立 Key Vault](../general/quick-create-powershell.md)
- [使用 Azure CLI 建立 Key Vault](../general/quick-create-cli.md)

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行作業的帳戶。

![Key Vault 建立完成後的輸出](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將祕密新增至保存庫，您只需要採取一些額外步驟。 在此情況下，我們會新增應用程式可以使用的密碼。 此密碼稱為 **ExamplePassword** ，且我們會在其中儲存 **hVFkk965BuUv** 值。

1. 在 Key Vault 屬性頁面上，選取 [祕密]。
2. 按一下 [產生/匯入]。
3. 在 [建立祕密] 畫面上選擇下列值：
    - **上傳選項** ：手動。
    - **Name** ：ExamplePassword。
    - **值** ：hVFkk965BuUv
    - 將其他的值保留預設值。 按一下頁面底部的 [新增]  。

一旦收到已成功建立祕密的訊息，即可按一下清單上的祕密。 

## <a name="retrieve-a-secret-from-key-vault"></a>從 Key Vault 擷取祕密

如果您按一下目前的版本，您可以看到您在上一個步驟中指定的值。

![祕密屬性](../media/quick-create-portal/current-version-hidden.png)

按一下右側窗格中的 [顯示祕密值] 按鈕，即可看到隱藏的值。 

![顯示祕密值](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Key Vault 並在其中儲存祕密。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 請閱讀[針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)
- 請參閱[使用 Key Vault 搭配 App Service Web 應用程式](../general/tutorial-net-create-vault-azure-web-app.md)
- 請參閱[使用 Key Vault 搭配部署至 VM 的應用程式](../general/tutorial-net-virtual-machine.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
