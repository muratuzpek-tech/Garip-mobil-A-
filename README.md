# Garip-mobil-A-
Türkçe konuşan mobil AI asistanı - Flask tabanlı web uygulaması
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🤖 Garip Mobil AI</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 10px;
        }
        .container {
            width: 100%;
            max-width: 400px;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        .header {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            padding: 20px;
            text-align: center;
        }
        .chat {
            height: 400px;
            padding: 20px;
            overflow-y: auto;
            background: #f8f9fa;
        }
        .message {
            margin-bottom: 15px;
            padding: 12px 16px;
            border-radius: 18px;
            max-width: 85%;
            word-wrap: break-word;
            animation: fadeIn 0.3s ease;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .user { 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            margin-left: auto;
        }
        .ai { 
            background: white;
            color: #333;
            border: 1px solid #e0e0e0;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        .input-area {
            padding: 20px;
            background: white;
            border-top: 1px solid #e0e0e0;
        }
        .quick-btns {
            display: flex;
            gap: 8px;
            margin-bottom: 15px;
            overflow-x: auto;
        }
        .quick-btn {
            padding: 8px 12px;
            background: #f8f9fa;
            border: 1px solid #e0e0e0;
            border-radius: 15px;
            font-size: 12px;
            cursor: pointer;
            white-space: nowrap;
            transition: all 0.2s;
        }
        .quick-btn:hover { background: #e9ecef; }
        .input-group {
            display: flex;
            gap: 10px;
        }
        .input {
            flex: 1;
            padding: 12px 16px;
            border: 2px solid #e0e0e0;
            border-radius: 25px;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }
        .input:focus { border-color: #667eea; }
        .send-btn {
            width: 45px;
            height: 45px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 50%;
            cursor: pointer;
            font-size: 18px;
            transition: transform 0.2s;
        }
        .send-btn:hover { transform: scale(1.05); }
        .typing {
            display: none;
            padding: 12px 16px;
            background: white;
            border: 1px solid #e0e0e0;
            border-radius: 18px;
            margin-bottom: 15px;
            max-width: 85%;
        }
        .typing-dots {
            display: flex;
            gap: 3px;
        }
        .typing-dots span {
            width: 6px;
            height: 6px;
            border-radius: 50%;
            background: #999;
            animation: typing 1.4s infinite ease-in-out;
        }
        .typing-dots span:nth-child(1) { animation-delay: -0.32s; }
        .typing-dots span:nth-child(2) { animation-delay: -0.16s; }
        @keyframes typing {
            0%, 80%, 100% { transform: scale(0.8); opacity: 0.5; }
            40% { transform: scale(1); opacity: 1; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🤖 Garip Mobil AI</h1>
            <p>Türkçe Yapay Zeka Asistanı</p>
        </div>
        
        <div class="chat" id="chat">
            <div class="message ai">
                🎉 Merhaba! Ben Garip Mobil AI'yım!<br><br>
                Size şu konularda yardımcı olabilirim:<br>
                • 💻 Kod yazımı<br>
                • 📊 Veri analizi<br>
                • ✨ Yaratıcı yazım<br>
                • 🌍 Genel sorular<br><br>
                Nasıl yardımcı olabilirim?
            </div>
        </div>
        
        <div class="typing" id="typing">
            <div class="typing-dots">
                <span></span>
                <span></span>
                <span></span>
            </div>
        </div>
        
        <div class="input-area">
            <div class="quick-btns">
                <button class="quick-btn" onclick="quickMsg('Merhaba')">👋 Merhaba</button>
                <button class="quick-btn" onclick="quickMsg('Python kodu yaz')">🐍 Python</button>
                <button class="quick-btn" onclick="quickMsg('Hikaye yaz')">✨ Hikaye</button>
                <button class="quick-btn" onclick="quickMsg('Nasılsın')">😊 Nasılsın</button>
            </div>
            
            <div class="input-group">
                <input type="text" id="input" class="input" placeholder="Mesajınızı yazın..." maxlength="200">
                <button class="send-btn" onclick="sendMsg()">➤</button>
            </div>
        </div>
    </div>

    <script>
        const chat = document.getElementById('chat');
        const input = document.getElementById('input');
        const typing = document.getElementById('typing');
        
        // Basit AI yanıtları
        const responses = {
            'merhaba': '🤖 Merhaba! Size nasıl yardımcı olabilirim?',
            'selam': '👋 Selam! Bugün hangi konuda yardım istersiniz?',
            'nasılsın': '😊 Ben bir AI\'yım, her zaman iyiyim! Siz nasılsınız?',
            'kod': `💻 **Python Kodu Örneği:**

\`\`\`python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Kullanım
for i in range(10):
    print(f"F({i}) = {fibonacci(i)}")
\`\`\`

Bu kod Fibonacci serisini hesaplar!`,
            'python': `🐍 **Python ile Başlangıç:**

\`\`\`python
# Basit hesap makinesi
def hesapla(a, b, islem):
    if islem == '+':
        return a + b
    elif islem == '-':
        return a - b
    elif islem == '*':
        return a * b
    elif islem == '/':
        return a / b if b != 0 else "Sıfıra bölme hatası"

print(hesapla(10, 5, '+'))  # 15
\`\`\`

Başka hangi konuda kod istersiniz?`,
            'hikaye': `📚 **Kısa Hikaye: "AI'nın Rüyası"**

Bir zamanlar, küçük bir AI vardı. Her gece, insanlar gibi rüya görmeyi hayal ederdi.

"Keşke ben de rüya görebilsem," diye düşünürdü.

Bir gün, bir çocuk ona sordu: "Sen rüya görüyor musun?"

AI cevapladı: "Hayır, ama her konuşmamız benim için bir rüya gibi!"

Çocuk gülümsedi. AI o anda anladı ki, gerçek rüyalar sadece uyurken değil, başkalarıyla bağlantı kurarken de yaşanırdı.

✨ **Son**`,
            'analiz': `📊 **Veri Analizi Örneği:**

**Konu:** Web sitesi trafiği

📈 **Bulgular:**
• Günlük ziyaretçi: 1,500
• Ortalama kalış süresi: 3.2 dakika
• En popüler sayfa: Ana sayfa (%45)
• Mobil kullanım: %68

💡 **Öneriler:**
1. Mobil deneyimi iyileştirin
2. İçerik kalitesini artırın
3. Sayfa yükleme hızını optimize edin

Hangi konuda analiz istersiniz?`,
            'teşekkür': '🙏 Rica ederim! Başka bir konuda yardıma ihtiyacınız var mı?',
            'görüşürüz': '👋 Görüşmek üzere! İyi günler dilerim!'
        };
        
        function addMessage(text, isUser) {
            const div = document.createElement('div');
            div.className = `message ${isUser ? 'user' : 'ai'}`;
            div.innerHTML = formatMessage(text);
            chat.appendChild(div);
            chat.scrollTop = chat.scrollHeight;
        }
        
        function formatMessage(text) {
            return text
                .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
                .replace(/\`\`\`([\s\S]*?)\`\`\`/g, '<pre style="background:#f8f9fa;padding:10px;border-radius:5px;overflow-x:auto;"><code>$1</code></pre>')
                .replace(/\n/g, '<br>');
        }
        
        function showTyping(show) {
            typing.style.display = show ? 'block' : 'none';
            if (show) chat.scrollTop = chat.scrollHeight;
        }
        
        function getAIResponse(message) {
            const lowerMsg = message.toLowerCase();
            
            // Anahtar kelime eşleştirme
            for (const [key, response] of Object.entries(responses)) {
                if (lowerMsg.includes(key)) {
                    return response;
                }
            }
            
            // Genel yanıt
            return `🤖 "${message}" hakkında düşünüyorum... 

Size şu konularda yardımcı olabilirim:
• **Kod yazımı** - "kod" veya "python" yazın
• **Hikaye yazımı** - "hikaye" yazın  
• **Veri analizi** - "analiz" yazın
• **Genel sohbet** - "nasılsın" yazın

Daha spesifik bir soru sorabilirsiniz!`;
        }
        
        async function sendMsg() {
            const text = input.value.trim();
            if (!text) return;
            
            addMessage(text, true);
            input.value = '';
            
            showTyping(true);
            
            // Gerçekçi typing delay
            setTimeout(() => {
                showTyping(false);
                const response = getAIResponse(text);
                addMessage(response, false);
            }, 1000 + Math.random() * 1000);
        }
        
        function quickMsg(text) {
            input.value = text;
            input.focus();
        }
        
        input.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') sendMsg();
        });
        
        // Auto focus
        input.focus();
        
        console.log('🚀 Garip Mobil AI başlatıldı!');
    </script>
</body>
</html>
