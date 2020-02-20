---
title: Red Hat 保留方案折扣 - Azure
description: 了解 Red Hat 方案折扣如何套用至虛擬機器上的 Red Hat 軟體。
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 173967356c17150d5694b48e6ed2acd78155ca4b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199223"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>了解 Azure 會如何套用 Red Hat Linux Enterprise 軟體保留方案折扣

在您購買 Red Hat Linux 方案之後，折扣就會自動套用到符合保留的已部署 Red Hat 虛擬機器 (VM)。 Red Hat Linux 方案涵蓋在 Azure VM 上執行 Red Hat 軟體的成本。

若要購買適當的 Red Hat Linux 方案，您需要了解您執行哪些 Red Hat VM，以及這些 VM 上的 vCPU 數目。 使用下列各節，可協助您從使用量 CSV 檔案中找出所要購買的方案。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣會套用於不同的 VM 大小

如同保留的 VM 執行個體，購買 Red Hat 方案時具有執行個體大小彈性。 這表示您的折扣甚至適用於當您部署具有不同 vCPU 計數的 VM 時。 折扣適用於軟體方案內的各種 VM 大小。

折扣金額取決於下列表格中所列的比例。 此比例會比較該群組中每個計量的相對使用量。 此比例取決於 VM vCPU。 使用比例值來計算有多少 VM 執行個體取得 Red Hat Linux 方案折扣。

例如，如果您針對具有 3 或 4 個 vCPU 的 VM，購買適用於 Red Hat Linux Enterprise Server 的方案，則該保留的比例為 2。 此折扣涵蓋以下的 Red Hat 軟體成本：

- 2 部已部署的 VM (具有 1 或 2 個 vCPU)、
- 1 部已部署的 VM (具有 3 或 4 個 vCPU)、
- 或 0.77 或大約 77% 的 VM (具有 5 個以上 vCPU)。

5 個以上 vCPU 的比例為 2.6。 因此，對於具有 5 個以上 vCPU 的 VM，其 Red Hat 保留只涵蓋軟體成本的一部分 (大約是 77%)。

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>購買前請先了解 Red Hat VM 使用量

下表會顯示您可以購買保留的軟體方案、其相關聯的使用量計量，以及每個計量的比例。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure 入口網站市集名稱：

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (最新的 lvm)

|Red Hat VM | 計量識別碼| 比例| VM 大小範例|
| -------| ------------------------| --- |--- |
|1 至 4 個 vCPU VM 授權|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1 至 4 個 vCPU VM 授權|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1 至 4 個 vCPU VM 授權|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 個以上 vCPU VM 授權|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 個以上 vCPU VM 授權|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|44 個 vCPU VM 授權|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 個 vCPU VM 授權|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP (配備 HA)

Azure 入口網站市集名稱：

|Red Hat VM | 計量識別碼 | 比例|VM 大小範例|
| ------- | --- | ------------------------| --- | --- |
|1 至 4 個 vCPU VM 授權 |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 個以上 vCPU VM 授權|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux (配備 HA)

Azure 入口網站市集名稱：

|Red Hat VM | 計量識別碼 | 比例|VM 大小範例|
| ------- |------------------------| --- | --- |
|1 至 4 個 vCPU VM 授權|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 個以上 vCPU VM 授權|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure 入口網站市集名稱：

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat VM | 計量識別碼 | 比例|VM 大小範例|
| ------- |------------------------| --- |--- |
|1 個 vCPU VM 授權|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 個 vCPU VM 授權|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 個 vCPU VM 授權|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 個 vCPU VM 授權|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 個 vCPU VM 授權|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 個 vCPU VM 授權|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 個 vCPU VM 授權|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 個 vCPU VM 授權|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 個 vCPU VM 授權|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 個 vCPU VM 授權|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 個 vCPU VM 授權|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|44 個 vCPU VM 授權|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 個 vCPU VM 授權|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 個 vCPU VM 授權|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 個 vCPU VM 授權|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 個 vCPU VM 授權|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 個 vCPU VM 授權|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure 入口網站市集名稱：

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat VM | 計量識別碼 | 比例|VM 大小範例|
| ------- |------------------------| --- |--- |
|1 個 vCPU VM 授權|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 個 vCPU VM 授權|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 個 vCPU VM 授權|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 個 vCPU VM 授權|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 個 vCPU VM 授權|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 個 vCPU VM 授權|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 個 vCPU VM 授權|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 個 vCPU VM 授權|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 個 vCPU VM 授權|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 個 vCPU VM 授權|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 個 vCPU VM 授權|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|44 個 vCPU VM 授權|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 個 vCPU VM 授權|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 個 vCPU VM 授權|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 個 vCPU VM 授權|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 個 vCPU VM 授權|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 個 vCPU VM 授權|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>後續步驟

若要深入了解保留，請參閱下列文章：

- [什麼是 Azure 的保留](save-compute-costs-reservations.md)
- [使用 Azure 保留來預付 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 的保留](manage-reserved-vm-instance.md)
- [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
