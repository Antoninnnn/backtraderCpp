# Readme for BTgym

一个基于OpenAI Gym强化学习平台和backtrader回测平台的量化框架。

### Installation

建议建立虚拟环境btgym

`conda create --name btgym`

进入虚拟环境

`conda activate btgym`

克隆btgym

```
git clone https://github.com/Antoninnnn/btgym.git
cd btgym
pip install -e .
```



### Structure

![截屏2021-03-25 下午4.28.53](/Users/yangyining/Desktop/%E6%88%AA%E5%B1%8F2021-03-25%20%E4%B8%8B%E5%8D%884.28.53.png)



##### Envs

强化学习环境模块

##### datafeed

 模块提供了向BTgymEnv灌数据的api。

##### algorithms

（Gym的深度学习底层基于tensorflow）模块封装了诸如基础神经网络（nn），分布式计算（launcher），LSTM模型（policy），aac模型等等。

##### research

模块封装了一些更加先进的算法诸如因果卷积（casual_conv），引导搜寻（GPS）等等

##### strategy

backtrader的strategy子类

##### monitor

深度学习模型日志监控

##### rendering

增强学习各阶段动作可视化





### Demo

```
from gym import spaces
import backtrader as bt
from btgym import BTgymDataset, BTgymBaseStrategy, BTgymEnv

MyCerebro = bt.Cerebro()
MyCerebro.addstrategy(BTgymStrategy,
                      state_shape={'raw': spaces.Box(low=0,high=1,shape=(20,4))},
                      skip_frame=5,
                      state_low=None,
                      state_high=None,
                      drawdown_call=50,
                      )

MyCerebro.broker.setcash(100.0)
MyCerebro.broker.setcommission(commission=0.001)
MyCerebro.addsizer(bt.sizers.SizerFix, stake=10)
MyCerebro.addanalyzer(bt.analyzers.DrawDown)

MyDataset = BTgymDataset(filename='../examples/data/DAT_ASCII_EURUSD_M1_2016.csv',
                         start_weekdays=[0, 1, 2, 4],
                         start_00=True,
                         episode_duration={'days': 0, 'hours': 23, 'minutes': 55},
                         time_gap={'hours': 5},
                         )

MyEnvironment = BTgymEnv(dataset=MyDataset,
                         engine=MyCerebro,
                         port=5555,
                         verbose=1,
                         )
```

