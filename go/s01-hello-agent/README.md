# s01: Hello Agent

> _"Agent 的本质，就是一个调用 LLM 的循环"_

本课实现最小可运行的 Agent，理解 Agent 的核心结构。

## 运行

```bash
cd go/s01-hello-agent
export DEEPSEEK_API_KEY=your-key      # 使用 DeepSeek
export OPENAI_API_KEY=your-key        # 或使用 OpenAI

go run .
```

## 代码结构
```
s01-hello-agent/
├── main.go       # 程序入口 + 对话循环
├── message.go    # 消息类型定义
├── client.go     # API 客户端
└── README.md     # 本文件
```

## 核心代码
```go
// 初始化消息历史
messages := []Message{
    {Role: "system", Content: "你是一个有帮助的AI助手。简洁地回答问题。"},
}

// 对话循环 (Agent Loop 的雏形)
scanner := bufio.NewScanner(os.Stdin)
for {
    fmt.Print("你: ")
    if !scanner.Scan() {
        break
    }

    input := strings.TrimSpace(scanner.Text())
    if input == "quit" {
        break
    }
    if input == "" {
        continue
    }

    messages = append(messages, Message{
        Role: "user", Content: input,
    })

    // 调用 API
    ctx := context.Background()
    response, err := client.Complete(ctx, messages)
    if err != nil {
        fmt.Printf("API 错误: %v\n", err)
        continue
    }

    // 获取助手回复并加入历史 (保持上下文)
    assistantMsg := response.Choices[0].Message.Content
    fmt.Printf("\nAI: %s\n\n", assistantMsg)
    messages = append(messages, Message{
        Role: "assistant", Content: assistantMsg,
    })
}
```

## 学习要点

1. **Agent 本质**：LLM + 循环，不断调用模型直到任务完成
2. **消息格式**：role (system/user/assistant) + content 构成对话历史
3. **API 调用**：HTTP POST + JSON 序列化，标准的 REST 交互模式

## 下一课
[s02-api-client](../s02-api-client) - 多 Provider 支持
