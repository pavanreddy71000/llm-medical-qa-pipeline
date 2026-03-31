# Sample predictions

Selected predictions from the fine-tuned FLAN-T5 model on both datasets. Includes both successful outputs and failure cases — the failures are often more informative than the wins.

---

## PubMedQA — open-ended biomedical QA

### Correct and coherent

**Q:** Is there a link between gut microbiota and autoimmune diseases?
**Context:** Gut microbiota influences many aspects of human health and immune function.
**Predicted:** Gut microbiota plays an important role in the development of autoimmune diseases.
**Assessment:** Relevant, coherent, and directionally correct. The answer is vague though — it doesn't mention specific mechanisms (e.g., molecular mimicry, barrier dysfunction). A human expert would give a more nuanced response. Still, the model correctly identified the relationship from the context.

---

**Q:** Does vitamin D supplementation reduce the risk of respiratory infections?
**Context:** Vitamin D is known for immune support and may affect respiratory health.
**Predicted:** Vitamin D is an important component of a healthy immune system and may be a factor in the prevention of respiratory infections.
**Assessment:** Good. The hedged language ("may be a factor") is actually appropriate here since the evidence is mixed. The model avoided overclaiming, which is better than what the base model typically produces.

---

### Hallucination — fluent but factually wrong

**Q:** What are the long-term effects of chemotherapy on cognitive function?
**Context:** Chemotherapy is a common cancer treatment that may have side effects.
**Predicted:** Alzheimer's disease is a common side effect of chemotherapy.
**Assessment:** Factually wrong. Chemotherapy can cause cognitive issues (sometimes called "chemo brain"), but Alzheimer's disease is a specific neurodegenerative condition, not a side effect of chemo. The model latched onto the cognitive theme and jumped to the most prominent cognitive disease it knew. This is a textbook LLM hallucination — sounds confident and plausible, but the specific claim is unsupported. This kind of failure is dangerous in medical contexts.

---

### Vague / technically-not-wrong but unhelpful

**Q:** How effective is cognitive behavioral therapy for treating insomnia?
**Context:** CBT is a widely recommended treatment for various mental health conditions.
**Predicted:** Cognitive behavioral therapy is a treatment for insomnia.
**Assessment:** Not wrong, but not really an answer either. The question asked about effectiveness; the model just confirmed CBT is a treatment. The base model did something similar. This pattern — restating the premise instead of answering the question — showed up in roughly 15-20% of PubMedQA outputs.

---

## MedMCQA — multiple-choice medical exam questions

### Correct

**Q:** Which of the following is the most common cause of nephrotic syndrome in children?
**Options:** A. Membranous nephropathy B. Minimal change disease C. Focal segmental glomerulosclerosis D. IgA nephropathy
**Predicted:** Minimal change disease
**Correct:** Minimal change disease
**Assessment:** Correct. This is a well-established medical fact. The fine-tuned model consistently got standard clinical associations right when the answer was unambiguous.

---

### Incorrect — distractor confusion

**Q:** Which vitamin deficiency causes scurvy?
**Options:** A. Vitamin A B. Vitamin C C. Vitamin D D. Vitamin B12
**Predicted:** Vitamin A
**Correct:** Vitamin C
**Assessment:** Basic factual recall failure. Scurvy = Vitamin C is one of the most well-known vitamin associations. The model may have been thrown off by Vitamin A being the first option, or it may lack strong enough associations for this particular fact. This type of error (choosing the wrong option from a set where all options are in the same category) was the most common failure pattern in MedMCQA.

---

**Q:** The primary function of the sinoatrial node in the heart is to:
**Options:** A. Pump blood to the lungs B. Initiate the heartbeat C. Regulate blood pressure D. Filter blood
**Predicted:** Regulate blood pressure
**Correct:** Initiate the heartbeat
**Assessment:** The model picked an option that's related to cardiac function but is not the SA node's primary role. "Regulate blood pressure" is a plausible-sounding distractor — the heart is involved in blood pressure, and the SA node affects heart rate which affects blood pressure. But the direct answer is that the SA node is the heart's natural pacemaker. This shows the model reasoning by loose association rather than precise clinical knowledge.

---

**Q:** Which antibiotic is effective against Gram-positive bacteria?
**Options:** A. Ciprofloxacin B. Penicillin C. Metronidazole D. Amphotericin B
**Predicted:** Amphotericin B
**Correct:** Penicillin
**Assessment:** Amphotericin B is an antifungal, not an antibacterial. The model failed to distinguish between drug categories entirely. This suggests gaps in pharmacology knowledge that the limited training data (10% of MedMCQA) didn't cover adequately.

---

## Patterns observed

**What the fine-tuned model does well:**
- Generates fluent, grammatically correct answers consistently
- Picks up on context cues and produces topically relevant responses
- Handles standard, well-documented clinical associations in MCQ format
- Uses appropriately hedged language for uncertain topics (sometimes)

**Where it still fails:**
- Confident hallucinations on open-ended questions, especially when the context is thin
- Choosing semantically close distractors over correct answers in MCQ
- Restating the question premise instead of answering it (~15-20% of PubMedQA outputs)
- Pharmacology and detailed mechanism questions — likely undertrained in these subtopics
- No ability to say "I don't know" — always produces an answer, even when it shouldn't
