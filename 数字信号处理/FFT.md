# FFT

### 相关参数

| 参数           | 信息             | 备注        |
| -------------- | ---------------- | ----------- |
| 信号频率（F）  |                  | （0，Fs/2） |
| 采样频率（Fs） | 单位时间采样数量 |             |
| 采样周期（T）  | 采样时间间隔     | = 1/Fs      |
| 采样点数（N）  | 采集的总样本数量 |             |

频谱图频率间隔 = Fs / N

频谱横轴数量 = N/2，并且 N/2 < Fs

### 采样定理

1. 采样频率 > `2 * 信号频率`
2. 信号频率中，不能存在 `> 1/2 * 采样率`的信号，杂波也不行

+ 信号混叠