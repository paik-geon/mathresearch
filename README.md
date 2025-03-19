# mathresearch

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
