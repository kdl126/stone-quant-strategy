A股量化策略回测系统 - 双均线趋势跟踪策略
基于 Python 的完整量化回测框架，集成风控模块与专业绩效指标，支持在线可视化演示
 
项目背景
A股市场震荡行情较多，普通个人投资者易出现高买低卖、无法有效控制回撤等问题。
本项目基于经典双均线策略实现趋势跟踪，并加入动态风控规则，旨在为个人投资者提供可落地的量化交易方案，同时展示金融数据分析与工程开发能力。
 
数据获取：akshare（A股日线数据）
数据处理：pandas, numpy
回测与指标：自定义绩效计算（累计收益、最大回撤、夏普比率、胜率）
可视化：matplotlib（回测曲线）、Streamlit（在线交互Demo）
版本控制：Git / GitHub
 
策略核心逻辑
1. 信号规则
买入信号：5日均线向上穿越20日均线（金叉）
卖出信号：5日均线向下穿越20日均线（死叉）
 
# 核心信号生成代码
df['ma5'] = df['close'].rolling(5).mean()
df['ma20'] = df['close'].rolling(20).mean()
df['signal'] = 0
df.loc[df['ma5'] > df['ma20'], 'signal'] = 1  # 金叉 → 买入
df.loc[df['ma5'] < df['ma20'], 'signal'] = 0  # 死叉 → 卖出
 
2. 风控规则
当策略回撤超过10%时强制清仓，有效控制极端行情下的亏损风险：
# 风控：最大回撤超过10%时清仓
df['cum_strategy'] = (1 + df['strategy_return']).cumprod()
df['max2date'] = df['cum_strategy'].cummax()
df['drawdown'] = (df['cum_strategy'] - df['max2date']) / df['max2date']
df.loc[df['drawdown'] < -0.10, 'position'] = 0  # 回撤超10% → 清仓
 
回测指标
累计收益率：策略总收益表现
最大回撤：衡量策略风险水平
夏普比率：风险调整后收益
交易胜率：盈利交易占比
盈亏比：平均盈利与平均亏损的比值
 
补充
1. 安装依赖
pip install akshare pandas numpy matplotlib streamlit
 
2. 本地回测
运行核心回测脚本，生成收益曲线与绩效指标：
python backtest.py
 
3. 在线可视化Demo
启动Streamlit网页版，支持输入任意股票代码实时回测：
streamlit run app.py
 
回测结果示例（以贵州茅台为例）
回测标的：贵州茅台（600519）
回测区间：2020-01-01 至 2026-03-22
累计收益：264.26%(持有不动仅60.78%)
年化收益：24.18%(持有不动仅8.28%)
最大回撤：-9.78%(持有不动为-50.70%)
夏普比率：1.39(持有不动仅0.35)
交易胜率：56.21%
![600519股票策略回测]
（https://github.com/kdl126/stone-quant-strategy/blob/main/屏幕截图%202026-03-22%20222711.png?raw=true） 

未来改进方向
1. 加入多因子模型（估值、动量、成长因子）优化策略
2. 集成机器学习模型预测价格趋势
3. 支持多股票组合回测与行业轮动
4. 接入实时行情，实现半自动交易提醒
