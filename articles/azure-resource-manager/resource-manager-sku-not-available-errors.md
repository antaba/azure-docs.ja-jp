---
title: Azure SKU 利用不可エラー | Microsoft Docs
description: デプロイ時の SKU 利用不可エラーをトラブルシューティングする方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 490c912a6abd6570c9bc74de8b86a516a8e6f807
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU 利用不可エラーを解決する

この記事では、**SkuNotAvailable** エラーを解決する方法について説明します。

## <a name="symptom"></a>症状

リソース (通常は仮想マシン) をデプロイするときに、次のエラー コードとエラー メッセージが表示されます。

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

このエラーは、選択したリソースの SKU (VM サイズなど) が、選択した地域で利用できない場合に発生します。

## <a name="solution-1---powershell"></a>解決策 1 - PowerShell

リージョンで利用可能な SKU を特定するには、[Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) コマンドを使用します。 結果を場所でフィルター処理します。 このコマンドには、PowerShell の最新バージョンが必要です。

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations -icontains "southcentralus"}
```

結果には、目的の場所で利用可能な SKU の一覧とその SKU に関する制約事項が含まれます。

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

## <a name="solution-2---azure-cli"></a>解決策 2 - Azure CLI

リージョンで利用可能な SKU を特定するには、`az vm list-skus` コマンドを使用します。 その後、`grep` または同様のユーティリティを使用して出力をフィルター処理できます。

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

## <a name="solution-3---azure-portal"></a>解決策 3 - Azure Portal

リージョンで利用可能な SKU を特定するには、[ポータル](https://portal.azure.com)を使用します。 ポータルにログインし、インターフェイスからリソースを追加します。 値を設定するときに、そのリソースで利用可能な SKU が表示されます。 デプロイを完了する必要はありません。

![利用可能な SKU](./media/resource-manager-sku-not-available-errors/view-sku.png)

## <a name="solution-4---rest"></a>解決策 4 - REST

リージョンで利用可能な SKU を特定するには、仮想マシンの REST API を使用します。 次の要求を送信します。

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

次の形式で利用可能な SKU とリージョンを返します。

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

UI に表示されたリージョン (またはビジネス ニーズを満たすその他のリージョン) に適切な SKU が見つからない場合は、Azure サポートに [SKU 要求](https://aka.ms/skurestriction)を送信してください。
