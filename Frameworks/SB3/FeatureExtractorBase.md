1. 역할 및 필요성
Stable-Baselines3는 이미지(Box space)나 사전(Dict space) 형태의 관측값을 처리하기 위한 기본적인 특징 추출기(CombinedExtractor, NatureCNN)를 내장하고 있다. 하지만, 환경이 더 복잡하거나 특별한 전처리가 필요할 때 기본 추출기로는 한계가 있다.
FeatureExtractorBase는 이러한 상황에서 사용자가 자신만의 신경망 구조를 만들어 관측값을 처리할 수 있게 해주는 **"설계도"**와 같은 역할을 한다.
 * 주요 목적:
   * 맞춤형 신경망 구조 설계: 환경에 특화된 CNN, MLP 또는 둘의 조합을 자유롭게 구성하여 특징 추출 성능을 극대화한다.
   * 복합적인 관측값 처리: 여러 종류의 데이터가 섞인 관측값(예: 이미지 + 로봇 관절 각도 + 속도)을 하나의 일관된 벡터로 통합 처리한다.
   * 전처리 통합: 스케일링, 정규화 등 데이터 전처리 과정을 특징 추출 신경망 내에 포함시켜 모델을 일관성 있게 관리한다.
2. 주요 구성 요소
torch.nn.Module을 상속받는 FeatureExtractorBase를 상속하여 새로운 클래스를 만들 때는 보통 다음 두 가지 요소를 구현해야 한다.
 * __init__(self, observation_space, features_dim):
   * 특징 추출에 사용할 신경망 레이어(예: nn.Conv2d, nn.Linear, nn.ReLU)를 정의하는 곳.
   * observation_space: 환경의 관측 공간에 대한 정보. (입력 데이터의 형태를 알 수 있음)
   * features_dim: 이 추출기가 최종적으로 출력할 특징 벡터의 차원(크기). 사용자가 직접 지정한다.
 * forward(self, observations):
   * 실제 관측값(observations)이 입력으로 들어왔을 때, __init__에서 정의한 레이어들을 통과시켜 최종 특징 벡터를 계산하고 반환하는 로직을 구현하는 곳.
3. 구체적 예시
예시 1: 간단한 Custom CNN 정의
기본 NatureCNN보다 더 얕거나 다른 구조의 CNN을 사용하고 싶을 때.
 * 상황: 2D 게임 화면(이미지)을 입력으로 받지만, 기본 CNN 구조가 너무 복잡하여 더 가벼운 모델을 만들고 싶다.
 * 구현:
   * __init__: 2개의 합성곱 레이어와 1개의 완전 연결(Linear) 레이어를 정의.
   * forward: 이미지를 합성곱 레이어에 통과시킨 후, Flatten하고 마지막 Linear 레이어를 거쳐 64차원의 벡터를 출력.

```python
import gymnasium as gym
import torch.nn as nn
from stable_baselines3.common.torch_layers import BaseFeaturesExtractor

class CustomCNN(BaseFeaturesExtractor):
    """
    :param observation_space: (gym.Space)
    :param features_dim: (int) Number of features extracted.
        This corresponds to the number of unit for the last layer.
    """
    def __init__(self, observation_space: gym.spaces.Box, features_dim: int = 64):
        super().__init__(observation_space, features_dim)
        # We assume CxHxW images (channels first)
        n_input_channels = observation_space.shape[0]
        self.cnn = nn.Sequential(
            nn.Conv2d(n_input_channels, 16, kernel_size=8, stride=4, padding=0),
            nn.ReLU(),
            nn.Conv2d(16, 32, kernel_size=4, stride=2, padding=0),
            nn.ReLU(),
            nn.Flatten(),
        )

        # Compute shape by doing one forward pass
        with th.no_grad():
            n_flatten = self.cnn(
                th.as_tensor(observation_space.sample()[None]).float()
            ).shape[1]

        self.linear = nn.Sequential(nn.Linear(n_flatten, features_dim), nn.ReLU())

    def forward(self, observations: th.Tensor) -> th.Tensor:
        return self.linear(self.cnn(observations))

```

예시 2: 이미지와 벡터 데이터를 함께 처리 (Dict Observation)
 * 상황: 자율주행 환경에서 전방 카메라 이미지(image)와 현재 차량의 속도(speed)라는 두 가지 정보를 동시에 관측값으로 사용한다.
 * 구현:
   * __init__:
     * 이미지를 처리할 CNN 파트(cnn_net)를 정의.
     * 속도(1차원 벡터)를 처리할 MLP 파트(mlp_net)를 정의.
     * 두 네트워크의 출력 벡터를 합쳤을 때의 크기를 계산하고, 이 합쳐진 벡터를 최종 특징 차원으로 매핑하는 결합 레이어(combined_net)를 정의.
   * forward:
     * 입력 observations (사전 형태)에서 image와 speed 키로 데이터를 각각 꺼낸다.
     * 이미지는 cnn_net에, 속도는 mlp_net에 통과시킨다.
     * 두 결과 벡터를 torch.cat으로 연결(concatenate)한 후 combined_net을 통과시켜 최종 특징 벡터를 반환.

```python
import gymnasium as gym
import torch as th
import torch.nn as nn
from stable_baselines3.common.torch_layers import BaseFeaturesExtractor

class CustomCombinedExtractor(BaseFeaturesExtractor):
    def __init__(self, observation_space: gym.spaces.Dict, features_dim: int = 128):
        super().__init__(observation_space, features_dim)

        # 이미지 처리를 위한 CNN 정의
        image_space = observation_space.spaces["image"]
        n_input_channels = image_space.shape[0]
        self.cnn = nn.Sequential(
            nn.Conv2d(n_input_channels, 32, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(2),
            nn.Conv2d(32, 32, kernel_size=3, padding=1),
            nn.ReLU(),
            nn.MaxPool2d(2),
            nn.Flatten(),
        )
        # CNN 출력 크기 계산
        with th.no_grad():
            sample_image = th.as_tensor(image_space.sample()[None]).float()
            cnn_output_dim = self.cnn(sample_image).shape[1]

        # 속도(벡터) 처리를 위한 MLP 정의
        vector_space = observation_space.spaces["speed"]
        vector_input_dim = vector_space.shape[0]
        self.mlp = nn.Sequential(
            nn.Linear(vector_input_dim, 16),
            nn.ReLU(),
        )
        mlp_output_dim = 16

        # CNN과 MLP의 출력을 합쳐서 최종 feature_dim으로 변환
        self.combined_head = nn.Linear(cnn_output_dim + mlp_output_dim, features_dim)

    def forward(self, observations: th.Tensor) -> th.Tensor:
        # 이미지와 속도 데이터를 분리
        image_obs = observations["image"]
        vector_obs = observations["speed"]

        # 각각의 네트워크 통과
        cnn_features = self.cnn(image_obs)
        mlp_features = self.mlp(vector_obs)

        # 두 특징 벡터를 연결
        combined_features = th.cat((cnn_features, mlp_features), dim=1)
        
        # 최종 특징 벡터 반환
        return self.combined_head(combined_features)
```
