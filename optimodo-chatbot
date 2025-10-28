// Optimodo Chat Widget
(function() {
    const styles = `
        .n8n-chat-widget {
            --chat--color-primary: var(--n8n-chat-primary-color, #854fff);
            --chat--color-secondary: var(--n8n-chat-secondary-color, #6b3fd4);
            --chat--color-background: var(--n8n-chat-background-color, #ffffff);
            --chat--color-font: var(--n8n-chat-font-color, #333333);
            font-family: 'Geist Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Open Sans', sans-serif;
        }
        .n8n-chat-widget .chat-container { position: fixed; bottom: 20px; right: 20px; z-index: 1000; display: none; width: 380px; height: 600px; background: var(--chat--color-background); border-radius: 12px; box-shadow: 0 8px 32px rgba(133,79,255,0.15); border: 1px solid rgba(133,79,255,0.2); overflow: hidden; font-family: inherit; }
        .n8n-chat-widget .chat-container.open { display: flex; flex-direction: column; }
        .n8n-chat-widget .brand-header { padding: 16px; display: flex; align-items: center; gap: 12px; border-bottom: 1px solid rgba(133,79,255,0.1); position: relative; }
        .n8n-chat-widget .close-button { position: absolute; right: 16px; top: 50%; transform: translateY(-50%); background: none; border: none; color: var(--chat--color-font); cursor: pointer; font-size: 20px; opacity: 0.6; }
        .n8n-chat-widget .close-button:hover { opacity: 1; }
        .n8n-chat-widget .chat-interface { display: none; flex-direction: column; height: 100%; }
        .n8n-chat-widget .chat-interface.active { display: flex; }
        .n8n-chat-widget .chat-messages { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; }
        .n8n-chat-widget .chat-message { padding: 12px 16px; margin: 8px 0; border-radius: 12px; max-width: 80%; word-wrap: break-word; font-size: 14px; line-height: 1.5; }
        .n8n-chat-widget .chat-message.user { background: linear-gradient(135deg, var(--chat--color-primary) 0%, var(--chat--color-secondary) 100%); color: white; align-self: flex-end; }
        .n8n-chat-widget .chat-message.bot { background: var(--chat--color-background); border: 1px solid rgba(133,79,255,0.2); color: var(--chat--color-font); align-self: flex-start; }
        .n8n-chat-widget .chat-buttons { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 6px; }
        .n8n-chat-widget .chat-button { padding: 8px 14px; border-radius: 8px; background: linear-gradient(135deg, var(--chat--color-primary), var(--chat--color-secondary)); color: #fff; border: none; cursor: pointer; font-size: 13px; transition: transform 0.15s; }
        .n8n-chat-widget .chat-button:hover { transform: translateY(-2px); }
        .n8n-chat-widget .chat-input { padding: 16px; border-top: 1px solid rgba(133,79,255,0.1); display: flex; gap: 8px; }
        .n8n-chat-widget .chat-input textarea { flex: 1; padding: 12px; border: 1px solid rgba(133,79,255,0.2); border-radius: 8px; resize: none; font-size: 14px; }
        .n8n-chat-widget .chat-toggle { position: fixed; bottom: 20px; right: 20px; width: 60px; height: 60px; border-radius: 30px; background: linear-gradient(135deg, var(--chat--color-primary) 0%, var(--chat--color-secondary) 100%); color: white; border: none; cursor: pointer; box-shadow: 0 4px 12px rgba(133,79,255,0.3); display: flex; align-items: center; justify-content: center; transition: transform 0.3s; }
        .n8n-chat-widget .chat-toggle:hover { transform: scale(1.05); }
        .n8n-chat-widget .chat-toggle svg { width: 24px; height: 24px; fill: currentColor; }
    `;

    // Load font
    const fontLink = document.createElement('link');
    fontLink.rel = 'stylesheet';
    fontLink.href = 'https://cdn.jsdelivr.net/npm/geist@1.0.0/dist/fonts/geist-sans/style.css';
    document.head.appendChild(fontLink);

    // Inject styles
    const styleSheet = document.createElement('style');
    styleSheet.textContent = styles;
    document.head.appendChild(styleSheet);

    const config = window.ChatWidgetConfig || {};
    if (window.OptimodoChatWidgetInitialized) return;
    window.OptimodoChatWidgetInitialized = true;

    let currentSessionId = crypto.randomUUID();

    const widgetContainer = document.createElement('div');
    widgetContainer.className = 'n8n-chat-widget';

    const chatContainer = document.createElement('div');
    chatContainer.className = `chat-container${config.style?.position === 'left' ? ' position-left' : ''}`;

    chatContainer.innerHTML = `
        <div class="brand-header">
            <img src="${config.branding?.logo || ''}" alt="${config.branding?.name || 'Chat'}">
            <span>${config.branding?.name || 'Chat'}</span>
            <button class="close-button">×</button>
        </div>
        <div class="new-conversation">
            <h2 class="welcome-text">${config.branding?.welcomeText || '👋 Willkommen!'}</h2>
            <button class="new-chat-btn">Chat starten</button>
            <p class="response-text">${config.branding?.responseTimeText || ''}</p>
        </div>
        <div class="chat-interface">
            <div class="brand-header">
                <img src="${config.branding?.logo || ''}" alt="${config.branding?.name || 'Chat'}">
                <span>${config.branding?.name || 'Chat'}</span>
                <button class="close-button">×</button>
            </div>
            <div class="chat-messages"></div>
            <div class="chat-input">
                <textarea placeholder="Nachricht eingeben..." rows="1"></textarea>
                <button type="submit">Senden</button>
            </div>
        </div>
    `;

    const toggleButton = document.createElement('button');
    toggleButton.className = `chat-toggle${config.style?.position === 'left' ? ' position-left' : ''}`;
    toggleButton.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 2C6.477 2 2 6.477 2 12c0 1.821.487 3.53 1.338 5L2.5 21.5l4.5-.838A9.955 9.955 0 0012 22c5.523 0 10-4.477 10-10S17.523 2 12 2z"/></svg>`;

    widgetContainer.appendChild(chatContainer);
    widgetContainer.appendChild(toggleButton);
    document.body.appendChild(widgetContainer);

    const newChatBtn = chatContainer.querySelector('.new-chat-btn');
    const chatInterface = chatContainer.querySelector('.chat-interface');
    const messagesContainer = chatContainer.querySelector('.chat-messages');
    const textarea = chatContainer.querySelector('textarea');
    const sendButton = chatContainer.querySelector('button[type="submit"]');

    // Funktion: Botnachricht mit optionalen Buttons
    function addBotMessage(text, choices = []) {
        const botDiv = document.createElement('div');
        botDiv.className = 'chat-message bot';
        botDiv.textContent = text;
        messagesContainer.appendChild(botDiv);

        if (choices.length > 0) {
            const btnContainer = document.createElement('div');
            btnContainer.className = 'chat-buttons';
            choices.forEach(choice => {
                const btn = document.createElement('button');
                btn.className = 'chat-button';
                btn.textContent = choice;
                btn.onclick = () => sendMessage(choice);
                btnContainer.appendChild(btn);
            });
            messagesContainer.appendChild(btnContainer);
        }

        messagesContainer.scrollTop = messagesContainer.scrollHeight;
    }

    async function startNewConversation() {
        currentSessionId = crypto.randomUUID();
        try {
            const res = await fetch(config.webhook?.url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify([{ action: "start", sessionId: currentSessionId }])
            });
            const data = await res.json();
            chatContainer.querySelector('.new-conversation').style.display = 'none';
            chatInterface.classList.add('active');
            addBotMessage(data.output || '👋 Hallo! Wie kann ich dir helfen?', data.choices || []);
        } catch (err) { console.error(err); }
    }

    async function sendMessage(message) {
        const userDiv = document.createElement('div');
        userDiv.className = 'chat-message user';
        userDiv.textContent = message;
        messagesContainer.appendChild(userDiv);
        messagesContainer.scrollTop = messagesContainer.scrollHeight;

        try {
            const res = await fetch(config.webhook?.url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ action: "sendMessage", sessionId: currentSessionId, chatInput: message })
            });
            const data = await res.json();
            addBotMessage(data.output || '', data.choices || []);
        } catch (err) { console.error(err); }
    }

    newChatBtn.addEventListener('click', startNewConversation);
    sendButton.addEventListener('click', () => {
        const msg = textarea.value.trim();
        if (msg) { sendMessage(msg); textarea.value = ''; }
    });
    textarea.addEventListener('keypress', e => {
        if (e.key === 'Enter' && !e.shiftKey) {
            e.preventDefault();
            const msg = textarea.value.trim();
            if (msg) { sendMessage(msg); textarea.value = ''; }
        }
    });
    toggleButton.addEventListener('click', () => chatContainer.classList.toggle('open'));
    chatContainer.querySelectorAll('.close-button').forEach(btn => btn.addEventListener('click', () => chatContainer.classList.remove('open')));
})();
