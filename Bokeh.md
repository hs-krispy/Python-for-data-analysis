## Bokeh

- 데이터를 그래프로 시각화할 때, 보다 동적이고 인터렉티브한 그래프로 생성 가능
- 생성된 그래프를 대상으로 이동, 확대, 축소, 저장 등이 가능 

```python
from bokeh.plotting import figure
from bokeh.io import show, output_notebook
import numpy as np

# jupyter notebook에서 그래프 출력
output_notebook()
p = figure(plot_width=800, plot_height=600,
          title="bokeh 예제 그래프",
          x_axis_label="X", y_axis_label="Y")

rec_x = [1, 3, 5, 7, 9]
rec_y = [8, 7, 3, 8, 15]
cir_x = [9, 15, 4, 6, 13]
cir_y = [8, 4, 11, 6, 13]

p.square(rec_x, rec_y, size=12, color="green", alpha=0.6)
p.circle(cir_x, cir_y, size=12, color="red")

output_notebook()
show(p)
```

<img src="https://user-images.githubusercontent.com/58063806/122787146-ff77f380-d2ef-11eb-8167-90d73c3a78b5.png" width=70% />

#### line, bar plot

```python
from bokeh.layouts import gridplot
from bokeh.plotting import output_file

# figure를 html파일로 저장
output_file("line.html")
p1 = figure(plot_width=300, plot_height=300, title="line plot")
p1.line([1, 2, 3, 4, 5], [6, 7, 2, 4, 5], line_width=2)

p2 = figure(plot_width=300, plot_height=300, title="stair plot")
p2.step([1, 2, 3, 4, 5], [6, 7, 2, 4, 5], line_width=2, mode="center")

p3 = figure(plot_width=300, plot_height=300, title="mulit-line plot")
p3.multi_line([[1, 3, 2], [3, 4, 6, 6]], [[2, 1, 4], [4, 7, 8, 5]], color=["firebrick", "navy"],
             alpha=[0.8, 0.3], line_width=4)

# subplot들을 합침
layout = gridplot([[p1, p2, p3]])
show(layout)
```

<img src="https://user-images.githubusercontent.com/58063806/122788632-7cf03380-d2f1-11eb-8fe7-360d6d3b4c39.png" width=100% />

```python
import pandas as pd
from bokeh.sampledata.stocks import AAPL

df = pd.DataFrame(AAPL)
df["date"] = pd.to_datetime(df["date"])
# output_file("datetime.html")

p = figure(plot_width=800, plot_height=250, x_axis_type="datetime")
p.line(df["date"], df["close"], color="navy", alpha=0.5)
show(p)
```

<img src="https://user-images.githubusercontent.com/58063806/122788885-bf197500-d2f1-11eb-8222-0b57008e257f.png" width=90%/>

```python
output_notebook()
p1 = figure(plot_width=400, plot_height=400, title="vertical bar plot")
# 세로 막대 차트
p1.vbar(x=[1, 2, 3], width=0.5, bottom=0, top=[1.2, 2.5, 3.7], color="firebrick")

p2 = figure(plot_width=400, plot_height=400, title="horizontal bar plot")
# 가로 막대 차트
p2.hbar(y=[1, 2, 3], height=0.5, left=0, right=[1.2, 2.5, 3.7], color="navy")

layout = gridplot([[p1, p2]])
show(layout)
```

<img src="https://user-images.githubusercontent.com/58063806/122789625-77dfb400-d2f2-11eb-94a9-2dac8cd25251.png" width=80% />

#### histogram

<img src="https://user-images.githubusercontent.com/58063806/122792164-dc037780-d2f4-11eb-889b-aa42c6371a01.png" width=20%/>

```python
p = figure(plot_width=400, plot_height=400, title="Histogram of Arrival Delays", 
           x_axis_label="Delay (min)", y_axis_label="Number of Flights")
# top - 각 범위의 빈도수, left - 왼쪽 범위, right - 오른쪽 범위
p.quad(bottom=0, top=delays["ArrDelay"], left=delays["left"], right=delays["right"],
      fill_color="red", line_color="black")
show(p)
```

<img src="https://user-images.githubusercontent.com/58063806/122792517-369cd380-d2f5-11eb-86e2-71f4ff2561d0.png" width=50%/>

#### hex plot

```python
from bokeh.util.hex import axial_to_cartesian

q = np.array([0, 0, 0, -1, -1, 1, 1])
r = np.array([0, -1, 1, 0, 1, -1, 0])
p = figure(plot_width=400, plot_height=400, toolbar_location=None)
p.grid.visible = False

p.hex_tile(q, r, size=1, fill_color=["firebrick"] * 3 + ["navy"] * 4,
          line_color="white", alpha=0.5)
x, y = axial_to_cartesian(q, r, 1, "pointytop")
# x - [ 0.         -0.8660254   0.8660254  -1.73205081 -0.8660254   0.8660254 1.73205081]
# y - [-0.   1.5 -1.5 -0.  -1.5  1.5 -0. ]

p.text(x, y, text=["(%d, %d)" % (q, r) for (q, r) in zip(q, r)], text_baseline="middle", text_align="center")
show(p)
```

<img src="https://user-images.githubusercontent.com/58063806/122790490-53d0a280-d2f3-11eb-80f3-e3580454c9b2.png" width=40% />

- axial_to_cartesian : q, r 좌표를 타일 중심의 x, y 좌표에 매핑

```python
from bokeh.transform import linear_cmap
from bokeh.util.hex import hexbin

n = 50000
x = np.random.standard_normal(n)
y = np.random.standard_normal(n)

bins = hexbin(x, y, 0.1)
print(bins.head())
#     q   r  counts
# 0 -27   8       1
# 1 -27  17       1
# 2 -25  11       1
# 3 -25  12       1
# 4 -25  16       1
# ...

q = np.array([0, 0, 0, -1, -1, 1, 1])
r = np.array([0, -1, 1, 0, 1, -1, 0])
# match_aspect = True : 타일이 항상 정육각형이 되도록 
p = figure(tools="wheel_zoom, reset", match_aspect=True, background_fill_color="#440154")
p.grid.visible = False

p.hex_tile(q="q", r="r", size=0.1, line_color=None, source=bins, 
           fill_color=linear_cmap("counts", "Viridis256", 0, max(bins.counts)))
show(p)
```

<img src="https://user-images.githubusercontent.com/58063806/122792028-bb3b2200-d2f4-11eb-894e-57736eb098fc.png" width=50% />



