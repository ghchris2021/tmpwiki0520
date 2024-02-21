The `llama_chat_apply_template()` was added in [#5538](https://github.com/ggerganov/llama.cpp/pull/5538), which allows developers to format the chat into text prompt. By default, this function takes the template stored inside model's metadata `tokenizer.chat_template`.

To reduce the complexity of the implementation, we do **not** include a jinja parser in the project. This function works by matching the supplied template with a list of pre-defined templates hard-coded inside the function.

This is the list of templates currently supported by `llama_apply_chat_template`. If you found another template on huggingface that's not yet supported by llama.cpp, please feel free to open an issue:

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
]
for variant in VARIANTS_TO_TEST:
    tokenizer = AutoTokenizer.from_pretrained(variant)
    history = [
        { 'role': 'system', 'content': 'test' },
        { 'role': 'user', 'content': 'hello' },
        { 'role': 'assistant', 'content': 'response' },
        { 'role': 'user', 'content': 'again' },
        { 'role': 'assistant', 'content': 'response' },
    ]
    if 'Mistral' in variant:
        history.pop(0) # no system prompt for mistral
    print(variant)
    print(tokenizer.apply_chat_template(history, tokenize=False))
    print('-' * 30)
```
</details>

```
teknium/OpenHermes-2.5-Mistral-7B
<|im_start|>user
hello<|im_end|>
<|im_start|>assistant
response<|im_end|>
<|im_start|>user
again<|im_end|>
<|im_start|>assistant
response<|im_end|>

------------------------------
mistralai/Mistral-7B-Instruct-v0.2
<s>[INST] hello [/INST]response</s>[INST] again [/INST]response</s>
------------------------------
TheBloke/FusionNet_34Bx2_MoE-AWQ
[INST] <<SYS>>
test
<</SYS>>

hello [/INST] response </s><s>[INST] again [/INST] response </s>
------------------------------
bofenghuang/vigogne-2-70b-chat
<s>[INST] <<SYS>>
test
<</SYS>>

hello [/INST] response </s>[INST] again [/INST] response </s>
------------------------------
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

------------------------------
```

Additionally, we also support zephyr template (I cannot found it on huggingface, but have seen in [this list](https://github.com/ggerganov/llama.cpp/blob/c8d847d57efdc0f9bbbf881d48c645e151b36fd8/examples/server/public/promptFormats.js) )

```
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