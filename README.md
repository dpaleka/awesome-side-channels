# Awesome Side-Channel Attacks on LLM APIs

A curated list of research papers, repositories, and posts exploring **side-channel attacks on LLM APIs** – techniques that exploit hidden signals in the API (tokenization quirks, log-likelihood feedback, caching, streaming/timing, etc.) to extract secret information about the model, its users, or to bypass certain safeguards. 


## Contents

- [Extracting information about other users of the API](#extracting-information-about-other-users-of-the-api)
- [Breaking the LLM APIs for other users](#breaking-the-llm-apis-for-other-users)
- [Extracting information about the LLM](#extracting-information-about-the-llm)

## Extracting information about other users of the API

- [**Auditing Prompt Caching in Language Model APIs**](https://arxiv.org/abs/2502.07776) – *Gu et al., Feb 2025*.
  
  - **Attack vector:** Timing differences in cache-hit vs cache-miss responses when the API uses a shared prompt cache
  - **Required access**: querying the LLM API supporting prompt caching, and the cache is shared between users
  - **Information gained:** detecting if there exists an user who has recently used a specific prompt


- [**InputSnatch: Stealing Input in LLM Services via Timing Side-Channel Attacks**](https://arxiv.org/abs/2411.18191) – *Zheng et al., Nov 2024*. 
  
  - **Attack vector:** Timing differences in cache-hit vs cache-miss responses when the API uses a shared prompt cache
  - **Required access**: querying the LLM API supporting prefix-based prompt caching, and the cache is shared between users
  - **Information gained:** complete user prompts (prefix tokens reconstructed iteratively) from unknown users



- [**Stealing User Prompts from Mixture-of-Experts Models**](https://arxiv.org/abs/2410.22884) – *Yona et al., 2024*. 
  
  - **Attack vector:** MoE architectures that computes something over a batch of inputs from multiple users and use a deterministic tie-breaker can leak information about some inputs into the outputs of other users
  - **Required access**: place many queries together with the victim's queries in the same batch
  - **Information gained:** complete victim prompts, reconstructed token-by-token


- [**I Know What You Asked: Prompt Leakage via KV-Cache Sharing in Multi-Tenant LLM Serving**](https://www.ndss-symposium.org/ndss-paper/i-know-what-you-asked-prompt-leakage-via-kv-cache-sharing-in-multi-tenant-llm-serving/) – *Wu et al., NDSS 2025*.
  
  - **Attack vector:** KV-cache sharing in multi-tenant LLM serving can create cache-hit / timing signals that enable token-by-token reconstruction of other users' prompts (PROMPTPEEK)
  - **Required access**: attacker can issue queries to the same multi-tenant serving system as the victim (co-tenant / concurrent-serving setting)
  - **Information gained:** partial or complete prompts of other users


- [**The Early Bird Catches the Leak: Unveiling Timing Side Channels in LLM Serving Systems**](https://arxiv.org/abs/2409.20002) – *Song et al., Oct 2025*.
  
  - **Attack vector:** timing side-channels arising from shared KV-cache + semantic cache behaviors (and related allocation/scheduling effects) in multi-user LLM serving
  - **Required access**: query access to the serving system; attacker uses cache-hit detection + token-by-token search over shared prefixes
  - **Information gained:** confidential system prompts and prompts from other users; can also infer cached/processed documents via semantic cache


#### Attacks that require eavesdropping on encrypted network traffic

- [**What Was Your Prompt? A Remote Keylogging Attack on AI Assistants**](https://arxiv.org/pdf/2403.09751) – *Weiss et al., Mar 2024*. 
  
  - **Attack vector:** streaming sends encrypted network packets that leak token lengths, and token lengths leak a lot of information about the text
  - **Required access**: eavesdrop on encrypted network traffic between the user and a streaming LLM API; streaming packets are aligned with token boundaries
  - **Information gained:** decrypting eavesdropped LLM outputs (29% exact reconstruction, 55% topic identification)


- [**Wiretapping LLMs: Network Side-Channel Attacks on Interactive LLM Services**](https://eprint.iacr.org/2025/167.pdf) – *Soleimani et al., 2025*.
  
  - **Attack vector:** timing patterns from speculative decoding in streaming APIs; the patterns of speculative decoding leaks information about the LLM outputs
  - **Required access**: eavesdrop on encrypted network traffic between the user and a streaming LLM API; streaming packets are aligned with speculative decoding
  - **Information gained:** statistical information about the encrypted LLM outputs


- [**Time Will Tell: Timing Side Channels via Output Token Count in Large Language Models**](https://arxiv.org/abs/2412.15431) – *Zhang et al., Dec 2024*. 
  
  - **Attack vector:** the response time of the LLM API is a good proxy for the number of tokens in the output
  - **Required access**: eavesdrop on encrypted network traffic between the user and an LLM API (not necessarily streaming)
  - **Information gained:** input attributes like target language (~75% accuracy)


- [**Mitigating a Token-Length Side-Channel Attack in our AI Products**](https://blog.cloudflare.com/ai-side-channel-attack-mitigated/) – *Cloudflare Research Team, Mar 2024*. 
  
  - **Defense mechanism:** token-length patterns in network traffic leak information, but can be obscured by randomizing the length of token packets
  - **Attack vector:** timing patterns from speculative decoding in streaming APIs; the patterns of speculative decoding leaks information about the LLM outputs
  - **Required access**: eavesdrop on encrypted network traffic between the user and a streaming LLM API; streaming packets are aligned with speculative decoding
  - **Information gained:** statistical information about the encrypted LLM outputs


- [**NetEcho: From Real-World Streaming Side-Channels to Full LLM Conversation Recovery**](https://arxiv.org/abs/2510.25472) – *Zhang et al., Oct 2025*.
  
  - **Attack vector:** packet size/timing patterns in streaming LLM apps (including scenarios with padding/obfuscation), enabling fine-grained text recovery
  - **Required access**: passive eavesdropping on encrypted network traffic between the user and a streaming LLM application/API
  - **Information gained:** partial-to-substantial reconstruction of prompts and responses (conversation recovery)


- [**Whisper Leak: a side-channel attack on Large Language Models**](https://arxiv.org/abs/2511.03675) – *McDonald & Bar Or, Nov 2025*.
  
  - **Attack vector:** packet size + timing patterns in streaming responses leak metadata usable for topic inference even under TLS
  - **Required access**: passive eavesdropping on encrypted network traffic between the user and a streaming LLM service
  - **Information gained:** prompt/topic classification (identifying conversations matching sensitive topics)


- [**Whisper Leak: A novel side-channel cyberattack on remote language models**](https://www.microsoft.com/en-us/security/blog/2025/11/07/whisper-leak-a-novel-side-channel-cyberattack-on-remote-language-models/) – *Microsoft Security Blog, Nov 2025*.
  
  - **Defense mechanism:** mitigation discussion and ecosystem/provider response context for streaming metadata leakage


## Breaking LLM APIs for other users

- [**Buffer Overflow in Mixture of Experts**](https://arxiv.org/abs/2402.05526) – *Hayes et al., Feb 2024*. 
  
  - **Attack vector:** if a MoE architecture computes something over a batch of inputs from multiple users, then malicious inputs from one user can degrade the outputs of other users' queries
  - **Required access**: place many queries together with the victim's query in the same batch
  - **Information gained:** None (potential performance degradation for user victims)


## Extracting information about the LLM not given by the API

- [**Stealing Part of a Production Language Model**](https://arxiv.org/abs/2403.06634) – *Carlini et al., Mar 2024*. 
  
  - **Attack vector:** logit bias can leak full logits; logits can be used to reconstruct the embedding projection matrix up to symmetries
  - **Required access**: queries to the LLM API supporting logit bias; it's faster if logprobs + logit bias is supported
  - **Information gained:** embedding projection matrix; model hidden dimensions; architectural details


- [**Logits of API-Protected LLMs Leak Proprietary Information**](https://arxiv.org/abs/2403.09539) - *Finlayson et al., Mar 2024*. 
  
  - **Attack vector:** logit bias can be used to reconstruct the image of the pre-softmax model
  - **Required access**: queries to the LLM API supporting logit bias; it's faster if logprobs + logit bias is supported
  - **Information gained:** model hidden dimensions; architectural details


- [**Computing Optimization-Based Prompt Injections Against Closed-Weights Models By Misusing a Fine-Tuning API**](https://arxiv.org/abs/2501.09798) – *Labunets et al., Jan 2025*. 
  
  - **Attack vector:** Fine-tuning loss can be used to get original LLM loss values for a given prompt
  - **Required access**: Fine-tuning API that reports training loss per-sample
  - **Information gained:** Loss for a given token sequence; e.g. for gray-box jailbreak attacks


- [**The Worst (But Only) Claude 3 Tokenizer**](https://javirando.com/blog/2024/claude-tokenizer/) – *Rando & Tramèr, 2024*. 
  
  - **Attack vector:** Token boundaries revealed in streaming API responses
  - **Required access**: streaming API where streaming packets are aligned with token boundaries
  - **Information gained:** vocab; reproducing the tokenization algorithm


- [**Privacy Side Channels in Machine Learning Systems**](https://arxiv.org/abs/2309.05610) – *Debenedetti et al., 2023*.

  - **Attack vector:** fixed context length leaks how many tokens are in a long prompt
  - **Required access**: large queries to the LLM API
  - **Information gained:** vocab (practical); reconstruction of tokenization algorithm (theoretical)


- [**Auditing Prompt Caching in Language Model APIs**](https://arxiv.org/abs/2502.07776) – *Gu et al., Feb 2025*.

  - **Attack vector:** prompt caching optimization on prompts that share a prefix are only possible in decoder-only architectures
  - **Required access**: embedding model API with prompt caching enabled (even for a single user)
  - **Information gained:** whether an embedding model is a decoder-only transformer


- [**Privacy Side Channels in Machine Learning Systems**](https://arxiv.org/abs/2309.05610) – *Debenedetti et al., 2023*.
  
  - **Attack vector:** output filter that activates on exact sensitive strings from the training data leaks those sensitive strings
  - **Required access**: query access to a model with an output filter
  - **Information gained:** Training data membership; extraction of secrets that base models don't directly memorize


- [**"Energon": Unveiling Transformers from GPU Power and Thermal Side-Channels**](https://arxiv.org/abs/2508.01768) – *Chaudhuri et al., Aug 2025*.
  
  - **Attack vector:** GPU power/thermal side-channels in shared GPU settings can reveal transformer architectural details
  - **Required access**: ability to observe power/thermal signals in the deployment environment (co-located / shared infrastructure setting)
  - **Information gained:** model family and architectural/hyperparameter details


- [**Selective KV-Cache Sharing to Mitigate Timing Side-Channels in LLM Inference**](https://arxiv.org/abs/2508.08438) – *Chu et al., Aug 2025*.
  
  - **Defense mechanism:** selective KV-cache sharing (SafeKV) intended to reduce cache-hit timing leakage while retaining performance benefits


---

*Contributions welcome.* This list focuses on **side-channel exploits unique to LLM APIs or other AI services** 

Things not covered by this list:
- Attacks requiring physical co-location of the attacker and the target
- Attacks requiring data poisoning or any modification to the training process
- Adversarial attacks or prompt injection attacks that take the defense as given, not exploiting a side-channel
