# Chart-based Dense Pose Estimation for Humans and Animals

## <a name="Overview"></a> Overview

人間や動物のためのチャートベースのDensePose推定におけるチャートベースの方法の目的は、物体をチャートに分割し、各ピクセルの対応するチャートインデックス I と局所チャート座標 (U, V) を推定することによって、画像ピクセルと3Dオブジェクトメッシュの間に密な対応関係を確立することです。

<div align="center">
  <img src="https://dl.fbaipublicfiles.com/densepose/web/densepose_teaser_compressed_25.gif" width="700px" />
</div>

人間のDensePose推定に使用されるチャートは図1に示されています。人体は24の部位に分割され、それぞれの部位は U と V 座標によってパラメータ化され、各座標は [0, 1] の値を取ります。

<div align="center">
  <img src="https://dl.fbaipublicfiles.com/densepose/web/coords.png" width="400px" />
</div>
<p class="image-caption"><b>Figure 1.</b> Partitioning and parametrization of human body surface.</p>

パイプラインは、図2に概要が示されている特徴ピラミッドネットワークを使用したFaster R-CNNを使用します。各検出されたオブジェクトについて、モデルは、その粗いセグメンテーション S（前景/背景または背景+14の定義済みのボディパーツを持つ2または15チャンネル）、細分割 I（背景+24の定義済みのボディパーツを持つ25チャンネル）とローカルチャート座標 U と V を予測します。

<div align="center">
  <img src="https://dl.fbaipublicfiles.com/densepose/web/densepose_pipeline_iuv.png" width="500px" />
</div>
<p class="image-caption"><b>Figure 2.</b> DensePose chart-based architecture based on Faster R-CNN with Feature Pyramid Network (FPN).</p>

### <a name="Bootstrap"></a> Bootstrapping Chart-Based Models

[Sanakoyeu et al.、2020]は、チンパンジーなどの近接動物クラスに対して人間でトレーニングされたDensePoseモデルを転送するパイプラインを紹介しました。トレーニングは、2つのステージで行われます。最初に、ソースドメイン（完全なDensePose注釈 S、I、U、および V を持つ人間）とサポートドメイン（セグメンテーション注釈のみを持つ動物）からデータを取得してマスターモデルがトレーニングされます。選択された動物クラスは、ターゲットドメインの結果の品質を保証するためにカテゴリフィルタを介して選択されます。トレーニングはクラス非依存の方法で行われ、すべての選択されたカテゴリが単一のカテゴリ（人間）にマップされます。

次に、スチューデントモデルが、マスターモデルを適用して得られたターゲットドメインのデータ、高信頼度の検出を選択し、結果をサンプリングして、ソースおよびサポートドメインからのデータと組み合わせてトレーニングされます。

<div align="center">
  <img src="https://dl.fbaipublicfiles.com/densepose/web/densepose_pipeline_bootstrap_iuv.png" width="1000px" />
</div>
<p class="image-caption"><b>Figure 3.</b> Domain adaptation: <i>master</i> model is trained on data from source and
supporting domains to produce predictions in target domain; <i>student</i> model combines data from source and
supporting domains, as well as sampled predictions from the master model on target domain to improve
target domain predictions quality.</p>

事前にトレーニングされたマスターおよびスチューデントモデルの例は、Model Zooで入手できます。

### Datasets

チャートベースのモデルのトレーニングと検証に使用されるデータセットの詳細については、DensePose Datasetsページを参照してください。

## <a name="ModelZoo"></a> Model Zoo and Baselines

### Legacy Models

Güler et al, 2018]のスケジュールを用いてトレーニングしたベースライン(https://arxiv.org/pdf/1802.00434.pdf)

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_rcnn_R_50_FPN_s1x_legacy -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_s1x_legacy.yaml">R_50_FPN_s1x_legacy</a></td>
<td align="center">s1x</td>
<td align="center">0.307</td>
<td align="center">0.051</td>
<td align="center">3.2</td>
<td align="center">58.1</td>
<td align="center">58.2</td>
<td align="center">52.1</td>
<td align="center">54.9</td>
<td align="center">164832157</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_s1x_legacy/164832157/model_final_d366fa.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_s1x_legacy/164832157/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_s1x_legacy -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_s1x_legacy.yaml">R_101_FPN_s1x_legacy</a></td>
<td align="center">s1x</td>
<td align="center">0.390</td>
<td align="center">0.063</td>
<td align="center">4.3</td>
<td align="center">59.5</td>
<td align="center">59.3</td>
<td align="center">53.2</td>
<td align="center">56.0</td>
<td align="center">164832182</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_s1x_legacy/164832182/model_final_10af0e.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_s1x_legacy/164832182/metrics.json">metrics</a></td>
</tr>
</tbody></table>

### Improved Baselines, Original Fully Convolutional Head

これらのモデルは、[Kirillov et al, 2019](https://arxiv.org/abs/1901.02446)の改良された学習スケジュールとPanoptic FPN headを使用しています。

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_rcnn_R_50_FPN_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_s1x.yaml">R_50_FPN_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.359</td>
<td align="center">0.066</td>
<td align="center">4.5</td>
<td align="center">61.2</td>
<td align="center">67.2</td>
<td align="center">63.7</td>
<td align="center">65.3</td>
<td align="center">165712039</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_s1x/165712039/model_final_162be9.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_s1x/165712039/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_s1x.yaml">R_101_FPN_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.428</td>
<td align="center">0.079</td>
<td align="center">5.8</td>
<td align="center">62.3</td>
<td align="center">67.8</td>
<td align="center">64.5</td>
<td align="center">66.2</td>
<td align="center">165712084</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_s1x/165712084/model_final_c6ab63.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_s1x/165712084/metrics.json">metrics</a></td>
</tr>
</tbody></table>

### <a name="ModelZooDeepLabV3"> Improved Baselines, DeepLabV3 Head

これらのモデルは、改良された学習スケジュール、[Kirillov et al, 2019](https://arxiv.org/abs/1901.02446)のPanoptic FPN head、[Chen et al, 2017](https://arxiv.org/abs/1706.05587)のDeepLabV3 headを用いています。

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_rcnn_R_50_FPN_DL_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_DL_s1x.yaml">R_50_FPN_DL_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.392</td>
<td align="center">0.070</td>
<td align="center">6.7</td>
<td align="center">61.1</td>
<td align="center">68.3</td>
<td align="center">65.6</td>
<td align="center">66.7</td>
<td align="center">165712097</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_s1x/165712097/model_final_0ed407.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_s1x/165712097/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_DL_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_DL_s1x.yaml">R_101_FPN_DL_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.478</td>
<td align="center">0.083</td>
<td align="center">7.0</td>
<td align="center">62.3</td>
<td align="center">68.7</td>
<td align="center">66.3</td>
<td align="center">67.6</td>
<td align="center">165712116</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_s1x/165712116/model_final_844d15.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_s1x/165712116/metrics.json">metrics</a></td>
</tr>
</tbody></table>

### <a name="ModelZooConfidence"> Baselines with Confidence Estimation

これらのモデルは、[Neverova et al., 2019](https://papers.nips.cc/paper/8378-correlated-uncertainty-for-learning-dense-correspondences-from-noisy-labels)の線に沿って、回帰されたUVクードリネートの信頼度の追加推定を実行します。

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_rcnn_R_50_FPN_WC1_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_WC1_s1x.yaml">R_50_FPN_WC1_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.353</td>
<td align="center">0.064</td>
<td align="center">4.6</td>
<td align="center">60.5</td>
<td align="center">67.0</td>
<td align="center">64.2</td>
<td align="center">65.4</td>
<td align="center">173862049</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC1_s1x/173862049/model_final_289019.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC1_s1x/173862049/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_WC2_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_WC2_s1x.yaml">R_50_FPN_WC2_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.364</td>
<td align="center">0.066</td>
<td align="center">4.8</td>
<td align="center">60.7</td>
<td align="center">66.9</td>
<td align="center">64.2</td>
<td align="center">65.7</td>
<td align="center">173861455</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC2_s1x/173861455/model_final_3abe14.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC2_s1x/173861455/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_DL_WC1_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_DL_WC1_s1x.yaml">R_50_FPN_DL_WC1_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.397</td>
<td align="center">0.068</td>
<td align="center">6.7</td>
<td align="center">61.1</td>
<td align="center">68.1</td>
<td align="center">65.8</td>
<td align="center">67.0</td>
<td align="center">173067973</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC1_s1x/173067973/model_final_b1e525.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC1_s1x/173067973/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_DL_WC2_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_DL_WC2_s1x.yaml">R_50_FPN_DL_WC2_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.410</td>
<td align="center">0.070</td>
<td align="center">6.8</td>
<td align="center">60.8</td>
<td align="center">67.9</td>
<td align="center">65.6</td>
<td align="center">66.7</td>
<td align="center">173859335</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC2_s1x/173859335/model_final_60fed4.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC2_s1x/173859335/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_WC1_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_WC1_s1x.yaml">R_101_FPN_WC1_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.435</td>
<td align="center">0.076</td>
<td align="center">5.7</td>
<td align="center">62.5</td>
<td align="center">67.6</td>
<td align="center">64.9</td>
<td align="center">66.3</td>
<td align="center">171402969</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC1_s1x/171402969/model_final_9e47f0.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC1_s1x/171402969/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_WC2_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_WC2_s1x.yaml">R_101_FPN_WC2_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.450</td>
<td align="center">0.078</td>
<td align="center">5.7</td>
<td align="center">62.3</td>
<td align="center">67.6</td>
<td align="center">64.8</td>
<td align="center">66.4</td>
<td align="center">173860702</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC2_s1x/173860702/model_final_5ea023.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC2_s1x/173860702/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_DL_WC1_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_DL_WC1_s1x.yaml">R_101_FPN_DL_WC1_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.479</td>
<td align="center">0.081</td>
<td align="center">7.9</td>
<td align="center">62.0</td>
<td align="center">68.4</td>
<td align="center">66.2</td>
<td align="center">67.2</td>
<td align="center">173858525</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC1_s1x/173858525/model_final_f359f3.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC1_s1x/173858525/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_DL_WC2_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_DL_WC2_s1x.yaml">R_101_FPN_DL_WC2_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.491</td>
<td align="center">0.082</td>
<td align="center">7.6</td>
<td align="center">61.7</td>
<td align="center">68.3</td>
<td align="center">65.9</td>
<td align="center">67.2</td>
<td align="center">173294801</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC2_s1x/173294801/model_final_6e1ed1.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC2_s1x/173294801/metrics.json">metrics</a></td>
</tr>
</tbody></table>

頭文字をとったもの：

WC1`: `U`と`V`の信頼度推定モデルタイプ1付き

WC2`: `U` と `V` の信頼度推定モデルタイプ 2 を使用する。

### <a name="ModelZooMaskConfidence"> マスクの信頼度推定を行うベースライン

UVクードリネートを回帰した信頼度の推定を行うモデル
と、粗いセグメンテーションと細かいセグメンテーションに関連するコンフィデンス（信頼度）である、
詳細は[Sanakoyeu et al., 2020](https://arxiv.org/pdf/2003.00080.pdf)をご参照ください。

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_rcnn_R_50_FPN_WC1M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_WC1M_s1x.yaml">R_50_FPN_WC1M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.381</td>
<td align="center">0.066</td>
<td align="center">4.8</td>
<td align="center">60.6</td>
<td align="center">66.7</td>
<td align="center">64.0</td>
<td align="center">65.4</td>
<td align="center">217144516</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC1M_s1x/217144516/model_final_48a9d9.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC1M_s1x/217144516/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_WC2M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_WC2M_s1x.yaml">R_50_FPN_WC2M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.342</td>
<td align="center">0.068</td>
<td align="center">5.0</td>
<td align="center">60.7</td>
<td align="center">66.9</td>
<td align="center">64.2</td>
<td align="center">65.5</td>
<td align="center">216245640</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC2M_s1x/216245640/model_final_d79ada.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_WC2M_s1x/216245640/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_DL_WC1M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_DL_WC1M_s1x.yaml">R_50_FPN_DL_WC1M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.371</td>
<td align="center">0.068</td>
<td align="center">6.0</td>
<td align="center">60.7</td>
<td align="center">68.0</td>
<td align="center">65.2</td>
<td align="center">66.7</td>
<td align="center">216245703</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC1M_s1x/216245703/model_final_61971e.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC1M_s1x/216245703/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_50_FPN_DL_WC2M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_50_FPN_DL_WC2M_s1x.yaml">R_50_FPN_DL_WC2M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.385</td>
<td align="center">0.071</td>
<td align="center">6.1</td>
<td align="center">60.8</td>
<td align="center">68.1</td>
<td align="center">65.0</td>
<td align="center">66.4</td>
<td align="center">216245758</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC2M_s1x/216245758/model_final_7bfb43.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_50_FPN_DL_WC2M_s1x/216245758/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_WC1M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_WC1M_s1x.yaml">R_101_FPN_WC1M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.423</td>
<td align="center">0.079</td>
<td align="center">5.9</td>
<td align="center">62.0</td>
<td align="center">67.3</td>
<td align="center">64.8</td>
<td align="center">66.0</td>
<td align="center">216453687</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC1M_s1x/216453687/model_final_0a7287.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC1M_s1x/216453687/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_WC2M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_WC2M_s1x.yaml">R_101_FPN_WC2M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.436</td>
<td align="center">0.080</td>
<td align="center">5.9</td>
<td align="center">62.5</td>
<td align="center">67.4</td>
<td align="center">64.5</td>
<td align="center">66.0</td>
<td align="center">216245682</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC2M_s1x/216245682/model_final_e354d9.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_WC2M_s1x/216245682/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_DL_WC1M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_DL_WC1M_s1x.yaml">R_101_FPN_DL_WC1M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.453</td>
<td align="center">0.079</td>
<td align="center">6.8</td>
<td align="center">62.0</td>
<td align="center">68.1</td>
<td align="center">66.4</td>
<td align="center">67.1</td>
<td align="center">216245771</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC1M_s1x/216245771/model_final_0ebeb3.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC1M_s1x/216245771/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_rcnn_R_101_FPN_DL_WC2M_s1x -->
<tr><td align="left"><a href="../configs/densepose_rcnn_R_101_FPN_DL_WC2M_s1x.yaml">R_101_FPN_DL_WC2M_s1x</a></td>
<td align="center">s1x</td>
<td align="center">0.464</td>
<td align="center">0.080</td>
<td align="center">6.9</td>
<td align="center">61.9</td>
<td align="center">68.2</td>
<td align="center">66.1</td>
<td align="center">67.1</td>
<td align="center">216245790</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC2M_s1x/216245790/model_final_de6e7a.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/densepose_rcnn_R_101_FPN_DL_WC2M_s1x/216245790/metrics.json">metrics</a></td>
</tr>
</tbody></table>

略語:

WC1M: U および V の信頼度推定モデルタイプ1とマスク信頼度推定を含む

WC2M: U および V の信頼度推定モデルタイプ2とマスク信頼度推定を含む

<a name="ModelZooBootstrap"></a> ブートストラップ基準
チンパンジーをターゲットカテゴリとして使用してブートストラップパイプラインを使用してトレーニングされたマスターモデルとスチューデントモデルは、Sanakoyeu et al., 2020 およびBootstrapping Pipelineを参照してください。評価はDensePose Chimpsデータセットで実行されます。

<table><tbody>
<!-- START TABLE -->
<!-- TABLE HEADER -->
<th valign="bottom">Name</th>
<th valign="bottom">lr<br/>sched</th>
<th valign="bottom">train<br/>time<br/>(s/iter)</th>
<th valign="bottom">inference<br/>time<br/>(s/im)</th>
<th valign="bottom">train<br/>mem<br/>(GB)</th>
<th valign="bottom">box<br/>AP</th>
<th valign="bottom">segm<br/>AP</th>
<th valign="bottom">dp. APex<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPS</th>
<th valign="bottom">dp. AP<br/>GPSm</th>
<th valign="bottom">model id</th>
<th valign="bottom">download</th>
<!-- TABLE BODY -->
<!-- ROW: densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA -->
<tr><td align="left"><a href="../configs/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA.yaml">R_50_FPN_DL_WC1M_3x_Atop10P_CA</a></td>
<td align="center">3x</td>
<td align="center">0.522</td>
<td align="center">0.073</td>
<td align="center">9.7</td>
<td align="center">61.3</td>
<td align="center">59.1</td>
<td align="center">36.2</td>
<td align="center">20.0</td>
<td align="center">30.2</td>
<td align="center">217578784</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA/217578784/model_final_9fe1cc.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10
P_CA/217578784/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uniform -->
<tr><td align="left"><a href="../configs/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uniform.yaml">R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uniform</a></td>
<td align="center">3x</td>
<td align="center">1.939</td>
<td align="center">0.072</td>
<td align="center">10.1</td>
<td align="center">60.9</td>
<td align="center">58.5</td>
<td align="center">37.2</td>
<td align="center">21.5</td>
<td align="center">31.0</td>
<td align="center">256453729</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uniform/256453729/model_final_241ff5.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uniform/256453729/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uv -->
<tr><td align="left"><a href="../configs/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uv.yaml">R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uv</a></td>
<td align="center">3x</td>
<td align="center">1.985</td>
<td align="center">0.072</td>
<td align="center">9.6</td>
<td align="center">61.4</td>
<td align="center">58.9</td>
<td align="center">38.3</td>
<td align="center">22.2</td>
<td align="center">32.1</td>
<td align="center">256452095</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uv/256452095/model_final_d689e2.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_uv/256452095/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_finesegm -->
<tr><td align="left"><a href="../configs/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_finesegm.yaml">R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_finesegm</a></td>
<td align="center">3x</td>
<td align="center">2.047</td>
<td align="center">0.072</td>
<td align="center">10.3</td>
<td align="center">60.9</td>
<td align="center">58.5</td>
<td align="center">36.7</td>
<td align="center">20.7</td>
<td align="center">30.7</td>
<td align="center">256452819</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_finesegm/256452819/model_final_cb4ac6.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_finesegm/256452819/metrics.json">metrics</a></td>
</tr>
<!-- ROW: densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_coarsesegm -->
<tr><td align="left"><a href="../configs/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_coarsesegm.yaml">R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_coarsesegm</a></td>
<td align="center">3x</td>
<td align="center">1.830</td>
<td align="center">0.070</td>
<td align="center">9.6</td>
<td align="center">61.3</td>
<td align="center">59.2</td>
<td align="center">37.9</td>
<td align="center">21.5</td>
<td align="center">31.6</td>
<td align="center">256455697</td>
<td align="center"><a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_coarsesegm/256455697/model_final_a6a4bf.pkl">model</a>&nbsp;|&nbsp;<a href="https://dl.fbaipublicfiles.com/densepose/evolution/densepose_R_50_FPN_DL_WC1M_3x_Atop10P_CA_B_coarsesegm/256455697/metrics.json">metrics</a></td>
</tr>
</tbody></table>

Acronyms:

WC1M`: `U`と`V`の信頼度推定モデルタイプ1とマスク信頼度推定を使用する。

Atop10P`：最適な10個のカテゴリーから選ばれたヒトと動物がトレーニングに使用される

CA`: クラスにとらわれないトレーニング。アノテーションされたインスタンスはすべて1つのカテゴリにマッピングされる。

B_<...>`：指定した結果のサンプリング戦略でブートストラップによるスケジュールを組む

ノートです：

リラックスした`dp. APex GPS` メトリックは [Sanakoyeu et al., 2020](https://arxiv.org/pdf/2003.00080.pdf) で DensePose の結果を評価するために使用されました。このメトリックは、評価プロトコルで使用される標準的なものに加えて、閾値0.2、0.3、0.4でのマッチを考慮します。最小の閾値は `DENSEPOSE_EVALUATION.MIN_IOU_THRESHOLD` 設定オプションで制御します。

### License

All models available for download are licensed under the
[Creative Commons Attribution-ShareAlike 3.0 license](https://creativecommons.org/licenses/by-sa/3.0/)

## <a name="References"></a> References

If you use chart-based DensePose methods, please take the references from the following
BibTeX entries:

DensePose bootstrapping pipeline:
```
@InProceedings{Sanakoyeu2020TransferringDensePose,
    title = {Transferring Dense Pose to Proximal Animal Classes},
    author = {Artsiom Sanakoyeu and Vasil Khalidov and Maureen S. McCarthy and Andrea Vedaldi and Natalia Neverova},
    journal = {The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
    year = {2020},
}
```

DensePose with confidence estimation:
```
@InProceedings{Neverova2019DensePoseConfidences,
    title = {Correlated Uncertainty for Learning Dense Correspondences from Noisy Labels},
    author = {Neverova, Natalia and Novotny, David and Vedaldi, Andrea},
    journal = {Advances in Neural Information Processing Systems},
    year = {2019},
}
```

Original DensePose:
```
@InProceedings{Guler2018DensePose,
  title={DensePose: Dense Human Pose Estimation In The Wild},
  author={R\{i}za Alp G\"uler, Natalia Neverova, Iasonas Kokkinos},
  journal={The IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
  year={2018}
}
```
