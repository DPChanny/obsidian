Stable Baselines3 (SB3)의 EvalCallback은 강화학습 모델의 훈련 과정을 객관적으로 평가하고 최상의 모델을 저장하기 위한 핵심 도구입니다. 훈련에 사용되는 환경과는 별개의 평가용 환경(evaluation environment)에서 주기적으로 에이전트의 성능을 측정하여 과적합을 방지하고, 가장 성능이 좋은 시점의 모델을 자동으로 저장해 줍니다.
EvalCallback의 핵심 기능 및 주요 파라미터
EvalCallback은 모델의 learn() 메소드에 callback 인자로 전달되어 사용됩니다. 훈련 중 특정 타임스텝 간격마다 평가를 수행하고, 그 결과를 바탕으로 작업을 수행합니다.
주요 파라미터
 * eval_env: 모델의 성능을 평가할 환경입니다. 훈련 환경과 분리된 별도의 환경을 사용하는 것이 일반적입니다. gym.make() 또는 make_vec_env()로 생성할 수 있습니다.
 * n_eval_episodes (기본값: 5): 각 평가마다 실행할 에피소드의 수입니다.
 * eval_freq (기본값: 10000): 평가를 수행할 env.step() 호출 간격입니다. 예를 들어 eval_freq=5000이면, 훈련 환경에서 5000 타임스텝마다 평가를 진행합니다.
 * log_path: 평가 결과를 저장할 경로입니다. evaluations.npz 파일이 이 경로에 생성되어 각 평가 시점의 타임스텝, 평균 보상, 표준편차 등이 기록됩니다.
 * best_model_save_path: 평가 결과 가장 높은 평균 보상을 기록한 모델을 저장할 경로입니다. 이 경로에 best_model.zip 파일이 저장됩니다.
 * deterministic (기본값: True): 평가 시 에이전트가 무작위 행동 대신 결정론적 행동(deterministic action)을 선택할지 여부입니다. True로 설정하면 일관된 성능 측정이 가능합니다.
 * callback_on_new_best: 새로운 최고 성능 모델이 발견되었을 때 호출될 또 다른 콜백입니다. 이를 통해 조기 종료와 같은 추가적인 로직을 구현할 수 있습니다.
EvalCallback 사용법 및 다양한 사례
1. 기본 사용법: 최고의 모델 저장하기
가장 일반적인 사용 사례는 훈련 중 가장 성능이 좋은 모델을 자동으로 저장하는 것입니다.

```python
import gymnasium as gym
from stable_baselines3 import PPO
from stable_baselines3.common.callbacks import EvalCallback
from stable_baselines3.common.env_util import make_vec_env

# 1. 훈련 환경과 평가 환경 생성
# Vectorized 환경을 사용하면 더 안정적인 훈련이 가능합니다.
train_env = make_vec_env('CartPole-v1', n_envs=4)

# 평가는 일반적으로 단일 환경에서 수행합니다.
eval_env = gym.make('CartPole-v1')

# 2. EvalCallback 설정
# 2000 타임스텝마다 평가를 수행하고, 평가 로그와 최고의 모델을 './logs/' 폴더에 저장합니다.
eval_callback = EvalCallback(eval_env, 
                             best_model_save_path='./logs/',
                             log_path='./logs/',
                             eval_freq=2000,
                             deterministic=True,
                             render=False)

# 3. 모델 생성 및 훈련
# verbose=1로 설정하여 훈련 과정 및 평가 결과를 출력합니다.
model = PPO('MlpPolicy', train_env, verbose=1)

# learn 메소드에 callback을 전달합니다.
model.learn(total_timesteps=50000, callback=eval_callback)

# 훈련 종료 후 최고의 모델 로드
best_model = PPO.load('./logs/best_model')
```

실행 결과:
훈련이 진행되면서 eval_freq 간격마다 평가가 실행되고, 평균 보상이 이전 최고 기록을 경신할 때마다 ./logs/best_model.zip 파일이 업데이트됩니다.

2. 사례: 특정 성능 도달 시 훈련 조기 종료하기
EvalCallback과 StopTrainingOnRewardThreshold 콜백을 조합하여, 모델이 특정 보상 기준을 넘으면 훈련을 자동으로 멈출 수 있습니다. 이는 불필요한 훈련 시간을 줄여줍니다.
```python
import gymnasium as gym
from stable_baselines3 import A2C
from stable_baselines3.common.callbacks import EvalCallback, StopTrainingOnRewardThreshold

# 환경 생성
train_env = gym.make('CartPole-v1')
eval_env = gym.make('CartPole-v1')

# 1. 조기 종료 콜백 설정
# 평가 환경에서의 평균 보상이 495.0을 넘으면 훈련을 중단합니다.
callback_on_best = StopTrainingOnRewardThreshold(reward_threshold=495.0, verbose=1)

# 2. EvalCallback에 조기 종료 콜백 연결
# callback_on_new_best 파라미터에 위에서 생성한 콜백을 전달합니다.
eval_callback = EvalCallback(eval_env,
                             eval_freq=1000,
                             callback_on_new_best=callback_on_best,
                             verbose=1)

# 모델 생성 및 훈련
model = A2C('MlpPolicy', train_env, verbose=1)
model.learn(total_timesteps=100000, callback=eval_callback)
```

실행 결과:
평가 결과 평균 보상이 reward_threshold 값인 495.0을 넘어서면 "Stopping training because the mean reward 495.00 is above the threshold 495.0" 메시지와 함께 훈련이 조기 종료됩니다.

3. 사례: 여러 환경에서 평가하여 안정성 높이기
평가 시에도 여러 환경을 사용하여(Vectorized Environment) 평가 결과의 신뢰도를 높일 수 있습니다. 이는 환경의 무작위성에 따른 성능 변동을 줄여줍니다.

```python
import gymnasium as gym
from stable_baselines3 import SAC
from stable_baselines3.common.env_util import make_vec_env
from stable_baselines3.common.callbacks import EvalCallback

# 훈련 환경
train_env = make_vec_env('Pendulum-v1', n_envs=1)

# 1. 여러 개의 평가 환경 생성
# 5개의 병렬 환경에서 평가를 수행하여 더 안정적인 평균 보상을 계산합니다.
eval_env = make_vec_env('Pendulum-v1', n_envs=5)

# 2. EvalCallback 설정
eval_callback = EvalCallback(eval_env,
                             n_eval_episodes=20, # 각 환경당 에피소드 수가 아닌, 총 에피소드 수
                             best_model_save_path='./logs_pendulum/',
                             log_path='./logs_pendulum/',
                             eval_freq=5000)

# 모델 생성 및 훈련
model = SAC('MlpPolicy', train_env, verbose=1)
model.learn(total_timesteps=100000, callback=eval_callback)
```

실행 결과:
eval_freq마다 5개의 Pendulum-v1 환경에서 동시에 평가가 진행되고, 그 평균값으로 성능을 판단하므로 한두 번의 운 좋은 에피소드로 인해 모델이 과대평가되는 것을 방지할 수 있습니다.


중요 고려사항
 * 환경 래핑(Wrapping): 훈련 환경과 평가 환경은 동일한 Wrapper로 감싸야 합니다. 예를 들어, 훈련 환경에서 TimeLimit이나 FrameStack 같은 래퍼를 사용했다면, 평가 환경에도 반드시 동일하게 적용해야 데이터의 형태와 조건이 일치합니다.
 * Monitor 래퍼: SB3의 콜백은 학습 보상 정보를 Monitor 래퍼를 통해 얻는 경우가 많습니다. 비록 EvalCallback은 자체적으로 보상을 계산하지만, 일관성을 위해 eval_env를 Monitor로 감싸는 것이 좋습니다. 단, make_vec_env 헬퍼 함수는 기본적으로 Monitor를 적용해 주므로 별도로 추가할 필요가 없습니다.
 * 성능 저하: eval_freq를 너무 작게 설정하면 잦은 평가로 인해 전체 훈련 시간이 길어질 수 있습니다. 환경의 복잡성과 에피소드 길이를 고려하여 적절한 값을 설정하는 것이 중요합니다.
