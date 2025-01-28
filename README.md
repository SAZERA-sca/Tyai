<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Chat Interface</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            background-color: #f0f2f5;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .chat-container {
            max-width: 800px;
            margin: 20px auto;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            padding: 20px;
        }

        .header {
            padding: 15px;
            background: #ffffff;
            border-bottom: 1px solid #e0e0e0;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .new-chat-btn {
            padding: 8px 16px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
        }

        .new-chat-btn:hover {
            background-color: #0056b3;
        }

        #messages {
            flex-grow: 1;
            overflow-y: auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .message {
            padding: 10px 15px;
            border-radius: 15px;
            max-width: 80%;
            word-wrap: break-word;
        }

        .user-message {
            background-color: #007bff;
            color: white;
            align-self: flex-end;
        }

        .ai-message {
            background-color: #e9ecef;
            color: black;
            align-self: flex-start;
        }

        .input-container {
            padding: 20px;
            background: #ffffff;
            border-top: 1px solid #e0e0e0;
        }

        #userInput {
            width: 100%;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
            outline: none;
        }

        #userInput:focus {
            border-color: #007bff;
        }

        .typing-indicator {
            display: none;
            padding: 10px 15px;
            background-color: #e9ecef;
            border-radius: 15px;
            color: #666;
            align-self: flex-start;
            font-style: italic;
        }
    </style>
</head>
<body>
    <div class="chat-container">
        <div class="header">
            <h1>AI Chat</h1>
            <button class="new-chat-btn">New Chat</button>
        </div>
        
        <div id="messages">
            <div class="typing-indicator" id="typingIndicator">
                AI is typing...
            </div>
        </div>
        
        <div class="input-container">
            <input type="text" id="userInput" placeholder="พิมพ์ข้อความของคุณที่นี่...">
        </div>
    </div>

    <script>
        // Your existing JavaScript code goes here
        const apiKey = '6a2757e0207562fe16afaa0f8d09706280f668b9c48b062ed9624082080283eb';
        const messagesContainer = document.getElementById('messages');
        const userInput = document.getElementById('userInput');
        const typingIndicator = document.getElementById('typingIndicator');

        userInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                sendMessage();
            }
        });

        async function sendMessage() {
            const userMessage = userInput.value.trim();
            if (!userMessage) return;

            // User message
            const userDiv = document.createElement('div');
            userDiv.classList.add('message', 'user-message');
            userDiv.textContent = userMessage;
            messagesContainer.appendChild(userDiv);

            // Clear input
            userInput.value = '';

            // Show typing indicator
            typingIndicator.style.display = 'block';

            try {
                const response = await fetch('https://api.together.xyz/v1/chat/completions', {
                    method: 'POST',
                    headers: {
                        'Authorization': `Bearer ${apiKey}`,
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        model: "meta-llama/Llama-3.3-70B-Instruct-Turbo-Free",
                        messages: [
                            {"role": "system", "content": "คุณเป็นผู้ช่วยแชทที่เป็นมิตรและมีประสิทธิภาพ"},
                            {"role": "user", "content": userMessage}
                        ],
                        max_tokens: 300,
                        temperature: 0.7
                    })
                });

                const data = await response.json();
                const botMessage = data.choices[0].message.content || 'ไม่มีการตอบกลับ';

                // Hide typing indicator
                typingIndicator.style.display = 'none';

                // AI message
                const botDiv = document.createElement('div');
                botDiv.classList.add('message', 'ai-message');
                botDiv.textContent = botMessage;
                messagesContainer.appendChild(botDiv);

                // Scroll to bottom
                messagesContainer.scrollTop = messagesContainer.scrollHeight;
            } catch (error) {
                // Hide typing indicator
                typingIndicator.style.display = 'none';

                const errorDiv = document.createElement('div');
                errorDiv.classList.add('message', 'ai-message');
                errorDiv.textContent = 'เกิดข้อผิดพลาด กรุณาลองใหม่อีกครั้ง';
                messagesContainer.appendChild(errorDiv);
                console.error('เกิดข้อผิดพลาด:', error);
            }
        }

        // New chat button functionality
        document.querySelector('.new-chat-btn').addEventListener('click', function() {
            messagesContainer.innerHTML = `
                <div class="typing-indicator" id="typingIndicator">
                    AI is typing...
                </div>
            `;
        });
    </script>
</body>
</html>
