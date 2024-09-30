- Tube-fin heat exchanger
	- refrigerant flows inside the tubes
	- air flows over the fins
-
- # 文字说明
	- **`N_air`** 是空气流动垂直方向上的离散化数量，默认为 1。
	- **初始化**：通过 `init` 关键字参数传递初始化记录，用来初始化模型的各个子组件，包括冷媒侧、空气侧和壁面。
	- **子系统**：包含冷媒侧、空气侧和壁面模型的子系统。
	- **状态变量**：包括压降、总热量、显热、潜热、总质量、出口温度等。
	- **方程连接**：子组件之间的方程连接描述了冷媒和空气流动的路径。
-
- # function TubeFinHEX
	- ## 参数
		- | Parameter               | Description (English)                                                  | Description (中文)                                                                | Initial Value (if applicable)                  |
		  |-------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------------|------------------------------------------------|
		  | `nSeg`                  | Number of segments in the heat exchanger (discretization)              | 热交换器中的分段数量（离散化）                                                     | Not explicitly defined                         |
		  | `nTube`                 | Number of tubes                                                        | 管道的数量                                                                        | Not explicitly defined                         |
		  | `ref_pipe_record`        | Refrigerant pipe record, containing fluid properties and heat transfer parameters | 冷媒管道记录，包含流体属性和传热参数                                               | From `ref_pipe_record`                         |
		  | `air_channel_record`     | Air channel record, containing air flow and heat transfer parameters   | 空气通道记录，包含空气流动和传热参数                                               | From `air_channel_record`                      |
		  | `HX_geometry_params`     | Geometric parameters of the heat exchanger, such as fin area, tube diameter | 热交换器的几何参数，例如翅片面积、管道直径                                         | From `HX_geometry_params`                      |
		  | `pipe_initial_conditions`| Initial conditions of the pipe, such as refrigerant pressure and temperature | 管道的初始条件，例如冷媒压力和温度                                                 | From `pipe_initial_conditions`                 |
		  | `pipe_geometry_params`   | Geometric parameters of the refrigerant pipe, such as length, diameter | 冷媒管道的几何参数，例如长度、直径                                                 | From `pipe_geometry_params`                    |
		  | `pipe_heat_transfer_params`| Heat transfer coefficients for the refrigerant pipe                   | 冷媒管道的传热系数                                                                 | From `pipe_heat_transfer_params`               |
		  | `airchannel_geometry_params`| Geometric parameters of the air channel, such as fin and tube surface areas | 空气通道的几何参数，例如翅片和管道表面积                                           | From `airchannel_geometry_params`              |
		  | `airchannel_heat_transfer_params`| Heat transfer coefficients for the air channel                 | 空气通道的传热系数                                                                 | From `airchannel_heat_transfer_params`         |
		  | `airchannel_initial_conditions` | Initial conditions of the air channel, such as air temperature, humidity | 空气通道的初始条件，例如空气温度和湿度比                                           | From `airchannel_initial_conditions`           |
		  | `Ps_start`              | Initial pressure of the refrigerant at the inlet                       | 冷媒入口的初始压力                                                                 | Explicit initial value from `pipe_initial_conditions` |
		  | `hs_start`              | Initial enthalpy of the refrigerant at the inlet                       | 冷媒入口的初始焓                                                                   | Explicit initial value from `pipe_initial_conditions` |
		  | `Tair_in_start`         | Initial air temperature at the inlet                                   | 空气入口的初始温度                                                                 | Explicit initial value from `airchannel_initial_conditions` |
		  | `Xi_air_in_start`       | Initial humidity ratio of the air at the inlet                         | 空气入口的初始湿度比                                                               | Explicit initial value from `airchannel_initial_conditions` |
		  | `m_flow_air_start`      | Initial air mass flow rate                                             | 空气的初始质量流量                                                                 | Explicit initial value from `airchannel_initial_conditions` |
		  | `m_flow_ref_start`      | Initial refrigerant mass flow rate                                     | 冷媒的初始质量流量                                                                 | Explicit initial value from `pipe_initial_conditions` |
		  | `ref_distributor_params`| Refrigerant distributor parameters                                     | 冷媒分流器的参数                                                                   | Not explicitly defined                         |
		  | `air_distributor_params`| Air distributor parameters                                             | 空气分流器的参数                                                                   | Not explicitly defined                         |
		  | `eta_fin`               | Fin efficiency                                                         | 翅片效率                                                                           | Explicit initial value from `airchannel_initial_conditions` |
		  | `Cp_air`                | Specific heat capacity of air                                          | 空气的比热容                                                                       | Not explicitly defined                         |
		  | `Cp_ref`                | Specific heat capacity of refrigerant                                  | 冷媒的比热容                                                                       | Not explicitly defined                         |
		  | `Qdot_total`            | Total heat transfer rate in the heat exchanger                         | 热交换器的总热流量                                                                 | Possibly from `airchannel_initial_conditions.Q_start` |
		  | `Qdot_sens`             | Sensible heat transfer rate                                            | 显热流量                                                                           | Possibly from `airchannel_initial_conditions.Qdot_sens_start` |
		  | `Qdot_lat`              | Latent heat transfer rate                                              | 潜热流量                                                                           | Possibly from `airchannel_initial_conditions.Qdot_lat_start` |
		  | `p_loss_air`            | Pressure drop on the air side                                          | 空气侧的压降                                                                       | Possibly from `airchannel_initial_conditions.p_loss_start` |
		  | `p_loss_ref`            | Pressure drop on the refrigerant side                                  | 冷媒侧的压降                                                                       | Not explicitly defined                         |
		  | `heatTransfer_coeff_air`| Heat transfer coefficient on the air side                              | 空气侧的传热系数                                                                   | Not explicitly defined                         |
		  | `heatTransfer_coeff_ref`| Heat transfer coefficient on the refrigerant side                      | 冷媒侧的传热系数                                                                   | Not explicitly defined                         |
		  | `air_out_T`             | Air outlet temperature                                                 | 空气出口温度                                                                       | Not explicitly defined                         |
		  | `ref_out_T`             | Refrigerant outlet temperature                                         | 冷媒出口温度                                                                       | Not explicitly defined                         |
		  | `Mtot`                  | Total refrigerant mass in the system                                   | 系统内的冷媒总质量                                                                 | Not explicitly defined                         |
		  | `mcond_out`             | Mass flow rate of condensate water                                     | 冷凝水流量                                                                         | Not explicitly defined                         |
		  | `ref_port_a`, `ref_port_b` | Refrigerant inlet and outlet port parameters                           | 冷媒入口和出口端口参数                                                             | Not explicitly defined                         |
		  | `air_port_a`, `air_port_b` | Air inlet and outlet port parameters                                   | 空气入口和出口端口参数                                                             | Not explicitly defined                         |
	- ## 变量
		- | Variable             | Description (English)                                                  | Description (中文)                                                                | Initial Value (if applicable)            |
		  |----------------------|------------------------------------------------------------------------|-----------------------------------------------------------------------------------|------------------------------------------|
		  | `dP_ref_hx(t)`       | Pressure drop of the refrigerant in the heat exchanger                  | 冷媒在热交换器中的压降                                                             | Not explicitly defined                   |
		  | `Q_tot(t)`           | Total heat transfer rate in the heat exchanger                          | 热交换器的总热流量                                                                 | Initial value: possibly from `airchannel_initial_conditions.Q_start` |
		  | `Qsen_tot(t)`        | Sensible heat transfer rate                                             | 显热流量                                                                           | Initial value: from `airchannel_initial_conditions.Qdot_sens_start` |
		  | `Qlat_tot(t)`        | Latent heat transfer rate                                               | 潜热流量                                                                           | Initial value: from `airchannel_initial_conditions.Qdot_lat_start`  |
		  | `Mtot(t)`            | Total refrigerant mass in the heat exchanger                            | 热交换器中冷媒的总质量                                                             | Not explicitly defined                   |
		  | `Tair_out(t)`        | Air outlet temperature                                                  | 空气出口温度                                                                       | Not explicitly defined                   |
		  | `mcond_out(t)`       | Condensate mass flow rate                                               | 冷凝水质量流量                                                                     | Not explicitly defined                   |
		  | `m_flow_ref(t)`      | Mass flow rate of the refrigerant                                       | 冷媒的质量流量                                                                     | Initial value: from `pipe_initial_conditions`  |
		  | `m_flow_air(t)`      | Mass flow rate of the air                                               | 空气的质量流量                                                                     | Initial value: from `airchannel_initial_conditions` |
		  | `refPort_a.P(t)`     | Pressure of the refrigerant at the inlet                                | 冷媒入口压力                                                                       | Initial value: from `pipe_initial_conditions`  |
		  | `refPort_b.P(t)`     | Pressure of the refrigerant at the outlet                               | 冷媒出口压力                                                                       | Not explicitly defined                   |
		  | `refPort_a.h(t)`     | Enthalpy of the refrigerant at the inlet                                | 冷媒入口焓                                                                         | Initial value: from `pipe_initial_conditions`  |
		  | `refPort_b.h(t)`     | Enthalpy of the refrigerant at the outlet                               | 冷媒出口焓                                                                         | Not explicitly defined                   |
		  | `airPort_a.T(t)`     | Air inlet temperature                                                   | 空气入口温度                                                                       | Initial value: from `airchannel_initial_conditions` |
		  | `airPort_b.T(t)`     | Air outlet temperature                                                  | 空气出口温度                                                                       | Not explicitly defined                   |
		  | `airPort_a.Xi(t)`    | Humidity ratio of the air at the inlet                                  | 空气入口湿度比                                                                     | Initial value: from `airchannel_initial_conditions` |
		  | `airPort_b.Xi(t)`    | Humidity ratio of the air at the outlet                                 | 空气出口湿度比                                                                     | Not explicitly defined                   |
		  | `ref_distributor.m_flow(t)` | Mass flow rate of the refrigerant in the distributor               | 冷
	- ## systems 系统
		- 系统列表，包含ODE系统的所有子系统
			- | Subsystem        | Description                                             |
			  |------------------|---------------------------------------------------------|
			  | `refPort_a`        | 冷媒的入口端口，描述冷媒的状态（压力、流量、焓等）         |
			  | `refPort_b`        | 冷媒的出口端口，描述冷媒离开热交换器时的状态               |
			  | `airPort_a`        | 空气的入口端口，描述空气的状态（压力、温度、湿度等）       |
			  | `airPort_b`        | 空气的出口端口，描述空气离开热交换器时的状态               |
			  | `ref_distributor`  | 冷媒分流器，将冷媒分配到多个通道                          |
			  | `ref_merger`       | 冷媒汇流器，将多个通道的冷媒重新汇集                       |
			  | `air_distributor`  | 空气分流器，将空气分配到多个通道                          |
			  | `air_merger`       | 空气汇流器，将多个通道的空气重新汇集                       |
			  | `air_medium_out`   | 描述空气出口的物理状态（压力、温度、湿度等）               |
			  | `circuit`          | 冷媒管道系统，描述冷媒的流动和传热过程                     |
			  | `wall`             | 壁面热传导模型，描述冷媒与空气之间的热传递                 |
			  | `airFlow`          | 空气流动模型，描述空气在多个通道中的流动和传热             |
				- ![image.png](../assets/image_1727673376603_0.png){:height 226, :width 83}
		- ## Port
			- ### Refrigerent port
				- `TwoPhaseFluidPort`连接器，包含变量：压力、焓、质量流量（都赋有初始值）
				- `refPort_a`：进口
				- `refPort_b`：出口
			- ### Air port
				- `MultiComponentFluidPort`连接器，包含变量：压力、质量流量、温度、水蒸气质量分数
					- [[interfaces.jl]] 中定义了`MultiComponentFluidPort`
				- a进口 b出口
		- ## Distributors and mergers
			- 管翅换热器中有多个流道
			- `RefrigerantSplitterMerger`, `AirSplitterMerger`
				- [[RefrigerantSplitterMerger.jl]]  [[AirSplitterMerger.jl]]
				- 代数方程系统（不随时间变化的ODE系统）
				- 分/汇流前后的质量（质量流量）和能量（焓）守恒
		- air_medium_out
			- `BaseProperties`   [[base_properties.jl]]
		- circuit
			- `StraightPipe`   [[StraightPipe.jl]]
		- wall
			- `OneTempWall1D`   [[OneTempWall1D.jl]]
		- airFlow
			- `HEXCrossflow`  [[HEXCrossFlow.jl]]
	- ## eqns = Equation[] 方程
		- | Equation                                                | Description (English)                                          | Description (中文)                                      |
		  |---------------------------------------------------------|----------------------------------------------------------------|---------------------------------------------------------|
		  | `dP_ref_hx ~ refPort_a.P - refPort_b.P`                 | Pressure drop across the heat exchanger (refrigerant side)      | 冷媒侧的热交换器压降                                    |
		  | `Q_tot ~ sum([airFlow[i].Q_flow[1] for i = 1:N_air]) * nFlows_distributor` | Total heat transfer rate on the air side                     | 空气侧的总热流量                                        |
		  | `Qsen_tot ~ sum([airFlow[i].Qdot_sens[1] for i = 1:N_air]) * nFlows_distributor` | Total sensible heat transfer rate                          | 总显热流量                                              |
		  | `Qlat_tot ~ sum([airFlow[i].Qdot_lat[1] for i = 1:N_air]) * nFlows_distributor` | Total latent heat transfer rate                            | 总潜热流量                                              |
		  | `Mtot ~ nFlows_distributor * circuit.Mtot`              | Total refrigerant mass inside the heat exchanger                | 热交换器内的总冷媒质量                                   |
		  | `mcond_out ~ sum([airFlow[i].mcond_flow[1] for i = 1:N_air]) * nFlows_distributor` | Mass flow rate of the condensed liquid                    | 冷凝水的质量流量                                        |
		  | `connect(circuit_heatPorts, wall_refHeatPorts)`         | Connect the refrigerant heat ports to the wall heat ports       | 将冷媒的热端口连接到壁面热端口                          |
		  | `connect(wall_airHeatPorts, airFlow_heatPorts)`         | Connect the wall heat ports to the air flow heat ports          | 将壁面热端口连接到空气流道热端口                        |
		  | `connect(refPort_a, ref_distributor.port_a)`            | Connect refrigerant inlet port to the distributor               | 将冷媒入口端口连接到分流器                              |
		  | `connect(ref_distributor.port_b, circuit.port_a)`       | Connect distributor output to the refrigerant circuit           | 将分流器输出连接到冷媒回路                              |
		  | `connect(circuit.port_b, ref_merger.port_a)`            | Connect refrigerant circuit output to the merger                | 将冷媒回路输出连接到汇流器                              |
		  | `connect(ref_merger.port_b, refPort_b)`                 | Connect refrigerant merger output to the outlet port            | 将汇流器输出连接到冷媒出口端口                          |
		  | `connect(airPort_a, air_distributor.port_a)`            | Connect air inlet port to the air distributor                   | 将空气入口端口连接到空气分流器                          |
		  | `connect(air_distributor.port_b, airFlow[i].port_a)` for i = 1:N_air | Connect air distributor output to the air flow channels      | 将空气分流器输出连接到空气流道                          |
		  | `connect(airFlow[i].port_b, air_merger.port_a)` for i = 1:N_air | Connect air flow output to the air merger                    | 将空气流道输出连接到空气汇流器                          |
		  | `connect(air_merger.port_b, airPort_b)`                 | Connect air merger output to the air outlet port                | 将空气汇流器输出连接到空气出口端口                      |
		  | `air_medium_out.P ~ airPort_b.P`                        | Set air outlet medium pressure equal to the air outlet port pressure | 空气出口介质的压力等于空气出口端口的压力                |
		  | `air_medium_out.h ~ airPort_b.h_outflow`                | Set air outlet medium enthalpy equal to the air outlet port enthalpy | 空气出口介质的焓等于空气出口端口的焓                    |
		  | `air_medium_out.Xi ~ airPort_b.Xi_outflow`              | Set air outlet medium humidity ratio equal to the air outlet port humidity ratio | 空气出口介质的湿度比等于空气出口端口的湿度比            |
		  | `air_medium_out.T ~ Tair_out`                           | Set air outlet medium temperature equal to the outlet air temperature | 空气出口介质的温度等于空气出口温度                      |
	- ## sys = ODESystem(eqns, t, vars, []; name, systems)
-