---
title: 在 Azure 雲端服務 (延伸支援) 中儲存和使用憑證
description: '在 Azure 雲端服務 (延伸支援中儲存和使用憑證的流程) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744315"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>使用具有 Azure 雲端服務 (延伸支援的憑證) 

Key Vault 是用來儲存與雲端服務相關聯的憑證， (延伸支援) 。 您可以透過 [Azure 入口網站](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) 和 [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)建立金鑰保存庫。 將憑證新增至 Key Vault，然後參考服務設定檔中的憑證指紋。 您也需要啟用適當許可權的 Key Vault，讓雲端服務 (延伸支援) 資源可以從 Key Vault 中取出儲存為秘密的憑證。  

## <a name="upload-a-certificate-to-key-vault"></a>將憑證上傳至 Key Vault 

1.  登入 Azure 入口網站，然後流覽至 Key Vault。 如果您沒有設定 Key Vault，則可以選擇在此相同視窗中建立一個。

2. 選取 **存取** 原則

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="影像會顯示從金鑰保存庫分頁選取存取原則。":::

3. 確定存取原則包含下列屬性：
    - **啟用存取 Azure 虛擬機器以進行部署**
    - **啟用範本部署 Azure Resource Manager 存取** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="影像會顯示 Azure 入口網站中的 [存取原則] 視窗。":::
 
4.  選取 **憑證** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="影像會顯示在 Azure 入口網站的 [金鑰保存庫] 的 [分頁原則] 視窗中選取 [憑證] 選項。":::

5. 選取 [**產生/匯入**]

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="顯示選取 [產生/匯入] 選項的影像":::

4.  完成所需的資訊以完成上傳憑證。 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="影像會顯示 Azure 入口網站中的匯入視窗。":::

5.  將憑證詳細資料新增至服務設定 ( .cscfg) 檔案中的角色。 請確定 Azure 入口網站中的憑證指紋符合服務設定 ( .cscfg) 檔中的指紋。 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。