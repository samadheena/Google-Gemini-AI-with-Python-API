# Google-Gemini-AI-with-Python-API
<center><a href="https://www.pieriantraining.com/" ><img src="../PTCenteredPurple.png" alt="Pierian Training Logo" /></a></center>


# Chat Conversations
Gemini also has the ability to carry on a conversation, where you can send messages and have a history of replies, so that Gemini can have context.

import google.generativeai as genai
genai.configure(api_key=api_key)

# Note: The vision model gemini-pro-vision is not optimized for multi-turn chat! Its made more for one-shot questions.
model = genai.GenerativeModel('gemini-pro')

Let's initiate the chat with no historical messages.

chat = model.start_chat()

Sending a message can be achieved using `response = chat.send_message(message)`

response = chat.send_message("Hi! I'm planning a trip to Paris, could you help me plan some activities?")

print(response.text)

---

Note however, since we initiated a chat, we have a full history of our prompts and Gemini Replies and we can reference them in an ongoing conversation.
To view the entire history, you can call `chat.history`.

for item in chat.history:
    print(item)
    print('\n\n')

type(item)

item.role

item.parts[0].text

### Continue the conversation

To continue the conversation, we simply call `.send_message()` again:

response = chat.send_message("Give me more details about that last point")

print(response.text)

print(chat.history)

## Stream Reply

Since tokens are generated on the fly, you could also  directly grab the chunkcs as the come in:

response = chat.send_message("Wasn't there a movie or musical about this?", stream=True)

for chunk in response:
    print(chunk.text)

## Token Count

You can easily count tokens as well:

model.count_tokens("hello lightness my old friend...I've come to laugh with you again")

