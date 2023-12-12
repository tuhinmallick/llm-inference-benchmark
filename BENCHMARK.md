# Do benchmark

## benchmark

Use <https://github.com/fw-ai/benchmark> to benchmark.

```bash
python -m venv .venv
source .venv/bin/activate

pip install -r llm_bench/requirements.txt
```

NOTICE: every bench run twice, the first run is warm up.

## backends

### text-generation-webui

version: snapshot-2023-12-10

```bash
python server.py --api --model Yi-6B-Chat --model-dir ~/models
```

bs=4:

```bash
locust -t 30s --provider openai -u 4 -r 4 -H http://127.0.0.1:5000 -p 512 -o 200 --prompt-randomize --api-key EMPTY --model=Yi-6B-Chat --chat
=================================== Summary ====================================
Provider           : openai
Model              : Yi-6B-Chat
Prompt Tokens      : 543.0
Generation Tokens  : 200
Stream             : False
Temperature        : 1.0
Logprobs           : None
Concurrency        : 4
Time To First Token:
Latency Per Token  :
Num Tokens         : 198.25
Total Latency      : 19634.00726575128
Num Requests       : 4
Qps                : 0.14749432591086067
================================================================================
Tokens per Second = 198.25 * 1000 / 19634.00726575128 * 4 = 40.39
```

bs=1 (stream=False)

```bash
locust -t 30s --provider openai -u 1 -r 1 -H http://127.0.0.1:5000 -p 512 -o 200 --prompt-randomize --api-key EMPTY --model=Yi-6B-Chat --chat
=================================== Summary ====================================
Provider           : openai
Model              : Yi-6B-Chat
Prompt Tokens      : 543.0
Generation Tokens  : 200
Stream             : False
Temperature        : 1.0
Logprobs           : None
Concurrency        : 1
Time To First Token:
Latency Per Token  :
Num Tokens         : 199.0
Total Latency      : 4887.356891998934
Num Requests       : 6
Qps                : 0.20226618477157257
================================================================================
Tokens per Second = 199.0 * 1000 / 4887.356891998934 = 40.71
```

stream=True failed: run failed:  POST /v1/chat/completions: JSONDecodeError('unexpected content after document: line 3 column 1 (char 271)')


### vllm

version: 0.2.4

```bash
python -m vllm.entrypoints.openai.api_server --model ~/models/Yi-6B-Chat --served-model-name Yi-6B-Chat --port 8000
```

bs=4:

```bash
locust -t 30s --provider openai -u 4 -r 4 -H http://127.0.0.1:8000 -p 512 -o 200 --prompt-randomize --api-key EMPTY --model=Yi-6B-Chat --chat
=================================== Summary ====================================
Provider           : openai
Model              : Yi-6B-Chat
Prompt Tokens      : 521.0
Generation Tokens  : 200
Stream             : False
Temperature        : 1.0
Logprobs           : None
Concurrency        : 4
Time To First Token:
Latency Per Token  :
Num Tokens         : 200.0
Total Latency      : 3593.354136968628
Num Requests       : 32
Qps                : 1.0800308609840037
================================================================================
Tokens per Second = 200.0 * 1000 / 3593.354136968628 = 55.65
```

bs=1 (stream=True)

```bash
locust -t 30s --provider openai -u 1 -r 1 -H http://127.0.0.1:8000 -p 512 -o 200 --prompt-randomize --api-key EMPTY --model=Yi-6B-Chat --chat --stream
=================================== Summary ====================================
Provider           : openai
Model              : Yi-6B-Chat
Prompt Tokens      : 521.0
Generation Tokens  : 200
Stream             : True
Temperature        : 1.0
Logprobs           : None
Concurrency        : 1
Time To First Token: 2.7346884445351964
Latency Per Token  : 16.615315399767947
Num Tokens         : 199.0
Total Latency      : 3309.182452998357
Num Requests       : 9
Qps                : 0.30185721330275145
================================================================================
Tokens per Second = 1000 / 16.615315399767947 = 60.18
```