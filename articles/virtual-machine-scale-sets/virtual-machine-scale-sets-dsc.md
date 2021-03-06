---
title: Desired State Configuration と仮想マシン スケール セットの使用 | Microsoft Docs
description: 仮想マシン スケール セットと Azure DSC 拡張機能の使用
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>仮想マシン スケール セットと Azure DSC 拡張機能の使用
[仮想マシン スケール セット](virtual-machine-scale-sets-overview.md) は、[Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 拡張機能ハンドラーとともに使用できます。 仮想マシン スケール セットは、多数の仮想マシンをデプロイおよび管理する手段であり、負荷に応じて柔軟にスケールインおよびスケールアウトができます。 DSC は、オンラインで運用ソフトウェアを実行できるように VM を構成するときに使用されます。

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>仮想マシンへのデプロイと仮想マシン スケール セットへのデプロイの違い
仮想マシン スケール セットの基本テンプレートの構造は、1 つの VM の場合とは少し異なります。 具体的には、1 つの VM では、"virtualMachines" ノードに拡張機能がデプロイされます。 エントリ タイプ "extensions" があり、ここで DSC がテンプレートに追加されます

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

仮想マシン スケール セット ノードには、"VirtualMachineProfile" を備えた "properties" セクション、"extensionProfile" 属性があります。 DSC は "extensions" の下に追加します

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの動作
仮想マシン スケール セットの動作は、1 つの VM の動作と同じです。 新しく作成された VM は、DSC 拡張機能で自動的にプロビジョニングされます。 拡張機能に新しいバージョンの WMF が必要な場合、VM はオンラインになる前に再起動されます。 オンラインになると、DSC 構成 .zip がダウンロードされ、VM 上でプロビジョニングされます。 詳細については、 [Azure DSC 拡張機能の概要](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
[DSC 拡張機能用の Azure Resource Manager テンプレート](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

[DSC 拡張機能で資格情報を安全に処理する方法](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを確認してください。 

Azure DSC 拡張機能ハンドラーの詳細については、「 [Azure Desired State Configuration 拡張機能ハンドラーの概要](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 

