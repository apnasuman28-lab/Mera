import { useState, useRef, useEffect } from 'react';
import { Button } from "/components/ui/button";
import { Input } from "/components/ui/input";
import { Card, CardContent, CardHeader, CardTitle } from "/components/ui/card";

const ChatWithMe = () => {
  const [messages, setMessages] = useState<Array<{text: string, sender: string, timestamp: Date}>>([]);
  const [inputMessage, setInputMessage] = useState('');
  const [isTyping, setIsTyping] = useState(false);
  const messagesEndRef = useRef<HTMLDivElement>(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const handleSendMessage = async () => {
    if (inputMessage.trim() === '') return;

    // Add user message
    const userMessage = {
      text: inputMessage,
      sender: 'User',
      timestamp: new Date()
    };
    
    setMessages(prev => [...prev, userMessage]);
    setInputMessage('');
  };

  const handleKeyPress = (e: React.KeyboardEvent) => {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSendMessage();
    }
  };

  const formatTime = (date: Date) => {
    return date.toLocaleTimeString('en-IN', { 
      hour: '2-digit', 
      minute: '2-digit',
      hour12: true 
    });
  };

  return (
    <div className="min-h-screen bg-background p-4 md:p-6">
      <div className="max-w-4xl mx-auto">
        <Card className="w-full h-[80vh] flex flex-col">
          <CardHeader className="border-b border-border">
            <CardTitle className="text-center text-primary">
              सार्वजनिक चैट
            </CardTitle>
            <p className="text-sm text-muted-foreground text-center">
              यहां सभी के संदेश दिखाई देंगे और कोई भी संदेश भेज सकता है
            </p>
          </CardHeader>
          
          <CardContent className="flex-1 overflow-hidden p-0">
            <div className="h-full flex flex-col">
              {/* Messages container */}
              <div className="flex-1 overflow-y-auto p-4 space-y-4">
                {messages.length === 0 ? (
                  <div className="text-center text-muted-foreground py-8">
                    <div className="w-16 h-16 mx-auto mb-4 rounded-full bg-primary/10 flex items-center justify-center">
                      <img 
                        src="https://placeholder-image-service.onrender.com/image/64x64?prompt=Friendly%20AI%20assistant%20avatar%20with%20welcoming%20expression&id=chat-avatar-1" 
                        alt="Friendly AI assistant avatar with welcoming expression and smile"
                        className="w-12 h-12 rounded-full"
                      />
                    </div>
                    <p className="text-lg font-medium">सार्वजनिक चैट रूम</p>
                    <p className="text-sm">यहां आप संदेश भेज सकते हैं और सभी के संदेश देख सकते हैं</p>
                  </div>
                ) : (
                  messages.map((message, index) => (
                    <div
                      key={index}
                      className={`flex ${message.sender === 'user' ? 'justify-end' : 'justify-start'}`}
                    >
                      <div
                        className={`max-w-xs md:max-w-md lg:max-w-lg px-4 py-2 rounded-lg ${
                          message.sender === 'user'
                            ? 'bg-primary text-primary-foreground'
                            : 'bg-secondary text-secondary-foreground'
                        }`}
                      >
                        <p className="text-sm">{message.text}</p>
                        <p className={`text-xs mt-1 ${
                          message.sender === 'user' ? 'text-primary-foreground/70' : 'text-secondary-foreground/70'
                        }`}>
                          {formatTime(message.timestamp)}
                        </p>
                      </div>
                    </div>
                  ))
                )}
                
                
                
                <div ref={messagesEndRef} />
              </div>

              {/* Input area */}
              <div className="border-t border-border p-4">
                <div className="flex space-x-2">
                  <Input
                    value={inputMessage}
                    onChange={(e) => setInputMessage(e.target.value)}
                    onKeyPress={handleKeyPress}
                    placeholder="अपना संदेश यहां टाइप करें..."
                    className="flex-1"
                    disabled={isTyping}
                  />
                  <Button
                    onClick={handleSendMessage}
                    disabled={isTyping || inputMessage.trim() === ''}
                    className="whitespace-nowrap"
                  >
                    भेजें
                  </Button>
                </div>
              </div>
            </div>
          </CardContent>
        </Card>

        <div className="mt-4 text-center text-sm text-muted-foreground">
          <p>इस लिंक को शेयर करके कोई भी इस चैट रूम में संदेश भेज और देख सकता है</p>
        </div>
      </div>
    </div>
  );
};

export default ChatWithMe;
