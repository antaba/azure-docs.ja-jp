---
title: Azure Machine Learning でアルゴリズムを最適化する | Microsoft Docs
description: Azure Machine Learning でアルゴリズムに最適なパラメーター セットを選択する方法について説明します。
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 3e4423f0a92b08ff7fa7dd8edf430f27ff2d464e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Azure Machine Learning でアルゴリズムを最適化するためにパラメーターを選ぶ
このトピックでは、Azure Machine Learning でアルゴリズムの正しいハイパーパラメーター セットを選択する方法について説明します。 ほとんどの機械学習アルゴリズムに、設定が必要なパラメーターがあります。 モデルをトレーニングするときは、これらのパラメーターに値を提供する必要があります。 トレーニングしたモデルの有効性は、選んだモデル パラメーターによって決まります。 パラメーターの最適なセットを見つけるプロセスのことを、*モデルの選択*といいます。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

モデルの選択を行うにはさまざまな方法があります。 Machine Learning で最も広く使われているモデルの選択方法の 1 つはクロス検証です。これは、Azure Machine Learning の既定のモデル選択メカニズムになっています。 Azure Machine Learning は R と Python の両方をサポートしているため、R または Python を使って独自のモデル選択メカニズムをいつでも実装できます。

最適なパラメーター セットを見つけるプロセスには、次の 4 つの手順があります。

1. **パラメーター空間を定義する**: アルゴリズムについて、検討の対象にする正確なパラメーター値をまず決めます。
2. **クロス検証の設定を定義する**: データセットのクロス検証のフォールドを選ぶ方法を決定します。
3. **メトリックを定義する**: その後、パラメーターの最適なセットを判別するために使うメトリックを決めます。たとえば、確度、二乗平均平方根の誤差、精度、再現率、f スコアなどがあります。
4. **トレーニング、評価、および比較を行う**: パラメーター値の一意の組み合わせごとに、ユーザーが定義した誤差メトリックを使い、それに基づいて、クロス検証を実行します。 評価と比較が済むと、最適なモデルを選ぶことができます。

次の図は、Azure Machine Learning でこのプロセスを実施する方法を示しています。

![最適なパラメーター セットを検索します](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>パラメーター空間を定義する
パラメーターのセットは、モデルの初期化ステップで定義できます。 すべての Machine Learning アルゴリズムのパラメーター ウィンドウには 2 つのトレーナー モードがあります。"*1 つのパラメーター*" と "*パラメーター範囲*" です。 パラメーター範囲モードを選びます。 パラメーター範囲モードでは、各パラメーターに複数の値を入力できます。 テキスト ボックスには、コンマ区切りの値を入力できます。

![2 クラスのブースト デシジョン ツリー、1 つのパラメーター](./media/algorithm-parameters-optimize/fig2.png)

 別の方法として、**[範囲ビルダーを使用]** で生成されるグリッドの最大ポイントと最小ポイント、および生成するポイントの合計数を定義できます。 既定では、パラメーターの値は線形スケールで生成されます。 しかし、**[対数スケール]** チェック ボックスをオンにすると、値が対数スケールで生成されます (つまり、隣接するポイントの差ではなく比率が一定になります)。 整数パラメーターの場合、ハイフンを使って範囲を定義できます。 たとえば、"1-10" は 1 ～ 10 (1 と 10 を含む) のすべての整数を意味するパラメーターのセットになります。 混合モードもサポートされています。 たとえば、パラメーター セット "1-10, 20, 50" は、整数 1 ～ 10、20、50 を含みます。

![2 クラスのブースト デシジョン ツリー、パラメーター範囲](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>クロス検証のフォールドを定義する
[パーティションとサンプル][partition-and-sample] モジュールを使用すると、データにフォールドをランダムに割り当てることができます。 次のモジュールのサンプル構成では、5 つのフォールドを定義し、サンプル インスタンスにフォールド番号をランダムに割り当てています。

![パーティションとサンプル](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>メトリックを定義する
[調整モデル ハイパーパラメーター][tune-model-hyperparameters] モジュールは、特定のアルゴリズムおよびデータセットに対してパラメーターの最適なセットを経験的に選択するためのサポートを提供します。 モデルのトレーニングに関する他の情報に加えて、このモジュールの **[プロパティ]** ウィンドウには、最適なパラメーター セットを決定するためのメトリックが含まれています。 また、分類アルゴリズムと回帰アルゴリズムに関する 2 つの異なるドロップダウン リスト ボックスがあります。 検討しているアルゴリズムが分類アルゴリズムの場合は回帰のメトリックが無視され、その逆も同じです。 この例では、メトリックは **[確度]** です。   

![パラメーターをスイープする](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>トレーニング、評価、および比較を行う
同じ[調整モデル ハイパーパラメーター][tune-model-hyperparameters] モジュールで、パラメーターのセットに対応するすべてのモデルをトレーニングし、さまざまなメトリックを評価した後、選ばれたメトリックに基づいて最適なトレーニング済みのモデルを作成します。 このモジュールには、次の 2 つの必須の入力があります。

* トレーニングを受けていない学習モデル
* データセット

このモジュールには、省略可能なデータセット入力もあります。 必須のデータセット入力に対してデータセットをフォールド情報と一緒に接続します。 データセットにフォールド情報を割り当てない場合は、既定で 10 フォールドのクロス検証が自動的に実行されます。 フォールドを割り当てずに、オプションのデータセット ポートに検証データセットを提供した場合は、トレーニング テスト モードが選択されます。このモードでは、最初のデータセットがパラメーターの組み合わせごとにモデルのトレーニングに使用されます。

![ブースト デシジョン ツリー分類子](./media/algorithm-parameters-optimize/fig6a.png)

次に検証データセットでモデルの評価が実行されます。 モジュールの左側の出力ポートは、パラメーター値の関数としての異なるメトリックを示しています。 右側の出力ポートからは、選択されたメトリック (ここでは**確度**) に従う最適なモデルに対応するトレーニング済みのモデルを使用できます。  

![検証データセット](./media/algorithm-parameters-optimize/fig6b.png)

右側の出力ポートを視覚化すると、選択された正確なパラメーターを確認できます。 このモデルは、トレーニング済みのモデルとして保存した後、テスト セットのスコア付けや、運用可能な Web サービスとして使用できます。

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
