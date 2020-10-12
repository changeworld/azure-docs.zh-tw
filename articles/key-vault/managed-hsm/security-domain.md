---
title: 關於 Azure 受控 HSM 安全性網域
description: 概述受管理的 HSM 安全性網域，這是復原受管理的 HSM 所需的一組核心認證
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994642"
---
# <a name="about-the-managed-hsm-security-domain"></a>關於受管理的 HSM 安全性網域

受控 HSM 安全性網域 (SD) 是在發生嚴重損壞時，復原受管理的 HSM 所需的一組核心認證。 安全性網域是在受管理的 HSM 硬體和服務軟體記憶體保護區中產生，並且代表 HSM 的「擁有權」。

每個受管理的 HSM 都必須要有要操作的安全性網域。 當您要求新的受管理 HSM 時，它會布建，但在您下載安全性網域之前不會啟動。 當受管理的 HSM 處於布建但未啟用時，有兩種方式可以啟用它：
- 下載您的安全性網域是預設的方法，可讓您安全地儲存安全網域，以與另一個受管理的 HSM 搭配使用，或從整體嚴重損壞中復原。
- 上傳您現有的安全性網域，這可讓您建立多個受管理的 HSM 實例，以共用相同的安全性網域。

## <a name="download-your-security-domain"></a>下載您的安全性網域

當受管理的 HSM 布建但未啟動時，下載安全性網域會捕捉從所有硬體完全遺失的復原所需的核心認證。 若要下載安全性網域，您必須建立至少 3 (10) RSA 金鑰組，並在要求安全性網域下載時，將這些公開金鑰傳送至服務。 您也必須指定 (仲裁) 在未來解密安全性網域所需的最小金鑰數目。 受控 HSM 將會初始化安全性網域，並使用您使用 [Shamir 的秘密共用演算法](https://dl.acm.org/doi/10.1145/359168.359176)提供的公開金鑰來加密它。 只有在有可用的私密金鑰仲裁時，才能解密您下載的安全性網域 blob;您必須保護私密金鑰安全，以確保安全性網域的安全性。 下載完成之後，受管理的 HSM 將處於啟用狀態，可供使用。  

> [!IMPORTANT]
> 若要進行完整的嚴重損壞修復，您必須具有安全性網域，以及用來保護它的私密金鑰仲裁，以及完整的 HSM 備份。 如果遺失安全性網域或有足夠的 RSA 金鑰 (私密金鑰) 以失去仲裁，而且沒有任何執行中的受控 HSM 實例，就無法進行嚴重損壞修復。

## <a name="upload-a-security-domain"></a>上傳安全性網域

受控 HSM 布建但未啟用時，您可以起始安全性網域復原程式。 受控 HSM 會產生 RSA 金鑰組，並傳回公開金鑰。 然後，您可以將安全性網域上傳至受控 HSM。 上傳之前，必須提供用戶端 (Azure CLI 或 PowerShell) ，並提供您在下載安全性網域時所使用之私密金鑰的最小仲裁數目。 用戶端會使用此仲裁來解密安全性網域，並使用您在要求復原時下載的公開金鑰來重新加密。 上傳完成後，受管理的 HSM 會處於啟用狀態。

## <a name="backup-and-restore-behavior"></a>備份與還原行為

備份 (完整備份或單一金鑰備份) 只有在來源管理的 HSM (建立備份) ，以及將還原備份的目的地管理 HSM () 共用相同的安全性網域時，才能成功還原。 如此一來，安全性網域也會定義每個受控 HSM 的密碼編譯界限。

## <a name="next-steps"></a>後續步驟

- 閱讀[受控 HSM 的概觀](overview.md)
- 瞭解如何 [使用 Azure CLI 管理受控 HSM](key-management.md)
- 檢閱[受控 HSM 最佳做法](best-practices.md)
