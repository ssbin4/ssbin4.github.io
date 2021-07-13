---
layout: post  
title: "PIGNet"  
subtitle: "PIGNet"  
categories: ml/dl
tags: paper_review

comments: true  
---  

> PIGNet 논문 리뷰입니다.

[PIGNet 논문 링크](https://arxiv.org/abs/2008.12249)

KAIST에서 2020년에 발표된 PIGNet 리뷰입니다. 여름방학 연구참여 발표 내용을 개인적으로 정리한 것입니다. 화학 관련 내용은 자세히 알지 못하므로 주로 deep learning 측면에서 논문을 정리하였습니다.

---

* __Introduction__

  신약 개발을 위해서는 약물과 단백질의 결합력을 예측하는 것, 즉 DTI(Drug-target Interaction)의 예측이 필수적이다. 하지만, 화학적으로 가능한 조합의 수가 너무 많기 때문에 brute-force 방식으로는 DTI prediction이 비효율적이며, 따라서 이를 개선하기 위해 현재까지 많은 모델들이 개발되어 왔다고 한다. 
  
  최근에는 이러한 DTI prediction에 딥러닝이 적용되고 있는데, 여기는 몇 가지 문제점이 존재한다. 먼저, 데이터의 수가 작아 모델을 일반화시키는데 어려움을 겪고 따라서 오버피팅의 문제가 자주 발생한다. 또한, 역시 데이터 수가 작기 때문에 숨겨진 구조를 파악하는 것이 아니라 데이터 자체적인 bias를 가지고 학습하게 되는 문제가 존재한다.

  본 논문에서 개발한 PIGNet에서는 딥러닝 기반의 DTI 예측 기술의 generalization ability를 높이기 위해 두 가지 전략을 사용하였다. 우선, 물리적인 성질을 반영하여 GNN을 구성하였는데, 더 구체적으로는 두 화합물의 결합력을 원자들간의 힘의 합으로서 계산하였다. 더 구체적인 것은 이후에 살펴보도록 하자. 또한, 데이터의 수가 작아 생기는 문제를 해결하기 위해 다양한 data augmentation 방법들을 사용하였다. 이 두 가지에 더해 uncertainty estimator를 도입하여 false positive를 가려낼 수 있도록 하였는데, 이에 대해서도 이후에 더 자세히 언급하도록 할 것이다.

---

* __Related Works__ 

  - 3D CNN

  이전의 딥러닝 기반의 DTI prediction 모델에서 3D CNN을 사용한 사례가 있었다. 이는 화합물의 결합 부위에서 각 원자들의 위치를 3차원 grid로 받아 CNN을 구성한 것이다. 하지만, 차원이 증가함에 따라 차원의 저주 문제가 발생할 수 있다.

  - GNN

  Graph Neural Net을 이용한 시도도 있었는데, 이는 molecular graph의 형태로 구조적인 정보를 받은 것으로, 3D CNN보다 성능은 더 좋았다고 한다. 여기서는 graph의 각 node는 화합물의 원자, edge는 원자들간의 interaction에 해당된다.

  - Physical-informed Neural Networks

  물리적인 성질을 반영하여 neural net을 구성하는 시도도 몇 가지 존재했는데, 본 논문에서는 이처럼 원자들간의 물리적, 화학적인 성질을 반영한 PIGNet을 제안한다.

---

* __Model__

  이제, PIGNet의 구체적인 구조에 대해 알아보자.

  - Architecture

  ![fig1](/assets/img/ml_dl/PIGNet/fig1.png)

  PIGNet은 protein-ligand complex의 binding free energy를 예측하는 모델이다. input으로 화합물의 구조적인 정보를 받으면, 이를 여러 층의 GNN에 통과시켜 각 원자들간의 interaction마다 update된 feature를 획득한다. 이를 다시 각각의 FC layer를 통해 4개의 energy component를 계산한다. 최종적으로, 각 원자들간의 interaction마다 4개의 energy component가 계산되면 다음과 같이 모든 원자 쌍들에 대한 이 energy component들의 합으로 전체 화합물의 binding free energy를 예측할 수 있다.

  ![eqn1](/assets/img/ml_dl/PIGNet/eqn1.png)

  ![input](/assets/img/ml_dl/PIGNet/input.png)

  input을 자세히 들여다보면, molecular graph, 즉 feature matrix H와 adjacency matrix A를 정보로 받는다. feature matrix에서 각 feature vector는 table에서와 같이 5개의 정보를 표시한다. adjacency matrix는 2개가 존재하는데, A<sup>1</sup>는 원자들 간의 공유 결합을, A<sup>2</sup>는 분자간의 interaction을 표시한다. 각 matrix의 원소 계산은 위와 같다.

  <img src="/assets/img/ml_dl/PIGNet/PIGNet-06.jpg" width="50%" height="50%" title="slide6" alt="slide6">

  GNN 부분을 더 자세히 들여다보자. GNN은 gated GAT와 interaction network 두 가지를 사용했다. 먼저, gated GAT의 경우 공유 결합 adjacency matrix인 A<sup>1</sup>을 이용하여 feature를 업데이트한다. 이 때 attention mechanism을 사용하는데 구체적인 업데이트 과정을 수식을 통해 살펴보자. 

  m<sub>i</sub>는 weight magtrix W<sub>1</sub>과 i번째 node의 feature vector의 곱이며, 첫 번째 수식에서 attention coefficient를 계산하게 된다. 이 때 i, j 두 개의 term을 더하는 이유는 e<sub>ij</sub>와 e<sub>ji</sub>가 같아지도록 하기 위함이다.

  두 번째 수식에서는 attention coefficient를 각 node에 대해 normalize시키며, 다음에서 non-linearity를 추가하여 new node feature를 계산한다.

  네 번째 수식에서 z는 previous node feature h의 중요도를 표시하는데, &#124;&#124;는 concatenation을 의미한다. 이렇게 계산한 z를 가지고 previous node feature과 new node feature의 linear combination을 통해 next node feature가 결정된다. 

  <img src="/assets/img/ml_dl/PIGNet/PIGNet-07.jpg" width="50%" height="50%" title="slide7" alt="slide7">

  다음으로, interaction network를 살펴보자. 이는 분자 간 interaction을 표시하는 adjacency matrix, A<sup>2</sup>와 gated GAT를 통해 업데이트된 feature를 가지고 계산된다. 

  먼저 W<sub>1</sub>을 가지고 m<sub>1</sub>을 계산하며, W<sub>2</sub>와 max pooling을 통해 m<sub>2</sub>를 계산한다. 다음으로 둘을 더한 다음 non-linearity를 추가하여 new node feature h'을 계산한다.

  마지막으로 GRU를 통해 new node feature와 previous node feature간의 적절한 계산을 거쳐 next node feature가 구해진다. 참고로 GRU는 RNN의 일종으로 LSTM과 유사한 역할을 수행하는데, 이전 정보와 현재 정보를 얼마만큼의 비중을 통해 새 정보를 계산할 것인가를 구한다. 

  이러한 gated GAT와 interaction network는 총 3개의 layer로 구성된다고 하는데, 이는 각 원자가 distance 3까지의 이웃 원자들의 정보까지 받아 처리하는 것임을 의미한다. 

  - Physics-informed parametrized function

  <img src="/assets/img/ml_dl/PIGNet/PIGNet-08.jpg" width="100%" height="100%" title="slide8" alt="slide8">

  이렇게 GNN을 거쳐 update된 node feature를 가지고 PIGNet에서는 4개의 energy component, 즉 VDW interaction, hydrophobic interaction, hydrogen bonding, metal-ligand interaction를 계산한다. 이들은 각 원자들간의 거리 d와 새로 계산된 d'을 가지고 구해지는데, d'은 첫 번째 식에서와 같이 계산되며 이 때 b는 FC layer에서 학습된다. 

  VDW interaction은 두 번째 식에서와 같이 계산되며, 이 때 c 또한 FC layer에서 학습된다.

  나머지 3개의 component는 네 번째 항목에서와 같이 계산식이 같으며 w는 FC layer에서 학습되고, c<sub>1</sub>와 c<sub>2</sub>는 각 energy component에 따라 다르게 정해진다. 

  마지막으로, binding free energy는 4개 energy component의 합을 rotor penalty로 나눈 값으로 계산되는데, 이는 entropy loss를 반영하기 위한 것이라고 하며, 계산식은 마지막 항목에서와 같다.

  - Loss functions

    ![fig2](/assets/img/ml_dl/PIGNet/fig2.jpg)

    loss는 위와 같이 L<sub>energy</sub>, L<sub>derivative</sub>, L<sub>augmentation</sub> 3개의 합으로 구성된다.

    - L<sub>energy</sub>

      <img src="/assets/img/ml_dl/PIGNet/L_energy.jpg" width="50%" height="50%" title="L_energy" alt="L_energy">

      PIGNet에서의 예측값과 실제 실험을 통해 관측된 참값의 MSE loss이다. 일반적인 딥러닝 모델에서의 loss를 생각하면 된다.

    - L<sub>derivative</sub>

      <img src="/assets/img/ml_dl/PIGNet/L_derivative.jpg" width="50%" height="50%" title="L_derivative" alt="L_derivative">

        실제 화합물은 가장 안정적인 binding일 때 potential이 최저가 될 것이다. 이를 loss function에 반영하기 위해 energy의 1차 미분 값이 0에 가까워지도록 한다.

        <img src="/assets/img/ml_dl/PIGNet/curve.jpg" width="50%" height="50%" title="curve" alt="curve">

        이 때, minimum이 더 뚜렷하게 나타나는 curve를 더 선호하기 위해 2차 미분값 term을 추가한다. 즉, 위 그림에서 오른쪽 curve보다는 왼쪽 curve를 더 선호하겠다는 뜻이다. 또한, 너무 과도하게 sharp한 curve는 문제가 될 수 있어 최대치를 C<sub>der2</sub>로 정한다고 한다.

    - L<sub>augmentation</sub>

      <img src="/assets/img/ml_dl/PIGNet/L_augmentation.jpg" width="50%" height="50%" title="L_augmentation" alt="L_augmentation">

        마지막으로, data augmentation에 따른 추가적인 loss를 더해준다. 본 모델에서는 3가지 data augmentation을 이용하였다.

      1. docking

         화합물의 결합 위치를 달리 하여 새로운 데이터를 만들어낸다. 이 때, 원본 데이터의 결합 위치일 때 binding free energy가 최저라고 가정하고 loss를 추가한다.

      2. random screening

         non-binding complex를 컴퓨터를 통해 만들어내어 데이터에 추가하는데, 대부분의 분자에 대해 binding free energy가 -6.8 이상일 것이라고 계산하여 이를 loss에 반영한 것이라고 한다.

      3. cross screening

           한 ligand가 target에 강하게 결합하면, 다른 target에는 결합할 가능성이 낮다는 것을 반영한 것이라고 한다.

         

  <img src="/assets/img/ml_dl/PIGNet/final_loss.jpg" width="50%" height="50%" title="final_loss" alt="final_loss">

  최종적인 loss는 위와 같이 정의되며, c<sub>derivative</sub>, c<sub>docking</sub>, ... 등은 hyperparameter이다. 

  - Benchmark Method

  마지막으로, 모델의 벤치마크에 대해 간략히 언급하고 넘어가도록 하자. 모델의 성능을 측정하기 위해 본 논문에서는 총 4개의 지표를 활용했다.

  1. scoring power: 예측된 binding affinity와 실험값과의 linear correlation
  2. ranking power: binder의 정확한 binding pose가 주어졌을 때, 특정 target protein에 대해 t원본 binder의 binding affinity의 순위를 매길 수 있는 능력
  3. docking power: docking augmentation에 의해 생성된 가짜 binding pose들로부터 원본 binding pose를 찾아낼 수 있는 능력
  4. screening power: screening augmentation에 의해 생성된 랜덤한 분자들로부터 target protein에 대한 원본 ligand를 찾아낼 수 있는 능력

---

* __Results and Discussions__

  - Model performance

    ![table1](/assets/img/ml_dl/PIGNet/table1.jpg)

    표에서 확인할 수 있듯이 다른 모델에 비해서 PIGNet이 더 좋은 성과를 보였다. 특히 docking power, screening power에서 타 모델은 낮은 성과를 보였는데, 이는 적은 data로 인한 over-fitting 문제 때문일 것이라고 설명한다.

  - Generalization ability
  
    ![table2](/assets/img/ml_dl/PIGNet/table2.jpg)
  
    data augmentation을 적용했을 때 모델의 성능이 더 좋아지는 것을 확인할 수 있다. 
  
  - Interpretation of the physically modeled outputs
  
    <img src="/assets/img/ml_dl/PIGNet/interpretation.png" width="50%" height="50%" title="interpretation" alt="interpretation">
  
    a에서 빨간색 영역은 원자가 다른 부분, 파란색 부분은 같은 부분이다. 파란색 부분의 atom-atom interaction은 거의 같은데 빨간색 부분에서는 유의미한 차이를 보이는 것을 확인할 수 있다. 즉, 전체 화합물이 아닌 개별 원자들간의 interaction을 통해 DTI를 해석할 가능성을 제시한다. 
  
    또한, b,c에서 볼 수 있듯이 모델이 화학적인 특성을 어느 정도 학습한 것을 확인할 수 있다고 한다.
  
  - Uncertainty quantification
  
    <img src="/assets/img/ml_dl/PIGNet/uncertainty.png" width="50%" height="50%" title="uncertainty" alt="uncertainty">
  
    DTI 모델에서 대부분의 positive 결과들은 false positive라고 한다. 따라서, 본 모델에서는 불확실성을 수치로 나타내고 가장 불확성이 큰 것들을 배제시켜 예측에 도움이 될 수 있게끔 하였다. 그래프에서 불확실성이 큰 것일수록 R 값이 낮은 것을 확인할 수 있다. 불확실성을 측정하기 위해서는 MC-dropout을 사용했다고 한다. 이는 training 시 뿐만 아니라 test 시에도 dropout을 적용하는 것을 말한다.

---

* __Conclusion__

  물리적인 성질을 이용하여 GNN을 구성한 것으로 DTI 예측에 이용한 모델인 PIGNet에 대해서 살펴보았다. 본 모델은 physics-informed equation과 data augmentation을 사용하여 모델의 성능을 향상시켰으며 모델의 물리적인 해석이 가능하고 불확실성을 수치화시켰다는 점에서 의미가 있다. 논문에서는 마지막으로 solvent energy를 추가하는 등의 방법으로 모델을 향상시킬 여지가 남아있다고 설명한다.

---

