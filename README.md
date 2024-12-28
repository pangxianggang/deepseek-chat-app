<!DOCTYPE html>
<html lang="zh">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>DeepSeek</title>
  <!-- 引入 Google Fonts -->
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@400;700&display=swap" rel="stylesheet">

  <!-- MathJax 配置与脚本 -->
  <script>
    window.MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\$', '\\$']],
        displayMath: [['$$', '$$'], ['\\[', '\\]']]
      }
    };
  </script>
  <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js"></script>

  <style>
    /* 全局样式 */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Noto Sans SC', sans-serif;
    }

    body {
      display: flex;
      flex-direction: column;
      width: 100%;
      height: 100vh;
      background: #ffffff;
      color: #333;
    }

    /* 顶栏 */
    .header {
      flex: 0 0 auto;
      height: 60px;
      background-color: #ffffff;
      border-bottom: 1px solid #e0e0e0;
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 20px;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .app-title {
      font-size: 24px;
      color: #333;
      font-weight: 700;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    /* 可选：添加图标 */
    .app-title::before {
      content: "🔍";
      font-size: 28px;
    }

    /* 顶栏右侧图标分组 */
    .header-icons {
      display: flex;
      align-items: center;
      gap: 15px;
    }

    /* 按钮通用样式 */
    .header-icons button,
    .toolbox-body button,
    .icon-btn,
    .chat-input button {
      cursor: pointer;
      border: none;
      outline: none;
      border-radius: 5px;
      transition: background-color 0.3s, color 0.3s;
    }

    /* 全屏按钮 */
    .fullscreen-btn {
      font-size: 22px;
      color: #555;
      background: #f0f0f0;
      padding: 8px;
    }

    .fullscreen-btn:hover {
      background: #e0e0e0;
      color: #000;
    }

    /* 工具箱按钮 */
    .toolbox-btn {
      font-size: 22px;
      color: #555;
      background: #f0f0f0;
      padding: 8px;
    }

    .toolbox-btn:hover {
      background: #e0e0e0;
      color: #000;
    }

    /* 工具箱(右侧浮层) */
    .toolbox-container {
      position: fixed;
      top: 60px;
      right: 0;
      width: 320px;
      height: calc(100% - 60px);
      background: #ffffff;
      border-left: 1px solid #e0e0e0;
      box-shadow: -2px 0 5px rgba(0, 0, 0, 0.1);
      display: flex;
      flex-direction: column;
      transform: translateX(100%);
      transition: transform 0.3s ease;
      z-index: 999;
    }

    .toolbox-container.show {
      transform: translateX(0);
    }

    .toolbox-header {
      font-size: 20px;
      font-weight: 700;
      padding: 20px;
      border-bottom: 1px solid #e0e0e0;
      background-color: #fafafa;
    }

    .toolbox-body {
      flex: 1;
      overflow-y: auto;
      padding: 20px;
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .toolbox-body label {
      font-size: 14px;
      color: #555;
      margin-bottom: 5px;
    }

    .toolbox-body input[type="text"],
    .toolbox-body input[type="number"],
    .toolbox-body input[type="color"] {
      width: 100%;
      height: 40px;
      font-size: 14px;
      padding: 0 10px;
      border: 1px solid #dcdcdc;
      border-radius: 5px;
      transition: border-color 0.3s;
    }

    .toolbox-body input[type="text"]:focus,
    .toolbox-body input[type="number"]:focus,
    .toolbox-body input[type="color"]:focus {
      border-color: #3367d6;
    }

    /* 按钮样式 */
    .toolbox-body button {
      height: 42px;
      font-size: 16px;
      background: #3367d6;
      color: #ffffff;
      border: none;
      border-radius: 5px;
      padding: 0 15px;
    }

    .toolbox-body button:hover {
      background: #2851a3;
    }

    /* 清空聊天窗口按钮 */
    #clearChatBtn {
      background: #e53935;
    }

    #clearChatBtn:hover {
      background: #c62828;
    }

    /* 聊天区 */
    .chat-container {
      flex: 1;
      overflow-y: auto;
      padding: 20px;
      background: #ffffff;
      position: relative;
    }

    .message-wrapper {
      display: flex;
      width: 100%;
      margin-bottom: 15px;
    }

    .wrapper-user {
      justify-content: flex-end;
    }

    .wrapper-ai {
      justify-content: flex-start;
    }

    .chat-message {
      display: inline-flex;
      flex-direction: column;
      padding: 12px 16px;
      border: 1px solid #e0e0e0;
      border-radius: 15px;
      max-width: 70%;
      word-wrap: break-word;
      line-height: 1.5;
      white-space: pre-wrap;
      background-color: #f9f9f9;
      color: #333;
      box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
      position: relative;
    }

    /* 气泡箭头 */
    .wrapper-user .chat-message::after {
      content: '';
      position: absolute;
      top: 10px;
      right: -10px;
      border-width: 5px;
      border-style: solid;
      border-color: transparent transparent transparent #f9f9f9;
    }

    .wrapper-ai .chat-message::after {
      content: '';
      position: absolute;
      top: 10px;
      left: -10px;
      border-width: 5px;
      border-style: solid;
      border-color: transparent #f9f9f9 transparent transparent;
    }

    /* 每条消息下方的图标区域 */
    .message-actions {
      display: flex;
      justify-content: flex-end;
      gap: 10px;
      margin-top: 8px;
    }

    .icon-btn {
      background: #f0f0f0;
      color: #555;
      padding: 6px;
      border-radius: 4px;
      transition: background-color 0.3s, color 0.3s;
    }

    .icon-btn:hover {
      background: #e0e0e0;
      color: #000;
    }

    /* 底部输入区 */
    .chat-input {
      flex: 0 0 auto;
      display: flex;
      padding: 15px 20px;
      background: #ffffff;
      border-top: 1px solid #e0e0e0;
      box-shadow: 0 -2px 4px rgba(0, 0, 0, 0.05);
    }

    .chat-input input {
      flex: 1;
      height: 50px;
      font-size: 16px;
      padding: 0 15px;
      border: 1px solid #dcdcdc;
      border-radius: 25px;
      transition: border-color 0.3s;
    }

    .chat-input input:focus {
      border-color: #3367d6;
      outline: none;
    }

    .chat-input button {
      width: 80px;
      margin-left: 15px;
      background: #3367d6;
      color: #ffffff;
      font-size: 16px;
      border: none;
      border-radius: 25px;
      transition: background-color 0.3s;
    }

    .chat-input button:hover {
      background: #2851a3;
    }

    /* 滚动条样式 */
    ::-webkit-scrollbar {
      width: 8px;
    }

    ::-webkit-scrollbar-thumb {
      background-color: #cccccc;
      border-radius: 4px;
    }

    /* 聊天记录列表 */
    .history-list {
      border-top: 1px solid #e0e0e0;
      padding-top: 15px;
      margin-top: 15px;
      max-height: 150px;
      overflow-y: auto;
    }

    .history-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 14px;
      border-bottom: 1px solid #f0f0f0;
      padding: 8px 0;
      word-break: break-all;
    }

    .history-item:last-child {
      border-bottom: none;
    }

    .history-item button {
      background: #e0e0e0;
      color: #555;
      padding: 4px 8px;
      border-radius: 4px;
      transition: background-color 0.3s, color 0.3s;
    }

    .history-item button:hover {
      background: #c0c0c0;
      color: #000;
    }

    /* 代码块样式 */
    pre code {
      background: #f4f4f4;
      padding: 10px;
      border-radius: 5px;
      display: block;
      overflow-x: auto;
    }

    /* 运行代码按钮 */
    .run-code-btn {
      margin-top: 5px;
      align-self: flex-end;
      background: #00bcd4;
      color: #ffffff;
      padding: 6px 12px;
      border-radius: 4px;
      font-size: 14px;
      cursor: pointer;
    }

    .run-code-btn:hover {
      background: #0097a7;
    }

    /* 模态框样式 */
    .code-modal {
      display: block;
      position: fixed;
      z-index: 1000;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
      overflow: auto;
      background-color: rgba(0, 0, 0, 0.5);
    }

    .code-modal-content {
      background-color: #fefefe;
      margin: 5% auto;
      padding: 20px;
      border: 1px solid #888;
      width: 80%;
      max-width: 800px;
      position: relative;
    }

    .code-modal-close {
      color: #aaa;
      position: absolute;
      top: 10px;
      right: 20px;
      font-size: 28px;
      font-weight: bold;
      cursor: pointer;
    }

    .code-modal-close:hover,
    .code-modal-close:focus {
      color: black;
      text-decoration: none;
      cursor: pointer;
    }

    .code-output-container {
      width: 100%;
      height: 600px;
    }

    .code-modal-header {
      font-size: 18px;
      font-weight: bold;
      margin-bottom: 10px;
    }

    /* 阻止拖拽时选中文本 */
    .chat-container,
    .chat-message,
    .code-output-container {
      user-select: none;
    }
  </style>
</head>

<body>
  <!-- 顶栏 -->
  <div class="header">
    <div class="app-title">DeepSeek</div>
    <div class="header-icons">
      <!-- 全屏按钮 -->
      <button class="fullscreen-btn" id="fullscreenToggle" title="全屏">⛶</button>
      <!-- 工具箱按钮 -->
      <button class="toolbox-btn" id="toolboxToggle" title="工具箱">🧰</button>
    </div>
  </div>
  <!-- 工具箱 -->
  <div class="toolbox-container" id="toolboxContainer">
    <div class="toolbox-header">工具箱</div>
    <div class="toolbox-body">
      <!-- 设置API Key -->
      <div class="tool-section">
        <label for="apiKeyInput">DeepSeek API Key</label>
        <input id="apiKeyInput" type="text" placeholder="请输入 DeepSeek Key">
        <button id="setKeyBtn">设置Key</button>
      </div>
      <!-- 设置记忆条数 -->
      <div class="tool-section">
        <label for="memoryLimit">记忆条数</label>
        <input id="memoryLimit" type="number" min="1" max="999" placeholder="如：10">
        <button id="setMemoryBtn">设置记忆</button>
      </div>
      <!-- 自定义颜色选项 -->
      <div class="tool-section">
        <label for="desktopColor">桌面背景颜色</label>
        <input id="desktopColor" type="color" class="color-picker" value="#FFFFFF">
      </div>
      <div class="tool-section">
        <label for="bubbleColorUser">用户气泡颜色</label>
        <input id="bubbleColorUser" type="color" class="color-picker" value="#FFF0F0" />
      </div>
      <div class="tool-section">
        <label for="bubbleColorAI">AI气泡颜色</label>
        <input id="bubbleColorAI" type="color" class="color-picker" value="#F0FFF0" />
      </div>
      <div class="tool-section">
        <label for="fontColor">字体颜色</label>
        <input id="fontColor" type="color" class="color-picker" value="#333333" />
      </div>
      <button id="applyColorBtn">应用颜色</button>
      <!-- 清空聊天窗口 -->
      <button id="clearChatBtn">清空聊天窗口</button>
      <!-- 聊天记录 -->
      <div class="history-list">
        <div style="font-weight: bold; margin-bottom: 8px;">聊天记录</div>
        <div id="historyContainer"></div>
      </div>
    </div>
  </div>
  <!-- 聊天区 -->
  <div id="chatContainer" class="chat-container"></div>
  <!-- 底部输入框 -->
  <div class="chat-input">
    <input id="userInput" type="text" placeholder="请输入内容..." />
    <button id="sendBtn">发送</button>
  </div>
  <script>
    /************************************************************
    * 全局变量 & DOM 获取
    ************************************************************/
    let apiKey = "";
    let memoryLimit = 10; // 默认记忆条数
    const KEY_STORAGE = "DEEPSEEK_CHAT_HISTORY";
    const COLOR_STORAGE = "DEEPSEEK_COLOR_CONFIG";
    const toolboxToggle = document.getElementById("toolboxToggle");
    const toolboxContainer = document.getElementById("toolboxContainer");
    const apiKeyInput = document.getElementById("apiKeyInput");
    const setKeyBtn = document.getElementById("setKeyBtn");
    const memoryLimitInput = document.getElementById("memoryLimit");
    const setMemoryBtn = document.getElementById("setMemoryBtn");
    const clearChatBtn = document.getElementById("clearChatBtn");
    const chatContainer = document.getElementById("chatContainer");
    const userInput = document.getElementById("userInput");
    const sendBtn = document.getElementById("sendBtn");
    const historyContainer = document.getElementById("historyContainer");
    // 自定义颜色相关
    const desktopColorInput = document.getElementById("desktopColor");
    const bubbleColorUserInput = document.getElementById("bubbleColorUser");
    const bubbleColorAIInput = document.getElementById("bubbleColorAI");
    const fontColorInput = document.getElementById("fontColor");
    const applyColorBtn = document.getElementById("applyColorBtn");
    // 全屏按钮
    const fullscreenToggle = document.getElementById("fullscreenToggle");
    // 聊天记录数组：[{role: "user"|"ai", content: "xxx", time: 167xxx }]
    let chatHistory = [];
    // 颜色配置（在本地缓存/读取）
    let colorConfig = {
      desktopColor: "#FFFFFF",
      bubbleColorUser: "#FFF0F0",
      bubbleColorAI: "#F0FFF0",
      fontColor: "#333333"
    };

    /************************************************************
    * 页面初始化
    ************************************************************/
    window.addEventListener("DOMContentLoaded", () => {
      // 从 localStorage 加载聊天记录
      const saved = localStorage.getItem(KEY_STORAGE);
      if (saved) {
        try {
          chatHistory = JSON.parse(saved);
        } catch (e) {
          console.warn("解析本地聊天记录失败：", e);
        }
      }
      // 从 localStorage 加载颜色配置
      const savedColor = localStorage.getItem(COLOR_STORAGE);
      if (savedColor) {
        try {
          colorConfig = JSON.parse(savedColor);
        } catch (e) {
          console.warn("解析本地颜色配置失败：", e);
        }
      }
      // 渲染历史 & 更新界面颜色
      renderChatFromHistory();
      renderHistoryList();
      applyColorConfig(); // 应用颜色设置到界面
      // 同步颜色选择器
      desktopColorInput.value = colorConfig.desktopColor;
      bubbleColorUserInput.value = colorConfig.bubbleColorUser;
      bubbleColorAIInput.value = colorConfig.bubbleColorAI;
      fontColorInput.value = colorConfig.fontColor;
    });

    /************************************************************
    * 顶栏相关按钮
    ************************************************************/
    // 全屏
    fullscreenToggle.addEventListener("click", () => {
      if (!document.fullscreenElement) {
        document.documentElement.requestFullscreen();
      } else if (document.exitFullscreen) {
        document.exitFullscreen();
      }
    });
    // 显示 / 隐藏工具箱
    toolboxToggle.addEventListener("click", () => {
      toolboxContainer.classList.toggle("show");
    });

    /************************************************************
    * 工具箱交互
    ************************************************************/
    // 设置 Key
    setKeyBtn.addEventListener("click", () => {
      const keyVal = apiKeyInput.value.trim();
      if (!keyVal) {
        alert("请输入有效的 DeepSeek Key");
        return;
      }
      apiKey = keyVal;
      alert("Key 设置成功！");
    });
    // 设置记忆条数
    setMemoryBtn.addEventListener("click", () => {
      const val = parseInt(memoryLimitInput.value, 10);
      if (isNaN(val) || val < 1) {
        alert("请输入正确的记忆条数");
        return;
      }
      memoryLimit = val;
      alert(`记忆条数已设置为：${memoryLimit}`);
    });
    // 应用颜色
    applyColorBtn.addEventListener("click", () => {
      colorConfig.desktopColor = desktopColorInput.value;
      colorConfig.bubbleColorUser = bubbleColorUserInput.value;
      colorConfig.bubbleColorAI = bubbleColorAIInput.value;
      colorConfig.fontColor = fontColorInput.value;
      // 应用到页面
      applyColorConfig();
      // 保存到 localStorage
      localStorage.setItem(COLOR_STORAGE, JSON.stringify(colorConfig));
    });
    // 清空聊天窗口（不删除历史）
    clearChatBtn.addEventListener("click", () => {
      chatContainer.innerHTML = "";
    });

    /************************************************************
    * 发送消息
    ************************************************************/
    sendBtn.addEventListener("click", async () => {
      const content = userInput.value.trim();
      if (!content) return;
      if (!apiKey) {
        alert("请先在工具箱中设置 DeepSeek Key！");
        return;
      }
      // 先渲染用户消息
      addMessage(content, "user");
      userInput.value = "";
      // 构建带有“如果回答中涉及数学公式，请使用 $...$ 或 $$...$$ 标记”的 system 提示
      const systemPrompt = "You are a helpful assistant. If your answer involves math formulas, please use $...$ or $$...$$ to mark the formula.";
      // 将最近 memoryLimit 条对话一起发送给 DeepSeek
      const messagesToSend = buildChatMessages(systemPrompt, content);
      // 向 DeepSeek 发送请求
      try {
        const response = await fetch("https://api.deepseek.com/v1/chat/completions", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            "Authorization": `Bearer ${apiKey}`
          },
          body: JSON.stringify({
            model: "deepseek-chat",
            messages: messagesToSend
          })
        });
        if (!response.ok) {
          throw new Error(`请求失败，状态码: ${response.status}`);
        }
        const result = await response.json();
        let aiReply = result?.choices?.[0]?.message?.content || "AI 未返回有效的消息。";
        // 去除多余符号：★☆*# 等
        aiReply = aiReply.replace(/[★☆*#]/g, "");
        // 逐字打字
        addMessage(aiReply, "ai", true);
      } catch (err) {
        console.error(err);
        addMessage("请求失败，请检查 Key、网络或模型。", "ai");
      }
    });

    /************************************************************
    * 构建聊天上下文
    ************************************************************/
    function buildChatMessages(systemPrompt, lastUserContent) {
      // 1. 先放一个系统提示
      const messages = [
        { role: "system", content: systemPrompt }
      ];
      // 2. 获取最近 memoryLimit 条非 system 消息（含 user + ai）
      const relevantMsgs = [];
      for (let i = chatHistory.length - 1; i >= 0 && relevantMsgs.length < memoryLimit; i--) {
        const msg = chatHistory[i];
        if (msg.role === "user") {
          relevantMsgs.push({ role: "user", content: msg.content });
        } else if (msg.role === "ai") {
          relevantMsgs.push({ role: "assistant", content: msg.content });
        }
      }
      // 取完后要反转一下顺序（因为我们是从后往前取）
      relevantMsgs.reverse();
      // 3. 把这些消息推到 messages 数组中
      messages.push(...relevantMsgs);
      // 4. 最后再加上本次用户输入
      messages.push({ role: "user", content: lastUserContent });
      return messages;
    }

    /************************************************************
    * 添加消息到屏幕 & 保存记录
    ************************************************************/
    function addMessage(text, role, typed = false) {
      // 存到 chatHistory
      const messageData = { role, content: text, time: Date.now() };
      chatHistory.push(messageData);
      // 渲染当前消息到屏幕
      renderSingleMessage(role, text, typed);
      // 保存到 localStorage
      localStorage.setItem(KEY_STORAGE, JSON.stringify(chatHistory));
      // 重新渲染聊天记录列表
      renderHistoryList();
    }

    /************************************************************
    * 在屏幕渲染单条消息（可选 逐字打字 + MathJax 渲染）
    ************************************************************/
    function renderSingleMessage(role, text, typed) {
      const wrapper = document.createElement("div");
      wrapper.classList.add("message-wrapper", role === "user" ? "wrapper-user" : "wrapper-ai");

      const msgDiv = document.createElement("div");
      msgDiv.classList.add("chat-message");

      // 文字部分
      const textSpan = document.createElement("div");
      textSpan.classList.add("message-text");

      // 处理消息内容，识别代码块
      const contentResult = processMessageText(text);
      textSpan.innerHTML = contentResult.html;

      // 图标按钮区域
      const actionsDiv = document.createElement("div");
      actionsDiv.classList.add("message-actions");

      // 复制按钮
      const copyBtn = document.createElement("button");
      copyBtn.classList.add("icon-btn");
      copyBtn.innerHTML = "📋"; // 剪贴板图标
      copyBtn.title = "复制";
      copyBtn.onclick = () => {
        navigator.clipboard.writeText(text)
          .then(() => alert("复制成功！"))
          .catch(() => alert("复制失败，请手动复制。"));
      };

      // 删除按钮
      const delBtn = document.createElement("button");
      delBtn.classList.add("icon-btn");
      delBtn.innerHTML = "🗑️"; // 垃圾桶图标
      delBtn.title = "删除";
      delBtn.onclick = () => {
        // 找到当前消息在 chatHistory 中的索引
        const index = chatHistory.findIndex(msg => msg.content === text && msg.role === role);
        if (index !== -1) {
          chatHistory.splice(index, 1);
          localStorage.setItem(KEY_STORAGE, JSON.stringify(chatHistory));
          renderHistoryList();
          renderChatFromHistory();
        }
      };

      actionsDiv.appendChild(copyBtn);
      actionsDiv.appendChild(delBtn);
      msgDiv.appendChild(textSpan);
      msgDiv.appendChild(actionsDiv);
      wrapper.appendChild(msgDiv);
      chatContainer.appendChild(wrapper);

      // 绑定运行代码按钮的事件
      contentResult.codeBlocks.forEach(block => {
        const btn = msgDiv.querySelector(`.run-code-btn[data-codeblockid="${block.id}"]`);
        if (btn) {
          btn.addEventListener('click', () => {
            runCode(block.language, block.code);
          });
        }
      });

      scrollToBottom();

      // 公式渲染
      if (window.MathJax) {
        MathJax.typesetPromise([textSpan]).catch(err => console.log(err));
      }
    }

    /************************************************************
    * 处理消息文本，识别代码块并生成对应的 HTML
    ************************************************************/
    function processMessageText(text) {
      const codeBlockRegex = /```(\w*)\n([\s\S]*?)```/g;
      let html = '';
      let lastIndex = 0;
      let match;
      let codeBlocks = [];

      while ((match = codeBlockRegex.exec(text)) !== null) {
        // Append text before code block
        html += escapeHtml(text.slice(lastIndex, match.index)).replace(/\n/g, '<br/>');

        // Code block
        const language = match[1] || 'plaintext';
        const code = match[2];

        // Unique ID for code block
        const codeBlockId = 'codeblock_' + codeBlocks.length;

        // Replace code block with formatted code and Run Code button
        html += `
          <pre><code>${escapeHtml(code)}</code></pre>
          <button class="run-code-btn" data-codeblockid="${codeBlockId}">运行代码</button>
        `;

        // Store code block
        codeBlocks.push({ id: codeBlockId, language, code });

        lastIndex = match.index + match[0].length;
      }

      // Append remaining text after last code block
      html += escapeHtml(text.slice(lastIndex)).replace(/\n/g, '<br/>');

      return { html, codeBlocks };
    }

    /************************************************************
    * 转义 HTML，防止 XSS
    ************************************************************/
    function escapeHtml(text) {
      return text.replace(/[&<>"']/g, function (m) {
        switch (m) {
          case '&':
            return '&amp;';
          case '<':
            return '&lt;';
          case '>':
            return '&gt;';
          case '"':
            return '&quot;';
          case "'":
            return '&#39;';
          default:
            return m;
        }
      });
    }

    /************************************************************
    * 运行代码并在模态框中显示结果
    ************************************************************/
    function runCode(language, code) {
      if (language.toLowerCase() === 'html') {
        // 创建模态框
        const modal = document.createElement('div');
        modal.classList.add('code-modal');
        modal.innerHTML = `
          <div class="code-modal-content">
            <span class="code-modal-close">&times;</span>
            <div class="code-modal-header">代码运行结果：</div>
            <div class="code-output-container"></div>
          </div>
        `;
        document.body.appendChild(modal);

        const closeModal = modal.querySelector('.code-modal-close');
        closeModal.addEventListener('click', () => {
          document.body.removeChild(modal);
        });

        // 在 iframe 中运行代码
        const outputContainer = modal.querySelector('.code-output-container');

        const iframe = document.createElement('iframe');
        iframe.sandbox = 'allow-scripts allow-same-origin';
        iframe.style.width = '100%';
        iframe.style.height = '100%';
        outputContainer.appendChild(iframe);

        const iframeDoc = iframe.contentDocument || iframe.contentWindow.document;
        iframeDoc.open();
        iframeDoc.write(code);
        iframeDoc.close();
      } else {
        alert('目前仅支持运行 HTML 代码。');
      }
    }

    /************************************************************
    * 从历史记录渲染到屏幕
    ************************************************************/
    function renderChatFromHistory() {
      chatContainer.innerHTML = "";
      chatHistory.forEach(msg => {
        // 不渲染 system 的消息
        if (msg.role === "system") return;
        renderSingleMessage(msg.role, msg.content, false);
      });
    }

    /************************************************************
    * 聊天记录：渲染到“工具箱”
    ************************************************************/
    function renderHistoryList() {
      historyContainer.innerHTML = "";
      chatHistory.forEach((item, idx) => {
        if (item.role === "system") return;
        const row = document.createElement("div");
        row.classList.add("history-item");
        const t = new Date(item.time);
        const timeStr = t.toLocaleTimeString("zh-CN", { hour12: false })
          + " " + t.toLocaleDateString("zh-CN");
        const shortContent = item.content.length > 30
          ? item.content.slice(0, 30) + "..."
          : item.content;
        row.innerHTML = `
          <div style="flex:1;">
          [${item.role === "user" ? "用户" : "AI"}] ${escapeHtml(shortContent)}
          <div style="font-size:12px;color:#999;">${timeStr}</div>
          </div>
        `;
        const delBtn = document.createElement("button");
        delBtn.textContent = "删除";
        delBtn.onclick = () => {
          chatHistory.splice(idx, 1);
          localStorage.setItem(KEY_STORAGE, JSON.stringify(chatHistory));
          renderHistoryList();
          renderChatFromHistory();
        };
        row.appendChild(delBtn);
        historyContainer.appendChild(row);
      });
    }

    /************************************************************
    * 滚动到底部
    ************************************************************/
    function scrollToBottom() {
      chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    /************************************************************
    * 应用颜色配置
    ************************************************************/
    function applyColorConfig() {
      // 桌面背景
      chatContainer.style.backgroundColor = colorConfig.desktopColor;
      // 所有聊天气泡
      const allMessages = document.querySelectorAll(".chat-message");
      allMessages.forEach(msgDiv => {
        // 判断是 user 还是 ai
        const wrapper = msgDiv.parentElement;
        if (wrapper.classList.contains("wrapper-user")) {
          msgDiv.style.backgroundColor = colorConfig.bubbleColorUser;
        } else {
          msgDiv.style.backgroundColor = colorConfig.bubbleColorAI;
        }
        msgDiv.style.color = colorConfig.fontColor;
      });
    }
  </script>
</body>

</html>

