---
title: 使用媒體服務) 攜帶您自己的金鑰 (客戶管理的金鑰
description: 您可以使用客戶管理的金鑰 (也就是使用媒體服務) 攜帶您自己的金鑰。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a56922c972efeb21c188413522bd05f83b74ca12
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681817"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>使用媒體服務將您自己的金鑰 (客戶管理的金鑰) 

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

攜帶您自己的金鑰 (BYOK) 是可協助客戶將其工作負載移至雲端的 Azure 全公司方案。 客戶管理的金鑰可讓客戶遵守產業合規性法規，並改善服務的租使用者隔離。 讓客戶控制加密金鑰是將不必要的存取和控制和 Microsoft 服務的信賴度降至最低的方式。

## <a name="keys-and-key-management"></a>金鑰和金鑰管理

當您使用媒體服務 2020-05-01 API 時，您可以使用您自己的金鑰搭配媒體服務。 系統會為媒體服務所擁有的系統金鑰所加密的所有帳戶建立預設帳戶金鑰。 當您使用自己的金鑰時，帳戶金鑰會以您的金鑰加密。 內容金鑰會以帳戶金鑰加密。 JobInputHttp url 和對稱權杖驗證金鑰也會加密。

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="客戶管理的金鑰會取代系統管理的金鑰":::

媒體服務會使用媒體服務帳戶的受控識別，從您所擁有的 Key Vault 讀取您的金鑰。 媒體服務要求 Key Vault 位於與帳戶相同的區域中，且已啟用虛刪除和清除保護。

您的金鑰可以是2048、3072或 4096 RSA 金鑰，同時支援 HSM 和軟體金鑰。

> [!NOTE]
> 不支援 EC 索引鍵。

您可以指定金鑰名稱和金鑰版本，或只指定索引鍵名稱。 當您只使用金鑰名稱時，媒體服務將會使用最新的金鑰版本。 系統會自動偵測新版本的客戶金鑰，並重新加密帳戶金鑰。

> [!WARNING]
> 媒體服務會監視客戶金鑰的存取權。 如果客戶金鑰變成無法存取 (例如，金鑰已遭刪除，或已刪除 Key Vault 或已移除存取權授與) ，則媒體服務會將帳戶轉換為客戶金鑰無法存取的狀態， (有效地停用帳戶) 。 不過，您可以在此狀態下刪除帳戶。 唯一支援的作業是帳戶取得、列出和刪除;所有其他要求 (編碼、串流等) 將會失敗，直到帳戶金鑰的存取權還原為止。

## <a name="tutorials"></a>教學課程

- [使用 Azure 入口網站搭配媒體服務使用客戶管理的金鑰或 BYOK](tutorial-byok-portal.md)
- [使用客戶管理的金鑰或 BYOK 搭配媒體服務 REST API](tutorial-byok-postman.md)。

## <a name="next-steps"></a>後續步驟

[使用媒體服務動態加密來保護您的內容](content-protection-overview.md)
