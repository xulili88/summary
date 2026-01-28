---


---

<h1 id="simrobot外部gc控制的实现">SimRobot外部(gc)控制的实现</h1>
<h2 id="概述">概述</h2>
<p>比较原本由内部命令控制simrobot，实现了通过外部gc控制simrobot</p>
<h2 id="替换simulatednao">1.替换SimulatedNao</h2>
<p><strong>目录：</strong> Src/Libs/SimulatedNao<br>
<strong>替换内容：</strong></p>
<pre><code>SimulatedNao/GameController.h
SimulatedNao/GameController.cpp
</code></pre>
<ul>
<li>新增UDP套接字成员变量</li>
<li>新增外部GameController启用/禁用方法</li>
<li>新增UDP消息处理方法</li>
<li>新增机器人状态发送方法</li>
<li>修改状态管理逻辑以支持外部控制</li>
</ul>
<pre><code>SimulatedNao/ConsoleRoboCupCtrl.cpp
SimulatedNao/ConsoleRoboCupCtrl.h
</code></pre>
<ul>
<li>在命令处理中添加  <code>gc external on/off</code>  命令</li>
<li>集成CommandServer的TCP命令服务器</li>
</ul>
<pre><code>SimulatedNao/CommandServer.cpp
SimulatedNao/CommandServer.h
</code></pre>
<ul>
<li>TCP服务器实现，用于接收外部命令</li>
<li>命令队列管理</li>
<li>客户端连接处理</li>
</ul>
<pre><code>SimulatedNao/Network/UdpComm.h
SimulatedNao/Network/UdpComm.cpp
</code></pre>
<ul>
<li>这些文件提供UDP通信的基础功能</li>
</ul>
<p><code>SimulatedNao/Representations/Communication/GameControllerData.h</code></p>
<ul>
<li>定义与外部GameController通信的数据结构</li>
</ul>
<pre><code>SimulatedNao/RemoteConsole.h
SimulatedNao/RemoteConsole.cpp
</code></pre>
<ul>
<li>远程机器人连接的UDP通信支持</li>
</ul>
<pre><code>SimulatedNao/RoboCupCtrl.h
SimulatedNao/RoboCupCtrl.cpp
</code></pre>
<ul>
<li>集成GameController和CommandServer</li>
<li>初始化UDP通信组件</li>
</ul>
<p><strong>拉取连接：</strong> <a href="http://212.64.83.115:5244/ye/SimulatedNao">http://212.64.83.115:5244/ye/SimulatedNao</a></p>
<h2 id="编译">2.编译</h2>
<p><code>./Make/Linux/compile Develop</code></p>
<h2 id="更换gc">3.更换gc</h2>
<p><strong>替换内容：</strong></p>
<pre><code>GameController3/game_controller_net/src/control_message_sender.rs
</code></pre>
<ul>
<li>发送游戏控制消息到SimRobot (UDP端口3838)</li>
</ul>
<pre><code>GameController3/game_controller_net/src/status_message_receiver.rs
</code></pre>
<ul>
<li>接收SimRobot机器人状态消息 (UDP端口3939)</li>
</ul>
<pre><code>GameController3/game_controller_msgs/src/control_message.rs
GameController3/game_controller_msgs/src/status_message.rs
</code></pre>
<ul>
<li>定义与SimRobot通信的RoboCup标准消息格式</li>
</ul>
<pre><code>GameController3/game_controller_msgs/headers/RoboCupGameControlData.h
</code></pre>
<ul>
<li>C语言兼容的协议头文件，与SimRobot接口对接</li>
</ul>
<pre><code>GameController3/game_controller_core/src/actions/
├── penalize.rs          # 惩罚控制
├── goal.rs              # 进球处理  
├── substitute.rs        # 换人操作
└── timeout.rs           # 暂停管理
</code></pre>
<ul>
<li>游戏动作执行后通过UDP同步到SimRobot</li>
</ul>
<pre><code>GameController3/game_controller_runtime/src/connection_status.rs
</code></pre>
<ul>
<li>管理与SimRobot的UDP连接状态</li>
</ul>
<pre><code>GameController3/game_controller_app/src/handlers.rs
</code></pre>
<ul>
<li>处理前端请求，调用UDP通信模块控制SimRobot</li>
</ul>
<pre><code>GameController3/logs/log_2026-01-27_*_*.yaml
</code></pre>
<ul>
<li>包含SimRobot仿真比赛的实际数据记录</li>
</ul>
<p><strong>拉取连接：</strong> <a href="http://212.64.83.115:5244/ye/gamecontrol">http://212.64.83.115:5244/ye/gamecontrol</a></p>
<h2 id="运行gc">4.运行gc</h2>
<p>打开gc后需要修改<br>
1.Competition：选择Champions Cup 5 vs. 5<br>
2.队伍暂时只能选择5和70<br>
3.Testing：勾选No Delay<br>
4.<strong>Interface</strong>:lo  （用本地网络）<br>
5.<strong>Casting</strong>:勾选Multicase  （组播）<br>
Start</p>
<h2 id="打开simrobot">5.打开simrobot</h2>
<p>在控制台输入<code>gc external on</code></p>
<h2 id="观察是否连接所有机器人">6.观察是否连接所有机器人</h2>

