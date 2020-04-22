---
title: 伺服器場災難復原
description: 本文介紹了數據恢復如何防止數據丟失。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683900"
---
# <a name="disaster-recovery-for-farmbeats"></a>伺服器場災難復原

資料恢復可保護您在 Azure 區域崩潰等事件中遺失數據。 在這種情況下,您可以啟動故障轉移,並恢復存儲在伺服器場Beats部署中的數據。

數據恢復不是 Azure FarmBeats 中的預設功能。 可以通過配置 FarmBeats 用於在 Azure 配對區域中儲存數據所需的 Azure 資源來手動配置此功能。 使用主動 + 被動方法啟用恢復。

以下各節提供有關如何在 Azure FarmBeats 中設定資料恢復的資訊:

- [開啟資料冗餘](#enable-data-redundancy)
- [從線上備份復原服務](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>開啟資料冗餘

FarmBeats 將資料儲存在三個 Azure 第一方服務中,即**Azure 儲存**,**宇宙 DB**與**時間序列見解**。 使用以下步驟將這些服務的資料冗餘啟用到配對的 Azure 區域:

1.  **Azure 儲存**- 遵循此準則,為伺服器場節拍部署中的每個存儲帳戶啟用數據冗餘。
2.  **Azure Cosmos DB** - 遵循此準則,為伺服器場節拍部署的 Cosmos DB 帳戶啟用數據冗餘。
3.  **Azure 時間序列見解 (TSI)** - TSI 當前不提供資料冗餘。 要恢復時間序列見解數據,請轉到感測器/天氣合作夥伴,並將數據再次推送到 FarmBeats 部署。

## <a name="restore-service-from-online-backup"></a>從線上備份復原服務

您可以啟動故障轉移並恢復儲存的數據,這些資料存儲用於 FarmBeats 部署。 回復 Azure 儲存和 Cosmos DB 的資料後,在 Azure 配對區域中建立另一個伺服器場 Beats 部署,然後使用以下步驟配置新部署以使用還原的資料儲存(即 Azure 儲存和 Cosmos DB)中的數據:

1. [設定 Cosmos DB](#configure-cosmos-db)
2. [設定儲存帳戶](#configure-storage-account)


### <a name="configure-cosmos-db"></a>設定 Cosmos DB

複製還原的 Cosmos DB 的訪問金鑰並更新新的 FarmBeats Datahub 金鑰保管庫。


  ![災害復原](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 複製還原的 Cosmos DB 的 URL 並在新的伺服器場節拍 Datahub 應用服務配置中更新它。 您現在可以在新的伺服器場節拍部署中刪除 Cosmos DB 帳戶。

  ![災害復原](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>設定儲存帳戶

複製還原的儲存帳戶的訪問密鑰,並在新的 FarmBeats Datahub 金鑰保管庫中更新它。

![災害復原](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 確保在新的伺服器場節拍批處理 VM 設定檔中更新存儲帳戶名稱。

![災害復原](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同樣,如果已為加速體儲存帳戶啟用數據恢復,請按照步驟 2 更新新的 FarmBeats 實例中的加速器記憶體儲存帳戶存取金鑰和名稱。
