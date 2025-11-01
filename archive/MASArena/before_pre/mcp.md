# MCP 协议实现指南

基于 `mock_math_server.py` 的详细分析，本文档系统讲解如何实现 MCP 协议。

## 1. MCP 协议概述

### 1.1 什么是 MCP
MCP (Model Context Protocol) 是一个用于 AI 模型与外部工具通信的协议。它定义了客户端和服务器之间的消息交换格式，使 AI 模型能够：
- 发现可用的工具
- 调用工具并传递参数
- 接收工具执行结果
- 处理错误情况

### 1.2 核心特点
- **传输层**: 通过标准输入输出 (stdio) 进行通信
- **消息格式**: JSON 格式的消息
- **异步处理**: 使用 `asyncio` 进行异步 I/O 操作
- **工具定义**: 使用 JSON Schema 定义工具接口

## 2. MCP 协议消息类型

### 2.1 Hello 消息
服务器启动时发送，声明可用工具。

```json
{
  "type": "hello",
  "version": "v1",
  "tools": [
    {
      "name": "tool_name",
      "description": "Tool description",
      "parameters": {
        "type": "object",
        "properties": {...},
        "required": [...]
      },
      "returns": {
        "type": "object",
        "properties": {...}
      }
    }
  ]
}
```

### 2.2 Tool Call 消息
客户端调用工具时发送。

```json
{
  "type": "tool_call",
  "id": "unique_call_id",
  "name": "tool_name",
  "parameters": {
    "param1": "value1",
    "param2": "value2"
  }
}
```

### 2.3 Tool Result 消息
服务器返回工具执行结果。

```json
{
  "type": "tool_result",
  "id": "unique_call_id",
  "result": "tool_execution_result"
}
```

### 2.4 Tool Error 消息
服务器返回工具执行错误。

```json
{
  "type": "tool_error",
  "id": "unique_call_id",
  "error": {
    "message": "Error description",
    "type": "application_error"
  }
}
```

### 2.5 Error 消息
协议级别的错误。

```json
{
  "type": "error",
  "message": "Protocol error description"
}
```

## 3. MCP 服务器实现步骤

### 3.1 基本结构

```python
import sys
import json
import asyncio
from typing import Dict, Any, Optional

class MCPServer:
    def __init__(self):
        self.tools = self._define_tools()
    
    def _define_tools(self) -> list:
        # 定义工具列表
        pass
    
    async def run(self):
        # 主运行循环
        pass
```

### 3.2 工具定义

```python
def _define_tools(self) -> list:
    return [
        {
            "name": "add",
            "description": "Add two numbers together",
            "parameters": {
                "type": "object",
                "properties": {
                    "a": {"type": "number", "description": "First number"},
                    "b": {"type": "number", "description": "Second number"}
                },
                "required": ["a", "b"]
            },
            "returns": {"type": "number", "description": "Sum of a and b"}
        }
    ]
```

### 3.3 消息处理

```python
async def handle_mcp_messages(self):
    try:
        # 1. 发送 hello 消息
        # 这就是“说 hello”的真正含义：发送一个结构化 JSON 消息给上游系统。
        await self.send_hello()
        
        # 2. 进入消息处理循环
        while True:
            message = await self.read_message()
            if not message:
                break
            
            await self.process_message(message)
    except Exception as e:
        await self.log_error(f"Server error: {e}")
        sys.exit(1)
```

### 3.4 消息读写

```python
async def read_message(self) -> Optional[Dict[str, Any]]:
    """从标准输入读取 JSON 消息"""
    try:
        line = await asyncio.to_thread(sys.stdin.readline)
        if not line:
            return None
        return json.loads(line.strip())
    except Exception as e:
        await self.log_error(f"Error reading message: {e}")
        return None

async def send_message(self, message: Dict[str, Any]):
    """向标准输出发送 JSON 消息"""
    try:
        json_str = json.dumps(message)
        sys.stdout.write(f"{json_str}\n")
        sys.stdout.flush()
    except Exception as e:
        await self.log_error(f"Error sending message: {e}")
```

### 3.5 工具调用处理

```python
async def handle_tool_call(self, message: Dict[str, Any]):
    """处理工具调用消息"""
    call_id = message.get("id")
    tool_name = message.get("name")
    parameters = message.get("parameters", {})
    
    try:
        result = await self.execute_tool(tool_name, parameters)
        
        # 发送成功结果
        await self.send_message({
            "type": "tool_result",
            "id": call_id,
            "result": result
        })
        
    except Exception as e:
        # 发送错误结果
        await self.send_message({
            "type": "tool_error",
            "id": call_id,
            "error": {
                "message": str(e),
                "type": "application_error"
            }
        })
```

## 4. 完整实现示例

### 4.1 最小化 MCP 服务器

参考 `minimal_mcp_server.py` 文件，这是一个完整的 MCP 服务器实现，包含：
- 工具定义
- 消息处理
- 错误处理
- 异步 I/O

### 4.2 测试客户端

参考 `mcp_client_example.py` 文件，演示如何：
- 启动 MCP 服务器
- 发送工具调用
- 接收结果
- 处理错误

## 5. 关键实现要点

### 5.1 异步处理
```python
# 使用 asyncio.to_thread 避免阻塞
line = await asyncio.to_thread(sys.stdin.readline)

# 使用异步子进程
process = await asyncio.create_subprocess_exec(...)
```

### 5.2 错误处理
```python
try:
    result = await self.execute_tool(tool_name, parameters)
    await self.send_message({
        "type": "tool_result",
        "id": call_id,
        "result": result
    })
except Exception as e:
    await self.send_message({
        "type": "tool_error",
        "id": call_id,
        "error": {
            "message": str(e),
            "type": "application_error"
        }
    })
```

### 5.3 消息验证
```python
def validate_message(self, message: Dict[str, Any]) -> bool:
    """验证消息格式"""
    required_fields = ["type"]
    if not all(field in message for field in required_fields):
        return False
    
    if message["type"] == "tool_call":
        tool_call_fields = ["id", "name", "parameters"]
        return all(field in message for field in tool_call_fields)
    
    return True
```

### 5.4 工具参数验证
```python
def validate_parameters(self, tool_name: str, parameters: Dict[str, Any]) -> bool:
    """验证工具参数"""
    tool = next((t for t in self.tools if t["name"] == tool_name), None)
    if not tool:
        return False
    
    required_params = tool["parameters"].get("required", [])
    return all(param in parameters for param in required_params)
```

## 6. 最佳实践

### 6.1 工具定义
- 使用清晰的工具名称和描述
- 定义完整的参数类型和验证规则
- 提供详细的返回值说明

### 6.2 错误处理
- 捕获所有可能的异常
- 提供有意义的错误消息
- 区分应用错误和协议错误

### 6.3 性能优化
- 使用异步 I/O 避免阻塞
- 实现超时机制
- 合理使用资源

### 6.4 安全性
- 验证所有输入参数
- 限制工具执行权限
- 防止资源泄露

## 7. 调试技巧

### 7.1 日志记录
```python
async def log_error(self, message: str):
    sys.stderr.write(f"[ERROR] {message}\n")
    sys.stderr.flush()

async def log_debug(self, message: str):
    sys.stderr.write(f"[DEBUG] {message}\n")
    sys.stderr.flush()
```

### 7.2 消息跟踪
```python
async def process_message(self, message: Dict[str, Any]):
    await self.log_debug(f"Received message: {message}")
    # 处理消息
    await self.log_debug(f"Processed message: {message}")
```

### 7.3 测试工具
使用提供的测试客户端验证服务器功能：
```bash
python3 mcp_client_example.py
```

## 8. 扩展功能

### 8.1 支持更多消息类型
```python
async def process_message(self, message: Dict[str, Any]):
    message_type = message.get("type")
    
    if message_type == "tool_call":
        await self.handle_tool_call(message)
    elif message_type == "ping":
        await self.handle_ping(message)
    elif message_type == "shutdown":
        await self.handle_shutdown(message)
    else:
        await self.send_message({
            "type": "error",
            "message": f"Unsupported message type: {message_type}"
        })
```

### 8.2 支持批量操作
```python
async def handle_batch_call(self, message: Dict[str, Any]):
    """处理批量工具调用"""
    calls = message.get("calls", [])
    results = []
    
    for call in calls:
        result = await self.execute_tool(call["name"], call["parameters"])
        results.append(result)
    
    await self.send_message({
        "type": "batch_result",
        "id": message.get("id"),
        "results": results
    })
```

## 9. 总结

MCP 协议的核心在于：
1. **标准化通信**: 通过 stdio 和 JSON 实现标准化通信
2. **工具抽象**: 将外部功能抽象为可调用的工具
3. **异步处理**: 使用异步 I/O 提高性能
4. **错误处理**: 完善的错误处理和恢复机制
5. **扩展性**: 支持动态工具发现和调用

通过遵循这些原则，可以构建出健壮、高效的 MCP 服务器，为 AI 模型提供强大的工具调用能力。