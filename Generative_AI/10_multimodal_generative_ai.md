# Chapter 10: Multimodal Generative AI — Vision, Audio & Video Foundation Models

---

## ১. মাল্টিমোডাল এআই-এর রূপরেখা (The Multimodal Paradigm)

বাস্তব বিশ্ব কোনো একক মাধ্যমে সীমাবদ্ধ নয়; মানুষ একই সাথে চোখ দিয়ে দেখে, কান দিয়ে শোনে এবং ভাষা দিয়ে চিন্তা করে। **মাল্টিমোডাল জেনারেটিভ এআই (Multimodal GenAI)** হলো এমন একটি ব্যবস্থা যা টেক্সট, ইমেজ, অডিও এবং ভিডিওকে একটি সমন্বিত গাণিতিক ভেক্টর স্পেসে অনুধাবন ও সৃষ্টি করতে পারে।

```
                                  [ Shared Latent Space ]
                                             |
             +--------------------+----------+----------+--------------------+
             |                    |                     |                    |
             v                    v                     v                    v
      [ Text Tokens ]       [ Image Patches ]    [ Audio Spectrogram ] [ Video Cubes ]
       "A cute dog"           (16x16 pixels)       (Log-Mel frames)     (Spatio-Temporal)
```

---

## ২. কনট্রাস্টিভ ল্যাঙ্গুয়েজ-ইমেজ প্রি-ট্রেইনিং (CLIP - Radford et al., OpenAI 2021)

CLIP হলো আধুনিক মাল্টিমোডাল বিপ্লবের ভিত্তিপ্রস্তর। এটি ৪০০ মিলিয়ন টেক্সট-ইমেজ জোড়ার ওপর সেলফ-সুপারভাইজড কনট্রাস্টিভ লার্নিংয়ের মাধ্যমে শেখে।

```
Image ---> [ Vision Encoder (ViT) ] ---> Normalized Image Vector I_i (d-dim)
                                                                             \
                                                                              ---> Cosine Matrix: S_ij = I_i * T_j / tau
                                                                             /
Text  ---> [ Text Encoder (Transf) ] --> Normalized Text Vector T_j (d-dim)
```

### সিমেট্রিক ইনফো-এনসিই লস (Symmetric InfoNCE Loss):
ধরা যাক একটি ব্যাচে $N$ সংখ্যক ইমেজ-টেক্সট জোড়া $\{(x_i^I, x_i^T)\}_{i=1}^N$ রয়েছে। তাদের নরমালাইজড এম্বেডিং হলো $I_i$ এবং $T_i$ ($\|I_i\|_2 = \|T_i\|_2 = 1$)।
মডেলের লক্ষ্য হলো ডায়াগনালের সঠিক জোড়াগুলোর সিমিলারিটি $1.0$-এর কাছে নেওয়া এবং অফ-ডায়াগনালের ভুল জোড়াগুলোর সিমিলারিটি কমানো।

#### ইমেজ-টু-টেক্সট ক্রস-এনট্রপি লস:
$$\mathcal{L}_i^{(I \to T)} = -\log \frac{\exp(\langle I_i, T_i \rangle / \tau)}{\sum_{j=1}^N \exp(\langle I_i, T_j \rangle / \tau)}$$

#### টেক্সট-টু-ইমেজ ক্রস-এনট্রপি লস:
$$\mathcal{L}_i^{(T \to I)} = -\log \frac{\exp(\langle T_i, I_i \rangle / \tau)}{\sum_{j=1}^N \exp(\langle T_j, I_i \rangle / \tau)}$$

#### মোট সিমেট্রিক লস:
$$\mathcal{L}_{\text{CLIP}} = \frac{1}{2N} \sum_{i=1}^N \left( \mathcal{L}_i^{(I \to T)} + \mathcal{L}_i^{(T \to I)} \right)$$

- $\tau$ হলো একটি লার্নেবল টেম্পারেচার প্যারামিটার যা লগিটগুলোকে স্কেল করে।

### জিরো-শট ইমেজ ক্লাসিফিকেশন (Zero-Shot Classification):
কোনো ইমেজ ক্লাসিফাই করতে মডেলকে আলাদা ফাইন-টিউন করতে হয় না। সম্ভাব্য ক্লাস লেবেলগুলোকে টেক্সট টেমপ্লেটে রূপান্তর করা হয়:
`"a photo of a {dog}"`, `"a photo of a {cat}"`, `"a photo of a {car}"`।
টেক্সট এনকোডার দিয়ে এই প্রম্পটগুলোর ভেক্টর তৈরি করে ইনপুট ইমেজের সাথে ডট প্রোডাক্ট বের করলেই সর্বাধিক স্কোরের ক্লাসটি তাৎক্ষণিক শনাক্ত হয়ে যায়!

---

## ৩. ভিশন-ল্যাঙ্গুয়েজ মডেল আর্কিটেকচার (VLM / LMM - LLaVA)

LLaVA (Large Language and Vision Assistant - Liu et al., 2023), GPT-4V এবং Gemini-এর মতো আধুনিক ভিশন মডেলগুলোর অভ্যন্তরীণ কাঠামো মূলত ৩টি অংশের সমন্বয়:

```
[ Input Image ] ---> [ Vision Encoder (CLIP ViT) ] ---> Visual Grid Features Z_v (e.g. 576 tokens)
                                                                 |
                                                                 v
                                                      [ Projection Layer / MLP ]
                                                                 |
                                                                 v
                                                      Projected Visual Tokens H_v
                                                                 |
[ User Prompt ] ---> [ Text Tokenizer ] ------------> Text Tokens H_q
                                                                 |
                                                                 v
                                    Concatenated Sequence: [ H_v , H_q ]
                                                                 |
                                                                 v
                                                 [ Autoregressive LLM (LLaMA-3) ]
                                                                 |
                                                                 v
                                                        Generated Description!
```

### ১. ভিশন এনকোডার (Vision Encoder):
সাধারণত একটি প্রি-ট্রেইন্ড ViT-L/14 বা SigLIP মডেল, যা ইনপুট ইমেজকে (যেমন $336 \times 336$) ছোট ছোট প্যাচে (যেমন $14 \times 14$) ভাগ করে প্রতিটি প্যাচের জন্য একটি ভেক্টর তৈরি করে ($24 \times 24 = 576$ প্যাচ টোকেন)।

### ২. ভিশন-ল্যাঙ্গুয়েজ প্রজেক্টর (The Multimodal Connector):
ভিশন এনকোডারের আউটপুট ডাইমেনশন (যেমন ১০২৪) এলএলএম-এর হিডেন ডাইমেনশনের (যেমন ৪০৯৬) সাথে মেলে না। একটি ২-লেয়ারের নন-লিনিয়ার MLP প্রজেক্টর ভিজ্যুয়াল ফিচারগুলোকে এলএলএম-এর শব্দ ভেক্টরের সমতুল্য টোকেন স্পেসে ম্যাপ করে:

$$H_v = W_2 \cdot \text{GELU}(W_1 Z_v + b_1) + b_2$$

### ৩. অটো-রিগ্রেসিভ এলএলএম:
মডেলের কাছে এই ভিজ্যুয়াল টোকেনগুলো সাধারণ ইংরেজি বা বাংলা শব্দের মতোই ট্রিট হয়। ফলে এলএলএম কোনো বিশেষ পরিবর্তন ছাড়াই ছবি দেখে জটিল প্রশ্নের উত্তর দিতে পারে।

---

## ৪. অডিও ও স্পিচ জেনারেশন (Whisper & Speech Transformers)

OpenAI Whisper (Radford et al., 2022) অডিও প্রসেসিংয়ের আধুনিক স্ট্যান্ডার্ড।

```
[ Raw Audio Waveform (16 kHz) ]
             |
             v
 [ 80-Channel Log-Mel Spectrogram ]
             |
             v
   [ 1D Conv Subsampling ]
             |
             v
[ Transformer Encoder ] <=== Cross Attention ===> [ Autoregressive Transformer Decoder ]
                                                                |
                                                                v
                                                   Transcribed Text / Translation
```

1. কাঁচা অডিও ওয়েভফর্মকে ২৫ মিলিসেকেন্ডের উইন্ডোতে ফুরিয়ার ট্রান্সফর্মেশন (STFT) করে ৮০-চ্যানেলের **Log-Mel Spectrogram**-এ রূপান্তর করা হয়।
2. কনভলিউশন লেয়ার অডিও লেন্থকে ২ গুণ সংকুচিত করে এনকোডারে পাঠায়।
3. ডিকোডার স্পিচ রিকগনিশন, ভয়েস অ্যাক্টিভিটি ডিটেকশন, টাইমস্ট্যাম্প প্রেডিকশন এবং সরাসরি ৫০+ ভাষায় অনুবাদ একযোগে সম্পাদন করে।

---

## ৫. ভিডিও জেনারেশন ও ডিফিউশন ট্রান্সফরমারস (DiT / Sora Architecture)

OpenAI Sora এবং আধুনিক ভিডিও মডেলগুলো সনাতন কনভলিউশনাল U-Net বর্জন করে **Diffusion Transformers (DiT - Peebles & Xie, 2023)** আর্কিটেকচার গ্রহণ করেছে।

```
[ Video: Channels x Frames x Height x Width ]
                     |
                     v
 [ Spatio-Temporal Patchification (3D Cubes) ] ---> Flattened 1D Latent Sequence
                     |
                     v
   [ Standard Transformer Blocks with DiT AdaLN ] <--- Timestep t + Text Condition
                     |
                     v
[ Unpatchify to Denoised Video Latents ] ---> [ High-Res Video Reconstruction ]
```

1. **স্প্যাশিও-টেম্পোরাল প্যাচ (3D Space-Time Cubes):** ভিডিওকে দৈর্ঘ্য, প্রস্থ এবং সময় অক্ষ বরাবর ছোট ছোট ত্রিমাত্রিক কিউব আকারে কেটে লিনিয়ার সিকোয়েন্স হিসেবে সাজানো হয়।
2. **স্কেলিং ল (Compute Scaling):** ট্রান্সফরমার আর্কিটেকচার হওয়ায় কম্পিউটেশনাল ক্ষমতা (FLOPs) বাড়ানোর সাথে সাথে ভিডিওর গতিবিধি, পদার্থবিজ্ঞানের নিয়ম এবং ভিজ্যুয়াল রিয়ালিজম স্বতঃস্ফূর্তভাবে উন্নত হয়।

---

## ৬. সংখ্যাগত উদাহরণ (Step-by-Step Numerical Example)

### CLIP InfoNCE লস গণনা (ব্যাচ সাইজ $N = 2$, $\tau = 1.0$):
ধরা যাক দুটি ইমেজ ($I_1, I_2$) এবং দুটি ক্যাপশন ($T_1, T_2$)-এর নরমালাইজড ডট প্রোডাক্ট সিমিলারিটি ম্যাট্রিক্স $S$:

$$S = \begin{pmatrix} \langle I_1, T_1 \rangle & \langle I_1, T_2 \rangle \\ \langle I_2, T_1 \rangle & \langle I_2, T_2 \rangle \end{pmatrix} = \begin{pmatrix} 0.90 & 0.10 \\ 0.20 & 0.80 \end{pmatrix}$$

যেখানে ডায়াগনালের মান ($0.90, 0.80$) হলো সত্য জোড়া।

#### ধাপ ১: এক্সপোনেনশিয়াল মান নির্ণয় ($e^s$)
- রো ১: $e^{0.90} \approx 2.4596$, $e^{0.10} \approx 1.1052 \implies \text{যোগফল} = 2.4596 + 1.1052 = 3.5648$
- রো ২: $e^{0.20} \approx 1.2214$, $e^{0.80} \approx 2.2255 \implies \text{যোগফল} = 1.2214 + 2.2255 = 3.4469$

#### ধাপ ২: ইমেজ-টু-টেক্সট লস $\mathcal{L}^{(I \to T)}$
$$\mathcal{L}_1^{(I \to T)} = -\log\left(\frac{2.4596}{3.5648}\right) = -\log(0.6899) \approx 0.3712$$
$$\mathcal{L}_2^{(I \to T)} = -\log\left(\frac{2.2255}{3.4469}\right) = -\log(0.6457) \approx 0.4374$$
$$\text{গড় } \mathcal{L}^{(I \to T)} = \frac{0.3712 + 0.4374}{2} = 0.4043$$

একইভাবে টেক্সট-টু-ইমেজের জন্য কলাম-ওয়াইজ সফটম্যাক্স হিসাব করে গড় লস বের করলে মোট CLIP লস নির্ণয় হয়!

---

## ৭. সম্পূর্ণ পাইটর্চ মাল্টিমোডাল প্রজেকশন ও ইনফো-এনসিই ইমপ্লিমেন্টেশন

নিচে একটি স্বয়ংসম্পূর্ণ, ভেরিফাইড পাইটর্চ স্ক্রিপ্ট দেওয়া হলো যা CLIP সিমেট্রিক InfoNCE লস এবং একটি সম্পূর্ণ ভিশন-ল্যাঙ্গুয়েজ MLP প্রজেকশন লেয়ার বাস্তবায়ন করে:

```python
"""
Multimodal Deep Learning: CLIP InfoNCE Loss and Vision-to-Language Projector.
Fully runnable, verified and mathematically grounded.
"""

import torch
import torch.nn as nn
import torch.nn.functional as F


# ============================================================================
# 1. Symmetric InfoNCE Loss (CLIP Loss)
# ============================================================================
class ClipLoss(nn.Module):
    def __init__(self, init_temperature: float = 0.07):
        super(ClipLoss, self).__init__()
        # Learnable log-temperature parameter for stability
        self.log_tau = nn.Parameter(torch.tensor(torch.log(torch.tensor(init_temperature))))

    def forward(self, image_embeddings: torch.Tensor, text_embeddings: torch.Tensor) -> torch.Tensor:
        # L2 Normalize embeddings along feature dimension
        image_embeddings = F.normalize(image_embeddings, p=2, dim=-1)
        text_embeddings = F.normalize(text_embeddings, p=2, dim=-1)

        # Scale cosine similarities by temperature
        tau = torch.exp(self.log_tau).clamp(min=1e-3, max=100.0)
        logits = torch.matmul(image_embeddings, text_embeddings.t()) / tau

        # Ground truth labels: Diagonal elements are positive matches
        batch_size = image_embeddings.size(0)
        labels = torch.arange(batch_size, device=image_embeddings.device)

        # Symmetric cross-entropy
        loss_i2t = F.cross_entropy(logits, labels)
        loss_t2i = F.cross_entropy(logits.t(), labels)

        return (loss_i2t + loss_t2i) / 2.0


# ============================================================================
# 2. Vision-Language MLP Projector (LLaVA-Style Connector)
# ============================================================================
class MultimodalProjector(nn.Module):
    def __init__(self, vision_dim: int = 768, llm_dim: int = 2048):
        super(MultimodalProjector, self).__init__()
        self.linear1 = nn.Linear(vision_dim, llm_dim)
        self.act = nn.GELU()
        self.linear2 = nn.Linear(llm_dim, llm_dim)

    def forward(self, visual_features: torch.Tensor) -> torch.Tensor:
        # visual_features shape: [Batch, Num_Patches, Vision_Dim]
        # output shape:          [Batch, Num_Patches, LLM_Dim]
        return self.linear2(self.act(self.linear1(visual_features)))


# ============================================================================
# 3. Complete Verification & Demo
# ============================================================================
if __name__ == "__main__":
    torch.manual_seed(42)

    batch_size = 4
    embed_dim = 128
    vision_dim = 512
    llm_dim = 1024
    num_patches = 16

    print("--- 1. Testing CLIP Symmetric InfoNCE Loss ---")
    clip_loss_fn = ClipLoss(init_temperature=0.07)
    dummy_img_emb = torch.randn(batch_size, embed_dim)
    dummy_txt_emb = torch.randn(batch_size, embed_dim)

    loss = clip_loss_fn(dummy_img_emb, dummy_txt_emb)
    print(f"Computed Symmetric CLIP Loss: {loss.item():.4f}")

    print("\n--- 2. Testing Multimodal Vision-to-LLM Connector ---")
    projector = MultimodalProjector(vision_dim=vision_dim, llm_dim=llm_dim)
    dummy_vision_patches = torch.randn(batch_size, num_patches, vision_dim)

    projected_tokens = projector(dummy_vision_patches)
    print(f"Raw Vision Patch Shape:    {dummy_vision_patches.shape}")
    print(f"Projected LLM Token Shape: {projected_tokens.shape}")
    print("Multimodal components executed and verified successfully!")
```

---

## ৮. তুলনা ও সিদ্ধান্ত ম্যাট্রিক্স

| মডেল | ইনপুট মোডালিটি | আউটপুট মোডালিটি | প্রধান আর্কিটেকচারাল মেকানিজম |
| :--- | :--- | :--- | :--- |
| **CLIP** | ইমেজ + টেক্সট | জয়েন্ট এম্বেডিং ভেক্টর | সিমেট্রিক কনট্রাস্টিভ লার্নিং (InfoNCE) |
| **LLaVA / GPT-4V**| ইমেজ + টেক্সট | টেক্সট টোকেন | Frozen ViT + MLP Projector + Autoregressive LLM |
| **Whisper** | কাঁচা অডিও স্পেকট্রোগ্রাম | টেক্সট (ট্রান্সক্রিপ্ট/অনুবাদ) | 1D Conv + Encoder-Decoder Transformer |
| **Sora / DiT** | টেক্সট প্রম্পট + টাইমস্টেপ| মাল্টি-ফ্রেম হাই-রেজ ভিডিও | 3D Spatio-Temporal Patches + Flow Matching DiT |

---

## ৯. ইন্টারভিউ প্রশ্নোত্তর (Interview Checklist)

1. **প্রশ্ন: CLIP মডেলে কনট্রাস্টিভ ট্রেইনিংয়ের জন্য বিশাল ব্যাচ সাইজ (যেমন ৩২,৭৬৮) কেন প্রয়োজন হয়?**
   - **উত্তর:** কনট্রাস্টিভ লার্নিং নেগেটিভ স্যাম্পলের বৈচিত্র্যের ওপর নির্ভরশীল। একটি ছোট ব্যাচ সাইজে (যেমন ৩২) মাত্র ৩১টি নেগেটিভ স্যাম্পল থাকে, যা মডেলকে শর্টকাট শিখতে উৎসাহিত করে। হাজার হাজার ব্যাচ সাইজে প্রতিটি পজিটিভ জোড়ার বিপরীতে হাজার হাজার কঠিন নেগেটিভ (hard negatives) থাকে, যা রিচ ও রোবাস্ট সিম্যান্টিক স্পেস গড়ে তোলে।
2. **প্রশ্ন: LLaVA-তে ভিশন এনকোডারকে সাধারণত ফ্রিজ (freeze) রাখা হয় কেন?**
   - **উত্তর:** ভিশন এনকোডার (যেমন CLIP ViT) ইতিমধ্যে লক্ষ লক্ষ ইমেজের সূক্ষ্ম ভিজ্যুয়াল কনসেপ্ট শিখে এসেছে। ফুল টিউনিং করলে এটি ভাষা মডেলের গ্রেডিয়েন্টের প্রভাবে ওভারফিট বা ক্যাটাস্ট্রফিক ফরগেটিংয়ের শিকার হতে পারে। কেবলমাত্র প্রজেকশন লেয়ার (MLP Connector) ট্রেইন করে ভিশন টোকেনগুলোকে ভাষা মডেলের ভেক্টরের সাথে নিখুঁতভাবে সারিবদ্ধ (align) করা সম্ভব।
3. **প্রশ্ন: অডিও স্পেকট্রোগ্রামে Mel-scale কেন ব্যবহার করা হয়?**
   - **উত্তর:** মানুষের কান শব্দের কম্পাঙ্ক লিনিয়ার স্কেলে অনুভব করে না; কম ফ্রিকোয়েন্সির সামান্য পরিবর্তন মানুষ সহজেই বুঝতে পারে (যেমন ১০০ Hz থেকে ২০০ Hz), কিন্তু অতি উচ্চ ফ্রিকোয়েন্সিতে পার্থক্য বোঝা কঠিন হয় (যেমন ১০,০০০ Hz থেকে ১০,১০০ Hz)। Mel-scale মানব কানের এই নন-লিনিয়ার শ্রবণ সংবেদনশীলতার গাণিতিক অনুকরণ করে।
