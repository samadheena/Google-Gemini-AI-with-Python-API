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




<center><a href="https://www.pieriantraining.com/" ><img src="../PTCenteredPurple.png" alt="Pierian Training Logo" /></a></center>


# Generating Text

Let's explore how to send a simple prompt and get Gemini to return text!

import os

api_key = os.environ['GEMINI_API_KEY'] 

import google.generativeai as genai
genai.configure(api_key=api_key)

for m in genai.list_models():
    if 'generateContent' in m.supported_generation_methods:
        print(m.name)

# `gemini-pro` Text and Single Response API Call
To start, let's ask a simple question. First, we'll create a `GenerativeModel` object, which tells Gemini which model to use. For text-based questions and answers, as well as chats, we'll use **gemini-pro**, a model designed for general *text-based* tasks.

model = genai.GenerativeModel('gemini-pro')

To communicate our prompts to Gemini's backend, we'll utilize the `model.generate_content('prompt')` function. This function handles diverse prompt types and use cases, such as text-only, text+image, and chatbot interactions, depending on the model linked to it.

You can find the full documentation of `model.generate_content()` [here](https://ai.google.dev/api/python/google/generativeai/GenerativeModel).

prompt = "What's the capital of the United States?"
response = model.generate_content(prompt)

type(response)

In this case the result can be accessed via `reponse.text`. The status is stored within `response.prompt_feedback`.<br />
If multiple responses have beed created you can inspect them using `response.candidates`

print(response.text)

print(response.prompt_feedback)

print(response.candidates)

____

Let's try another, more creative prompt.

prompt = "Write a rap about Claude Shannon with lots of esoteric references, but make sure it rhymes"
response = model.generate_content(prompt)
print(response.text)

----
Remember to save time, you could make this a function yourself:

def gemini_response(prompt):
    response = model.generate_content(prompt)
    return response.text


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

<center><a href="https://www.pieriantraining.com/" ><img src="../PTCenteredPurple.png" alt="Pierian Training Logo" /></a></center>


# Generation Configuration (Text Generation Parameters)

You can pass in configuration values to further refine the type of output you will get.

Let's set up an easy to use function to explore these concepts:

api_key = ''

import google.generativeai as genai
genai.configure(api_key=api_key)

model = genai.GenerativeModel('gemini-pro')

## The Generation Configuration Object

You can easily supply arguments to a geneartion configuration object:

config = genai.types.GenerationConfig(temperature=1.0,max_output_tokens=2000,candidate_count=1)

def get_response(prompt, generation_config={}):
    response = model.generate_content(contents=prompt,generation_config=generation_config)
    return response

result = get_response("Tell me a story about the Moon")
print(result.text)

result = get_response("Tell me a story about the Moon", generation_config= config)

result.text

**BE AWARE! Take careful notice how adding the configuration changes the object that is sent back! If you get a warning message read it carefully! For example, check out the warning below, it tells you how to access the information**

>
>> ValueError: The `response.text` quick accessor only works for simple (single-`Part`) text responses. This response is not simple text.Use the `result.parts` accessor or the full `result.candidates[index].content.parts` lookup instead.
>

result.candidates[0].content.parts[0].text

## Gemini LLM Configuration Parameters

### Temperature
In Gemini LLM, the `temperature` parameter plays a crucial role in the response generation process. It's instrumental during the sampling phase, particularly when `top_p` and `top_k` parameters are in effect. Essentially, `temperature` influences the randomness in token selection:

- **Low temperatures** are optimal for prompts necessitating deterministic, concise, and less creative responses.
- **High temperatures** foster diverse and creative outcomes, enhancing the model's response variability.

    - **Range**: `0.0 - 1.0`
    - **Default Settings**:
        - **gemini-pro**: `0.9`
        - **gemini-pro-vision**: `0.4`

config = genai.types.GenerationConfig(temperature=0.0)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

config = genai.types.GenerationConfig(temperature=1.0)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

### max_output_tokens
The `max_output_tokens` parameter defines the upper limit of tokens generated in a response. Notably, a token approximates four characters, translating to about 60-80 words for 100 tokens. Adjust this parameter based on the desired response length:

- **Lower values** lead to shorter responses.
- **Higher values** enable more extensive responses.

    - **Ranges**:
        - **gemini-pro**: `1-8192` (default: `8192`)
        - **gemini-pro-vision**: `1-2048` (default: `2048`)

config = genai.types.GenerationConfig(max_output_tokens=500)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

### top_k
`top_k` parameter influences the model's token selection strategy for generating outputs. It's a measure of how many of the most probable tokens are considered at each step:

- A **top_k of 1** implies a deterministic approach, choosing the most probable token.
- Higher **top_k values** introduce diversity, selecting from a broader range of probable tokens based on the set `temperature`.

    - **Range**: `1-40`
    - **Default Settings**:
        - **gemini-pro-vision**: `32`
        - **gemini-pro**: Not specified (none)

config = genai.types.GenerationConfig(top_k=1)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

config = genai.types.GenerationConfig(top_k=40)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

### top_p
The `top_p` parameter, akin to `top_k`, modifies the token selection process. It considers tokens from the most to least probable, cumulatively, until their probabilities match the `top_p` value. The model then selects the next token within this subset, guided by the `temperature` parameter:

- **Lower `top_p` values** lead to more predictable responses.
- **Higher `top_p` values** permit a wider array of potential responses, injecting randomness.

    - **Range**: `0.0 - 1.0`
    - **Default**: `1.0`

config = genai.types.GenerationConfig(top_p=0)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

config = genai.types.GenerationConfig(top_p=1)
result = get_response("Tell me about the United States",generation_config=config)
print(result.text)

### candidate_count
The `candidate_count` parameter determines the quantity of different response variations the model generates. For Gemini LLM, this value is fixed:

- **Required Value**: `1`

**IMPORTANT NOTE: AT THIS TIME, YOU ARE ONLY ALLOWED ONE CANDIDATE, BUT CHECK THE OFFICIAL DOCS FOR AN UPDATE IN THE FUTURE!**

config = genai.types.GenerationConfig(candidate_count=1)
result = get_response("Give me 3 top facts about the United States",generation_config=config)

print(result.text)

### stop_sequences
`stop_sequences` is a feature allowing the specification of strings that prompt the model to cease text generation. The response is truncated at the first occurrence of any listed string. This feature is sensitive to the case of the strings:

- **Usage Example**: If "Str" and "reverse" are in `stop_sequences`, the model stops generating text at their first appearance.
- **Limitation**: A maximum of 5 strings can be listed in `stop_sequences`.

config = genai.types.GenerationConfig(stop_sequences=['x','X'])
result = get_response("Give me a list of all the letters in the alphabet",generation_config=config)
print(result.text)

config = genai.types.GenerationConfig(stop_sequences=[])
result = get_response("Write a customer support email thanking the customer for reaching out. End it with 'Sincerely'",generation_config=config)
print(result.text)

config = genai.types.GenerationConfig(stop_sequences=['Sincerely'])
result = get_response("Write a customer support email thanking the customer for reaching out. End it with 'Sincerely'",generation_config=config)
print(result.text)

<center><a href="https://www.pieriantraining.com/" ><img src="../PTCenteredPurple.png" alt="Pierian Training Logo" /></a></center>


# Gemini API

In the world of artificial intelligence, language models have evolved to handle multimodal information, enabling them to understand and synthesize data from text and images. Google's Gemini API spearheads this advancement, introducing models that enable multimodal understanding and generation.

The Gemini API serves as the key to unlocking these models' capabilities, providing a toolkit for building AI applications.

Key Features of the Gemini API:

- Multimodal Understanding: Gemini models can process and comprehend information from text and images
- Generative Power: Gemini models excel at generating text, translating languages, writing different creative content formats, and answering your questions informatively.
- Versatility: Gemini models can be adapted to a wide range of applications, including chatbots, creative writing tools, and information retrieval systems.


---

**This Jupyter notebook will delve into getting setup with Gemini Python API, make sure to watch the video for full details, espeically on actually getting the API key value!**

## Installation of Python SDK

First up, you need to install the Python SDK for Google's Gemini models, note that this is a different Python SDK than previous Google LLMs such as PaLM. You can install it at the command line with:

    pip install google-generativeai
    
Or you can try running it directly in your notebook (keep in mind that in a virtual environment, you sometimes need to re-install Jupyter in that virtual environment to reconnect it to the libraries, you may also need to restart the kernel.

# !pip install google-generativeai

Confirm the installation by running the import:

import google.generativeai as genai

# API Key

You'll need to generate an API key for Gemini by going to: https://makersuite.google.com/app/apikey and creating a new API Key. Here is a screenshot:

<center><img src="ai_studio_snip.jpg"></center>

Eventually you should be able to get a key, it will look something like this: "XE4tlk8VrP9jsH7yLmQbDfNeZC5vYqx3WbTghJZ" (obviously this is not a real API key)

## API Key Troubleshooting Notes:

* It is important to note that you may need to allow access to "Early Access Apps" in order to get the API key. Here is a troubleshooting guide from Google explaining how to set-up access: https://ai.google.dev/tutorials/workspace_auth_quickstart

* If you get an error saying "The caller does not have permission" it is because you are only allowed ONE key PER project, you should see if you already have an existing project that Google made for you, often called something like "Generative Language Client", for more info: https://stackoverflow.com/questions/77762483/the-caller-does-not-have-permission-when-creating-api-key
----

## Saving and Setting your API Key

To securely save and reuse your API key in Jupyter Notebook using Python, you should avoid hardcoding it directly into your notebook. Instead, you can use environment variables or external files. Here are a couple of methods:

### Using Environment Variables

1. **Set the Environment Variable**: 
   - On Windows, use the command prompt to set an environment variable: 
     ```
     setx GEMINI_API_KEY "XE4tlk8VrP9jsH7yLmQbDfNeZC5vYqx3WbTghJZ"
     ```
   - On macOS/Linux, use the terminal:
     ```
     export GEMINI_API_KEY="XE4tlk8VrP9jsH7yLmQbDfNeZC5vYqx3WbTghJZ"
     ```
   - Note: After setting this, you might need to restart your Jupyter Notebook for the changes to take effect.
   - You can technically also do this via Python's os library:
   
     ```python
     import os
     os.environ['GEMINI_API_KEY'] = "XE4tlk8VrP9jsH7yLmQbDfNeZC5vYqx3WbTghJZ"
     ```
     
     but this sometimes is not permanent and is only live for that session, depending on your installation of Jupyter and Environment.

2. **Access in Jupyter Notebook**:
   - In your Jupyter notebook, use the `os` module to access the environment variable:
   
     ```python
     import os
     api_key = os.environ.get('GEMINI_API_KEY')
     ```
---

## Connect to Gemini via API Call in Python

Let's start by listing all available models to confirm our connection!

import os
# api_key = 'XE4tlk8VrP9jsH7yLmQbDfNeZC5vYqx3WbTghJZ'

import google.generativeai as genai
genai.configure(api_key=api_key)

### Listing all available models

Confirm everything is working by listing all available models for your account, you should see a gemini-pro and gemini-pro-vision, keep in mind, there may be more versions of gemini released in the future, like gemini-nano or gemini-ultra.

for m in genai.list_models():
    if 'generateContent' in m.supported_generation_methods:
        print(m.name)



        <center><a href="https://www.pieriantraining.com/" ><img src="../PTCenteredPurple.png" alt="Pierian Training Logo" /></a></center>


# Gemini Vision Model


import google.generativeai as genai
genai.configure(api_key=api_key)

# Note: The vision model gemini-pro-vision is not optimized for multi-turn chat! Its made more for one-shot questions.
model = genai.GenerativeModel('gemini-pro-vision')

## Image

We can display an image using the Python Imaging Library, which you can install with:

    pip install Pillow

#!pip install Pillow

import PIL.Image
img = PIL.Image.open('Fridge_Picture.jpg')
img

# Image Description

To have Gemini simply describe an image, you just need to pass in the image, no additional text is needed.

response = model.generate_content(img)

response.text

## Image and Text Prompt

To add additional text prompt, we pass in a list of the text prompt and the image. Note that Gemini-Vision-Pro is really meant for a single request that has the image, it is not optimized for ongoing conversation, which is why we use generate_content(). If you attempted to use this with chat, it would not understand what image you are referring to in the subsequent calls.

response = model.generate_content(contents=['Create a recipe based on the food items you see in this picture of my fridge.',img])

print(response.text)

response = model.generate_content(contents=['I am going to the grocery store, based on this picture of my fridge, do I need to buy tomatoes?',img])

print(response.text)


model.count_tokens("hello lightness my old friend...I've come to laugh with you again")



You can easily count tokens as well:

model.count_tokens("hello lightness my old friend...I've come to laugh with you again")

