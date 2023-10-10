# 在Cloud9 先輸入下列指令
```txt
mkdir python

pip3 install -t python/ openai --use-feature=2020-resolver

pip3 install -t python/ urllib3==1.25.4 --use-feature=2020-resolver

zip -r openai.zip python

aws lambda publish-layer-version --layer-name OpenAIforLambdaLayer --zip-file fileb://openai.zip --compatible-runtime python3.7
```

# 在Lambda上建立一個新的Function

```python
import os
import openai

openai.api_key = os.environ['openai_key']

def lambda_handler(event, context):
    completion = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "user", "content": "說個笑話"}
        ]
    )
    print(completion)
    translate_content = completion.choices[0].message.content
    return translate_content
```
