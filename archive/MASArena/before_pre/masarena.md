# Multi-Agent-System Arena 🏟️

## 理解agent
同一个模型，通过不同agent进行调整，获得不同的能力。  

`base.py`: class AgentSystem 是抽象基类。  
class AgentSystemRegistary: 负责agent的注册和管理



### DeepWiki
- **单智能体**：`single_agent` - 直接使用单个LLM解决问题
- **多智能体系统**：
  - `supervisor_mas` - 基于LangGraph的监督者-工作者模式 supervisor_mas.py:102-108
  - `agentverse` - 动态招募专家团队的系统 agentverse.py:58-73
  - `swarm` - 并行独立工作的群体系统
  - `chateval` - 基于辩论的多智能体系统
  - `jarvis` - 任务规划分解系统 jarvis.py:12-21
  - `evoagent` - 进化算法优化的系统
  - `metagpt` - 软件工程流程的角色化系统


## 理解benchmark
benchmark基准测试，用来评估agent的性能（内置）

(Copied from README.md)  
Supported benchmarks:  
Math: math, aime  
Code: humaneval, mbpp    
Reasoning: drop, bbh, mmlu_pro, ifeval


## 可视化，易插拔



