# জেনারেটিভ এআই মাস্টারক্লাস কারিকুলাম ও হ্যান্ডবুক (The Complete Generative AI Engineering Handbook: From Math to Production)

> *"Discriminative models classify the world: they predict $P(Y \mid X)$ — given an image, is it a cat? Generative models understand the world deeply enough to recreate it: they model $P(X)$ or $P(X \mid Y)$ — they learn the high-dimensional probability distribution of reality and sample brand-new, never-before-seen cats, sonnets, code, and 3D worlds."*
> 
> স্বাগতম **জেনারেটিভ এআই (Generative AI) ইঞ্জিনিয়ারিং মাস্টারক্লাস**-এ! এই কারিকুলামটি এমনভাবে তৈরি করা হয়েছে যাতে একজন শিক্ষার্থী বা প্রফেশনাল সফটওয়্যার ইঞ্জিনিয়ার জেনারেটিভ এআই-এর একদম গাণিতিক ভিত্তি (তথ্য তত্ত্ব, সম্ভাবনা, ভ্যারিয়েশনাল ইনফারেন্স) থেকে শুরু করে VAE, GAN, ডিফিউশন মডেল (Stable Diffusion), লার্জ ল্যাঙ্গুয়েজ মডেল (LLM আর্কিটেকচার, RoPE, GQA), প্রম্পট ইঞ্জিনিয়ারিং, LoRA/QLoRA ফাইন-টিউনিং, DPO অ্যালাইনমেন্ট, এন্টারপ্রাইজ RAG, অটোনোমাস এআই এজেন্টস (Multi-Agent Systems), মাল্টিমোডাল মডেলস (CLIP, LLaVA, Sora) এবং প্রোডাকশন ইনফারেন্স অপটিমাইজেশন (vLLM PagedAttention, Speculative Decoding) পর্যন্ত প্রতিটি বিষয় পুঙ্খানুপুঙ্খভাবে শিখতে পারেন।

---

## 🗺️ সম্পূর্ণ কারিকুলাম ও অধ্যায় ডিরেক্টরি (Curriculum Map)

| অধ্যায় ক্রম | ফাইলের নাম | মূল বিষয়বস্তু ও প্রযুক্তি | লিঙ্ক |
| :---: | :--- | :--- | :---: |
| **অধ্যায় ১** | **গাণিতিক ভিত্তি ও তত্ত্ব** | ডিসক্রিমিনেটিভ বনাম জেনারেটিভ, জয়েন্ট প্রোবাবিলিটি, KL/JS ডাইভারজেন্স, ভ্যারিয়েশনাল ইনফারেন্স ও ELBO প্রমাণ | [👉 অধ্যায় ১ পড়ুন](file:///g:/MNC/Generative_AI/01_foundations_and_math_of_genai.md) |
| **অধ্যায় ২** | **ভ্যারিয়েশনাল অটোএনকোডার (VAE)** | অটোএনকোডার সীমাবদ্ধতা, কন্টিনিউয়াস ল্যাটেন্ট স্পেস, Reparameterization ট্রিক, $\beta$-VAE, VQ-VAE কোডবুক | [👉 অধ্যায় ২ পড়ুন](file:///g:/MNC/Generative_AI/02_variational_autoencoders_vae.md) |
| **অধ্যায় ৩** | **জেনারেটিভ অ্যাডভারসারিয়াল নেটওয়ার্ক (GAN)** | Minimax গেম থিওরি, অপটিমাল ডিসক্রিমিনেটর প্রমাণ, মোড কলাপ্স, WGAN ও WGAN-GP, CycleGAN, StyleGAN | [👉 অধ্যায় ৩ পড়ুন](file:///g:/MNC/Generative_AI/03_generative_adversarial_networks_gan.md) |
| **অধ্যায় ৪** | **ডিফিউশন মডেল ও ইমেজ সিন্থেসিস** | থার্মোডায়নামিক্স, ফরোয়ার্ড নয়েজিং, রিভার্স ডিনয়েজিং U-Net, DDPM ও DDIM, Classifier-Free Guidance (CFG), Stable Diffusion ও Latent Diffusion | [👉 অধ্যায় ৪ পড়ুন](file:///g:/MNC/Generative_AI/04_diffusion_models_and_image_generation.md) |
| **অধ্যায় ৫** | **অটো-রিগ্রেসিভ LLM ও ট্রান্সফরমার্স** | ডিকোডার-অনলি আর্কিটেকচার, Causal Masking, RoPE পজিশনাল এনকোডিং, MHA বনাম MQA বনাম GQA, FlashAttention, KV Cache মেমরি ও স্যাম্পলিং গণিত | [👉 অধ্যায় ৫ পড়ুন](file:///g:/MNC/Generative_AI/05_autoregressive_llms_and_transformers.md) |
| **অধ্যায় ৬** | **প্রম্পট ইঞ্জিনিয়ারিং ও অ্যাডভান্সড রিজনিং** | Zero/Few-Shot, Chain-of-Thought (CoT), Tree-of-Thought (ToT), ReAct ফ্রেমওয়ার্ক, ডিরেকশনাল স্টিমুলাস, স্ট্রাকচার্ড JSON আউটপুট, প্রম্পট ইনজেকশন | [👉 অধ্যায় ৬ পড়ুন](file:///g:/MNC/Generative_AI/06_prompt_engineering_and_in_context_learning.md) |
| **অধ্যায় ৭** | **ফাইন-টিউনিং, PEFT (LoRA) ও হিউম্যান অ্যালাইনমেন্ট** | ইনস্ট্রাকশন টিউনিং (SFT), LoRA গণিত ($W_0 + \frac{\alpha}{r}BA$), QLoRA (NF4 ও ডাবল কোয়ান্টাইজেশন), RLHF (PPO) বনাম Direct Preference Optimization (DPO) ডেরিভেশন | [👉 অধ্যায় ৭ পড়ুন](file:///g:/MNC/Generative_AI/07_finetuning_peft_lora_and_alignment.md) |
| **অধ্যায় ৮** | **এন্টারপ্রাইজ RAG ও ভেক্টর সিস্টেমস** | চাঙ্কিং কৌশল (Semantic/Recursive), বায়-এনকোডার এমবেডিংস, HNSW ইনডেক্সিং, স্পার্স BM25 + ডেন্স RRF ফিউশন, ক্রস-এনকোডার রি-র‍্যাঙ্কিং, RAGAS ট্রায়াড | [👉 অধ্যায় ৮ পড়ুন](file:///g:/MNC/Generative_AI/08_rag_and_vector_databases.md) |
| **অধ্যায় ৯** | **অটোনোমাস এআই এজেন্টস ও টুল ইউজ** | এজেন্টের অ্যানাটমি (Perception, Planning, Memory, Action), Function Calling ও JSON Schemas, ReAct লুপ, Reflexion সেলফ-কারেকশন, মাল্টি-এজেন্ট কোলাবোরেশন | [👉 অধ্যায় ৯ পড়ুন](file:///g:/MNC/Generative_AI/09_autonomous_ai_agents_and_tool_use.md) |
| **অধ্যায় ১০**| **মাল্টিমোডাল জেনারেটিভ এআই** | কন্ট্রাস্টিভ লার্নিং (CLIP), ভিশন-ল্যাঙ্গুয়েজ মডেলস (LLaVA), প্রজেকশন লেয়ার, অডিও ও স্পিচ (Whisper), ভিডিও জেনারেশন আর্কিটেকচার (Diffusion Transformers - DiT / Sora) | [👉 অধ্যায় ১০ পড়ুন](file:///g:/MNC/Generative_AI/10_multimodal_generative_ai.md) |
| **অধ্যায় ১১**| **জেনএআই মূল্যায়ন, নিরাপত্তা ও গভর্নেন্স** | বেঞ্চমার্কস (MMLU, GSM8K, Chatbot Arena), LLM-as-a-Judge বায়াসেস, হ্যালুসিনেশন ডিটেকশন, রেড টিমিং, Llama Guard ও NeMo Guardrails, ডেটা প্রাইভেসি | [👉 অধ্যায় ১১ পড়ুন](file:///g:/MNC/Generative_AI/11_genai_evaluation_safety_and_governance.md) |
| **অধ্যায় ১২**| **প্রোডাকশন জেনএআই ও ইনফারেন্স অপটিমাইজেশন** | vLLM ও PagedAttention ভার্চুয়াল মেমরি অ্যালগরিদম, Continuous Batching, Speculative Decoding, কোয়ান্টাইজেশন (AWQ, GPTQ, GGUF), সেমান্টিক ক্যাশিং, FastAPI ডিপ্লয়মেন্ট | [👉 অধ্যায় ১২ পড়ুন](file:///g:/MNC/Generative_AI/12_production_genai_and_deployment.md) |

---

## 🏛️ জেনারেটিভ এআই ফ্যামিলি ট্রি ও আর্কিটেকচার তুলনা

```
                                  Generative AI Paradigm
                                            │
        ┌───────────────────┬───────────────┴───────────────┬───────────────────┐
        ▼                   ▼                               ▼                   ▼
[ Autoregressive LLMs ] [ Diffusion Models ]           [ VAEs ]              [ GANs ]
• GPT-4, LLaMA-3, Gemma • Stable Diffusion, Midjourney • VQ-VAE, DALL-E 1   • StyleGAN, CycleGAN
• Modeled as:           • Modeled as:                  • Modeled as:         • Modeled as:
  P(x_t | x_<t)           Denoising Score Matching       ELBO Optimization     Minimax 2-Player Game
• Best for: Text, Code  • Best for: Photoreal Images   • Best for: Latents   • Best for: Real-time Upscaling
```

---

## 🚀 এই হ্যান্ডবুকটি কীভাবে পড়বেন?
1. **তাত্ত্বিক ভিত্তি:** প্রতিটি অধ্যায়ের শুরুতে সংশ্লিষ্ট মডেলটির গাণিতিক ভিত্তি ও স্বজ্ঞাত চিত্র (ASCII Architecture) বিস্তারিতভাবে ব্যাখ্যা করা হয়েছে।
2. **বাস্তব সংখ্যাভিত্তিক হিসাব:** প্রতিটি সূত্রে ডেমো সংখ্যা বসিয়ে স্টেপ-বাই-স্টেপ ক্যালকুলেশন দেখানো হয়েছে (যেমন: কেভি-ক্যাশ মেমরি, RRF স্কোর, লস ফাংশন)।
3. **প্রোডাকশন কোড:** প্রতিটি অধ্যায়ে স্ক্র্যাচ পাইথন এবং PyTorch / HuggingFace দিয়ে সম্পূর্ণ এক্সিকিউটেবল কোড সংযুক্ত রয়েছে।

চলুন শুরু করা যাক—[👉 অধ্যায় ১: গাণিতিক ভিত্তি ও তত্ত্ব](file:///g:/MNC/Generative_AI/01_foundations_and_math_of_genai.md) দিয়ে আপনার জেনারেটিভ এআই অভিযাত্রা শুরু করুন!
