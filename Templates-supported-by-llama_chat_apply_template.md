The `llama_chat_apply_template()` was added in [#5538](https://github.com/ggerganov/llama.cpp/pull/5538), which allows developers to format the chat into text prompt. By default, this function takes the template stored inside model's metadata `tokenizer.chat_template`.

NOTE: We do **not** include a jinja parser in llama.cpp due to its complexity. Our implementation works by matching the supplied template with a list of pre-defined templates hard-coded inside the function.

This is the list of templates currently supported by `llama_apply_chat_template`. If you found another template on huggingface that's not yet supported by llama.cpp, please feel free to open an issue:

## Supported templates

<details>
<summary>Python code</summary>

```python
from transformers import AutoTokenizer

VARIANTS_TO_TEST = [
    'teknium/OpenHermes-2.5-Mistral-7B',
    'mistralai/Mistral-7B-Instruct-v0.2',
    'TheBloke/FusionNet_34Bx2_MoE-AWQ',
    'bofenghuang/vigogne-2-70b-chat',
    'mlabonne/AlphaMonarch-7B',
    'google/gemma-7b-it',
]

HISTORY = [
    { 'role': 'system', 'content': 'test' },
    { 'role': 'user', 'content': 'hello' },
    { 'role': 'assistant', 'content': 'response' },
    { 'role': 'user', 'content': 'again' },
    { 'role': 'assistant', 'content': 'response' },
]

for variant in VARIANTS_TO_TEST:
    history = [m for m in HISTORY] # copy
    if 'Mistral' in variant or 'gemma' in variant:
        history.pop(0) # no system prompt for mistral and gemma
    if 'gemma' in variant:
        # GemmaTokenizer is not yet support by the time this code is written
        GEMMA_TMLP = "{% if messages[0]['role'] == 'system' %}{{ raise_exception('System role not supported') }}{% endif %}{% for message in messages %}{% if (message['role'] == 'user') != (loop.index0 % 2 == 0) %}{{ raise_exception('Conversation roles must alternate user/assistant/user/assistant/...') }}{% endif %}{% if (message['role'] == 'assistant') %}{% set role = 'model' %}{% else %}{% set role = message['role'] %}{% endif %}{{ '<start_of_turn>' + role + '\n' + message['content'] | trim + '<end_of_turn>\n' }}{% endfor %}{% if add_generation_prompt %}{{'<start_of_turn>model\n'}}{% endif %}"
        print('Gemma')
        output = AutoTokenizer.from_pretrained(VARIANTS_TO_TEST[0]).apply_chat_template(history, tokenize=False, chat_template=GEMMA_TMLP)
        print(output)
        print('-' * 30)
    else:
        print(variant)
        tokenizer = AutoTokenizer.from_pretrained(variant)
        print(tokenizer.apply_chat_template(history, tokenize=False))
        print('-' * 30)
```
</details>

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

## Custom chat templates

Currently, it's not possible to use your own chat template with llama.cpp server's `/chat/completions`

One of the possible solutions is use `/completions` endpoint instead, and write your own code (for example, using python) to apply a template before passing the final prompt to `/completions`

TODO: write demo python code
