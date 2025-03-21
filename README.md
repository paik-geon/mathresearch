## 전체코드
```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.widgets import Slider, Button

#  기본 확률 설정 (아이템 종류 5개)
items = ["Common", "Uncommon", "Rare", "Epic", "Legendary"]
base_probs = np.array([0.5, 0.3, 0.15, 0.04, 0.01])  # 기본 확률 (총합 1)

#  확률 조작 함수
def adjust_probabilities(legendary_prob):
    """
    사용자가 'Legendary' 확률을 조작하면, 나머지 확률을 자동으로 조정하여 총합이 1이 되도록 설정.
    """
    remaining_prob = 1.0 - legendary_prob  # 조작된 확률을 제외한 나머지
    factor = remaining_prob / (1.0 - base_probs[-1])  # 기존 나머지 확률들의 비율 조정
    adjusted_probs = np.array(base_probs)
    adjusted_probs[:-1] *= factor  # 다른 확률들을 비율에 맞게 조정
    adjusted_probs[-1] = legendary_prob  # 'Legendary' 확률은 조작된 값 적용
    return adjusted_probs

#  드롭 시뮬레이션 함수
def generate_drops(seed_value, legendary_prob, trials=10000):
    np.random.seed(seed_value)
    modified_probs = adjust_probabilities(legendary_prob)  # 조작된 확률 반영
    results = np.random.choice(items, size=trials, p=modified_probs)
    return {item: np.sum(results == item) for item in items}, modified_probs

#  그래프 업데이트 함수
def update(val):
    seed_value = int(seed_slider.val)  # 시드값
    legendary_prob = legend_slider.val / 100  # 전설 확률 (0~1 범위로 변환)
    
    drop_data, modified_probs = generate_drops(seed_value, legendary_prob)
    
    ax.clear()  
    ax.bar(drop_data.keys(), drop_data.values(), color=["gray", "green", "blue", "purple", "gold"])
    ax.set_xlabel("Item Rarity")
    ax.set_ylabel("Drop Count")
    ax.set_title(f"Game Drop Simulation (Seed={seed_value})\nAdjusted Probabilities: {np.round(modified_probs, 3)}")
    fig.canvas.draw_idle()

#  UI 생성 (슬라이더, 그래프)
fig, ax = plt.subplots(figsize=(8, 6))
plt.subplots_adjust(bottom=0.3)

# 초기 시뮬레이션 실행
initial_seed = 42
initial_legendary_prob = base_probs[-1] * 100  # 초기 전설 확률 (%)
drop_data, _ = generate_drops(initial_seed, initial_legendary_prob / 100)

bars = ax.bar(drop_data.keys(), drop_data.values(), color=["gray", "green", "blue", "purple", "gold"])

#  시드값 조작 슬라이더
ax_seed_slider = plt.axes([0.2, 0.15, 0.65, 0.03])
seed_slider = Slider(ax_seed_slider, "Seed", 0, 100, valinit=initial_seed, valfmt="%0.0f")
seed_slider.on_changed(update)

#  전설 확률 조작 슬라이더
ax_legend_slider = plt.axes([0.2, 0.08, 0.65, 0.03])
legend_slider = Slider(ax_legend_slider, "Legendary %", 0, 50, valinit=initial_legendary_prob, valfmt="%0.1f%%")
legend_slider.on_changed(update)

#  확률 초기화 버튼
def reset(event):
    seed_slider.set_val(42)
    legend_slider.set_val(base_probs[-1] * 100)

ax_reset = plt.axes([0.8, 0.02, 0.1, 0.05])
button = Button(ax_reset, "Reset")
button.on_clicked(reset)

plt.show()
```
---

## 1. 사전기능 호출
```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.widgets import Slider, Button
```
코드를 실행하고 가시적으로 보여주기 위해 사용하는 코드
numpy, matplotlib이라는 계산, 시각화 도구를 호출



## 2. 기본 확률 설정
```python
#  기본 확률 설정 (아이템 종류 5개)
items = ["Common", "Uncommon", "Rare", "Epic", "Legendary"]
base_probs = np.array([0.5, 0.3, 0.15, 0.04, 0.01])  # 기본 확률 (총합 1)
```
랜덤시드의 무작위성을 보여주는 방법중 하나로 게임의 아이템 드롭 확률 결정
아이템의 등급을 5개로 나누 후 확률 설정



## 3. 확률 조작 함수
```python
#  확률 조작 함수
def adjust_probabilities(legendary_prob):
    """
    사용자가 'Legendary' 확률을 조작하면, 나머지 확률을 자동으로 조정하여 총합이 1이 되도록 설정.
    """
    remaining_prob = 1.0 - legendary_prob  # 조작된 확률을 제외한 나머지
    factor = remaining_prob / (1.0 - base_probs[-1])  # 기존 나머지 확률들의 비율 조정
    adjusted_probs = np.array(base_probs)
    adjusted_probs[:-1] *= factor  # 다른 확률들을 비율에 맞게 조정
    adjusted_probs[-1] = legendary_prob  # 'Legendary' 확률은 조작된 값 적용
    return adjusted_probs
```
확률을 조작할 수 있도록 만드는 함수를 생성
주석에도 설명해두었듯이 사용자가 슬라이더를 통해 'Legendary' 확률을 조작하면 나머지 확률을 자동으로 조정하여 총합이 1이 되도록 설정함


## 4. 드롭 시뮬레이션 함수
```python
#  드롭 시뮬레이션 함수
def generate_drops(seed_value, legendary_prob, trials=10000):
    np.random.seed(seed_value)
    modified_probs = adjust_probabilities(legendary_prob)  # 조작된 확률 반영
    results = np.random.choice(items, size=trials, p=modified_probs)
    return {item: np.sum(results == item) for item in items}, modified_probs
```
시드값과 조작된 확률을 적용하여 10,000번의 아이템 드롭 시뮬레이션을 수행
사용자가 전설 등급 확률을 조작하면 나머지 확률이 자동으로 조정되어 적용됨
결과는 아이템별 획득 개수와 조작된 확률을 반환하며 그래프를 통해 실시간으로 조작 효과를 확인할 수 있음


## 5. 그래프 업데이트 함수
```python
def update(val):
    seed_value = int(seed_slider.val)  # 시드값
    legendary_prob = legend_slider.val / 100  # 전설 확률 (0~1 범위로 변환)
    
    drop_data, modified_probs = generate_drops(seed_value, legendary_prob)
    
    ax.clear()  
    ax.bar(drop_data.keys(), drop_data.values(), color=["gray", "green", "blue", "purple", "gold"])
    ax.set_xlabel("Item Rarity")
    ax.set_ylabel("Drop Count")
    ax.set_title(f"Game Drop Simulation (Seed={seed_value})\nAdjusted Probabilities: {np.round(modified_probs, 3)}")
    fig.canvas.draw_idle()
```
10,000번의 시뮬레이션을 수행한 후 각각 그래프의 값을 생성

## 6. UI생성
```python
#  UI 생성 (슬라이더, 그래프)
fig, ax = plt.subplots(figsize=(8, 6))
plt.subplots_adjust(bottom=0.3)
```
UI 상단에는 그래프를, 하단에는 슬라이더를 배치

## 7. 초기 시드 설정값
```python
# 초기 시뮬레이션 실행
initial_seed = 42
initial_legendary_prob = base_probs[-1] * 100  # 초기 전설 확률 (%)
drop_data, _ = generate_drops(initial_seed, initial_legendary_prob / 100)

bars = ax.bar(drop_data.keys(), drop_data.values(), color=["gray", "green", "blue", "purple", "gold"])
```
프로그램을 실행했을 떄 초기 시드값 42로 시작하게 함

## 8. 시드값 조정 슬라이더
```python
#  시드값 조작 슬라이더
ax_seed_slider = plt.axes([0.2, 0.15, 0.65, 0.03])
seed_slider = Slider(ax_seed_slider, "Seed", 0, 100, valinit=initial_seed, valfmt="%0.0f")
seed_slider.on_changed(update)
```
시드값을 조정하기 위해 슬라이더를 생성
0부터 100까지의 값을 설정가능하도록 슬라이더 설정

## 9. 레전더리 확률 조작 슬라이더
```python
#  전설 확률 조작 슬라이더
ax_legend_slider = plt.axes([0.2, 0.08, 0.65, 0.03])
legend_slider = Slider(ax_legend_slider, "Legendary %", 0, 50, valinit=initial_legendary_prob, valfmt="%0.1f%%")
legend_slider.on_changed(update)
```
총량을 1을 유지할 수 있도록 50%로 제한을 두고 조작 슬라이더 생성
0%부터 50%까지 조작가능

## 10. 확률 초기화 버튼
```python
#  확률 초기화 버튼
def reset(event):
    seed_slider.set_val(42)
    legend_slider.set_val(base_probs[-1] * 100)

ax_reset = plt.axes([0.8, 0.02, 0.1, 0.05])
button = Button(ax_reset, "Reset")
button.on_clicked(reset)
```
초기 시드값 42에 레전더리 확률 0.2%로 초기화 버튼 생성

## 11. 프로그램 실행
```python
plt.show()
```
프로그램을 실행하기 위해 실행함수 호출

***


#  게임 랜덤 확률 조작 수식 정리


##  1. 남은 확률 계산

전설 아이템 확률을 제외한 **나머지 아이템들의 확률 총합**을 구함

$$
\mathit{remaining\_prob} = 1.0 - \mathit{legendary\_prob}
$$



##  2. 나머지 확률 조정 비율 계산

기존 아이템들의 확률을 비율에 맞게 자동 조정

$$
\mathit{scaling\_factor} = \frac{\mathit{remaining\_prob}}{1.0 - P_{\mathit{legendary}}}
$$

 `scaling_factor`를 곱해서 나머지 확률을 조정할 수 있음



##  3. 다른 아이템의 확률을 조정

모든 아이템의 확률을 "남은 확률"에 맞춰 자동 조정

$$
P'_i = P_i \times \mathit{scaling\_factor}, \quad i \neq \text{Legendary}
$$

이 수식을 적용하면, 기존 확률 비율은 유지되면서도  
총합이 100%가 되도록 자동 조정



##  4. 전설 아이템 확률 설정

사용자가 직접 조작한 확률을 전설 아이템 확률로 적용

$$
P'_{\mathit{legendary}} = \mathit{legendary\_prob}
$$



##  5. 랜덤 샘플링 (확률 분포 적용)

각 아이템의 등장 확률이 **P_i**일 때,  
10,000번의 시뮬레이션에서 각 아이템이 나올 개수는 다음과 같음

$$
N_i = P_i \times \text{trials}
$$

이를 통해 특정 아이템이 얼마나 뽑히는지를 계산 가능



##  6. 시드값 적용 (난수 생성 제어)

같은 시드(seed)를 사용하면 **항상 같은 확률 분포**에서 결과가 생성
이를 위해 **LCG(Linear Congruential Generator) 난수 생성 공식**을 적용할 수 있음

$$
X_n = (aX_{n-1} + c) \mod m
$$



##  7. 결론

- 전설 아이템 확률을 높이면, 나머지 확률이 자동으로 줄어듬
- 확률의 총합이 100%로 유지되도록 설계됨
- 같은 **시드(seed)** 값을 사용하면 **동일한 확률 분포에서 동일한 결과**가 나옴
- 이 방식을 사용하면 **랜덤 확률을 조작하여 특정 아이템을 더 많이 드롭**할 수 있음




---
코드는 전부 직접 작성하였지만 수식을 구성하는 부분은 여러 티스토리와 네이버 블로그, 논문, 학설지 등을 참고함.

## 출처 및 참고자료

1. **확률 이론 (Probability Theory)**
   - Sheldon Ross, *A First Course in Probability* (Pearson, 2019)
   - William Feller, *An Introduction to Probability Theory and Its Applications* (John Wiley & Sons, 1968)

2. **게임 확률 밸런싱 및 확률 조정 기법**
   - Jesse Schell, *The Art of Game Design* (CRC Press, 2020)
   - Richard Garfield, *Game Balance* (CRC Press, 2022)

3. **난수 생성 알고리즘**
   - Donald Knuth, *The Art of Computer Programming, Vol. 2: Seminumerical Algorithms* (Addison-Wesley, 1997)
   - Pierre L'Ecuyer, *Random Number Generation and Monte Carlo Methods* (Springer, 2011)
