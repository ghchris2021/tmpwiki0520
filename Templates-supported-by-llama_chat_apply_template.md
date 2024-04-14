The `llama_chat_apply_template()` was added in [#5538](https://github.com/ggerganov/llama.cpp/pull/5538), which allows developers to format the chat into text prompt. By default, this function takes the template stored inside model's metadata `tokenizer.chat_template`.

NOTE: We do **not** include a jinja parser in llama.cpp due to its complexity. Our implementation works by matching the supplied template with a list of pre-defined templates hard-coded inside the function.

This is the list of templates currently supported by `llama_apply_chat_template`. If you found another template on huggingface that's not yet supported by llama.cpp, please feel free to open an issue:

## Supported templates

```
Usage: ./server -m ... --chat-template chatml
teknium/OpenHermes-2.5-Mistral-7B
<|im_start|>user
hello<|im_end|>
<|im_start|>assistant
response<|im_end|>
<|im_start|>user
again<|im_end|>
<|im_start|>assistant
response<|im_end|>
```

```
Usage: ./server -m ... --chat-template llama2
mistralai/Mistral-7B-Instruct-v0.2
<s>[INST] hello [/INST]response</s>[INST] again [/INST]response</s>
```

```
(Currently cannot select this template with --chat-template)
TheBloke/FusionNet_34Bx2_MoE-AWQ
[INST] <<SYS>>
test
<</SYS>>

hello [/INST] response </s><s>[INST] again [/INST] response </s>
```

```
(Currently cannot select this template with --chat-template)
bofenghuang/vigogne-2-70b-chat
<s>[INST] <<SYS>>
test
<</SYS>>

hello [/INST] response </s>[INST] again [/INST] response </s>
```

```
Usage: ./server -m ... --chat-template monarch
mlabonne/AlphaMonarch-7B
<s>system
test</s>
<s>user
hello</s>
<s>assistant
response</s>
<s>user
again</s>
<s>assistant
response</s>
```

```
Usage: ./server -m ... --chat-template gemma
google/gemma-7b-it
<start_of_turn>user
hello<end_of_turn>
<start_of_turn>model
response<end_of_turn>
<start_of_turn>user
again<end_of_turn>
<start_of_turn>model
response<end_of_turn>
```

```
Usage: ./server -m ... --chat-template orion
<s>Human: hello

Assistant: </s>response</s>Human: again

Assistant: </s>response</s>
```

```
Usage: ./server -m ... --chat-template openchat
openchat/openchat-3.5-0106
<s>GPT4 Correct System: You are a helpful assistant<|end_of_turn|>GPT4 Correct User: Hello<|end_of_turn|>GPT4 Correct Assistant: Hi there<|end_of_turn|>GPT4 Correct User: Who are you<|end_of_turn|>GPT4 Correct Assistant:    I am an assistant   <|end_of_turn|>GPT4 Correct User: Another question<|end_of_turn|>GPT4 Correct Assistant:
```

```
Usage: ./server -m ... --chat-template vicuna
NousResearch/Nous-Capybara-34B
You are a helpful assistant

USER: Hello
ASSISTANT: Hi there</s>
USER: Who are you
ASSISTANT:    I am an assistant   </s>
USER: Another question
ASSISTANT:
```

```
Usage: ./server -m ... --chat-template vicuna-orca
migtissera/Tess-2.0-Yi-34B-200K
SYSTEM: You are a helpful assistant
USER: Hello
ASSISTANT: Hi there</s>
USER: Who are you
ASSISTANT:    I am an assistant   </s>
USER: Another question
ASSISTANT:
```

```
Usage: ./server -m ... --chat-template deepseek
deepseek-ai/deepseek-coder-33b-instruct
You are a helpful assistant### Instruction:
Hello
### Response:
Hi there
<|EOT|>
### Instruction:
Who are you
### Response:
   I am an assistant   
<|EOT|>
### Instruction:
Another question
### Response:

```

```
Usage: ./server -m ... --chat-template command-r
CohereForAI/c4ai-command-r-plus
<|START_OF_TURN_TOKEN|><|SYSTEM_TOKEN|>You are a helpful assistant<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|USER_TOKEN|>Hello<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|CHATBOT_TOKEN|>Hi there<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|USER_TOKEN|>Who are you<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|CHATBOT_TOKEN|>I am an assistant<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|USER_TOKEN|>Another question<|END_OF_TURN_TOKEN|><|START_OF_TURN_TOKEN|><|CHATBOT_TOKEN|>
```

Additionally, we also support zephyr template (I cannot find it on huggingface, but have seen in [this list](https://github.com/ggerganov/llama.cpp/blob/c8d847d57efdc0f9bbbf881d48c645e151b36fd8/examples/server/public/promptFormats.js) )

```
Usage: ./server -m ... --chat-template zephyr
<|system|>
test<|endoftext|>
<|user|>
hello<|endoftext|>
<|assistant|>
response<|endoftext|>
<|user|>
again<|endoftext|>
<|assistant|>
response<|endoftext|>
```

## How to add a new template

1. Check the `chat_template` in the model's HuggingFace `tokenizer_config.json` [(example)](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2/blob/main/tokenizer_config.json#L42).
	- If there isn't one, open an issue first to discuss. Some older models actually predate chat templates and multi-turn responses and would be difficult to support.
2. Use the following python script to generate a test conversation.
    <details>
    <summary>Script</summary>

	```python
    from transformers import AutoTokenizer

    VARIANTS_TO_TEST = [
        'teknium/OpenHermes-2.5-Mistral-7B',
        'mistralai/Mistral-7B-Instruct-v0.2',
        'TheBloke/FusionNet_34Bx2_MoE-AWQ',
        'bofenghuang/vigogne-2-70b-chat',
        'mlabonne/AlphaMonarch-7B',
        'google/gemma-7b-it',
        'OrionStarAI/Orion-14B-Chat',
        'openbmb/MiniCPM-2B-dpo-fp32',
        'openchat/openchat-3.5-0106',
        'deepseek-ai/deepseek-coder-33b-instruct',
        # Replace with your model's HuggingFace name
    ]

    HISTORY = [
        { 'role': 'system', 'content': 'You are a helpful assistant' },
        { 'role': 'user', 'content': 'Hello' },
        { 'role': 'assistant', 'content': 'Hi there' },
        { 'role': 'user', 'content': 'Who are you' },
        { 'role': 'assistant', 'content': '   I am an assistant   ' },
        { 'role': 'user', 'content': 'Another question' },
    ]

    for variant in VARIANTS_TO_TEST:
        history = [m for m in HISTORY] # copy
        if 'Mistral' in variant or 'gemma' in variant:
            history.pop(0) # no system prompt for mistral and gemma
        if 'gemma' in variant:
            # GemmaTokenizer is quite buggy, let's hard code the template here
            GEMMA_TMLP = "{% if messages[0]['role'] == 'system' %}{{ raise_exception('System role not supported') }}{% endif %}{% for message in messages %}{% if (message['role'] == 'user') != (loop.index0 % 2 == 0) %}{{ raise_exception('Conversation roles must alternate user/assistant/user/assistant/...') }}{% endif %}{% if (message['role'] == 'assistant') %}{% set role = 'model' %}{% else %}{% set role = message['role'] %}{% endif %}{{ '<start_of_turn>' + role + '\n' + message['content'] | trim + '<end_of_turn>\n' }}{% endfor %}{% if add_generation_prompt %}{{'<start_of_turn>model\n'}}{% endif %}"
            print("\n----- Gemma -----")
            output = AutoTokenizer.from_pretrained(VARIANTS_TO_TEST[0]).apply_chat_template(history, tokenize=False, add_generation_prompt=True, chat_template=GEMMA_TMLP)
            print(output)
            print("\n[Test String]\n// google/gemma-7b-it")
            print(output.replace("\n", "\\n"))
            print('"' + output.replace("\n", "\\n") + '",')
        else:
            print("\n----- " + variant + " -----")
            tokenizer = AutoTokenizer.from_pretrained(variant)
            output = tokenizer.apply_chat_template(history, tokenize=False, add_generation_prompt=True)
            print(output)
            print("\n[Test String]\n// " + variant)
            print('"' + output.replace("\n", "\\n") + '",')
	```
	</details>

3. Copy both the `chat_template` from HuggingFace and the formatted text below `[Test String]` into [tests/test-chat-template.cpp](https://github.com/ggerganov/llama.cpp/blob/master/tests/test-chat-template.cpp).

4. Run `make tests/test-chat-template`. You can now use this test to verify that your template implementation is identical to the original.

5. Implement your template in llama.cpp (search for `llama_chat_apply_template_internal`). 
	- This function attempts to detect the model's template when it's not specified. This uses the model's `chat_template` metadata, so pick a unique pattern.

6. `make` and run the test. Repeat until the output matches the original!

## Custom chat templates

Currently, it's not possible to use your own chat template with llama.cpp server's `/chat/completions`

One of the possible solutions is use `/completions` endpoint instead, and write your own code (for example, using python) to apply a template before passing the final prompt to `/completions`

TODO: write demo python code
