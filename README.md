# cxr-rag
RAG for chest X-ray report generation with BiomedCLIP, MedGemma, LoRA and sentence-level claim verification. Research prototype.

Topics

rag, medical-imaging, radiology, chest-xray, vision-language-model, report-generation, medgemma, biomedclip, lora, faiss, pytorch, nlp, computer-vision

cxr-rag is a research prototype that drafts chest X-ray reports with retrieval-augmented generation. For each X-ray, it retrieves similar past cases using BiomedCLIP embeddings and FAISS, passes them as reference to a vision-language model (MedGemma), and then checks every generated sentence against the retrieved evidence with an NLI verifier to flag unsupported claims. A 2x2 experiment compares zero-shot and LoRA fine-tuned models, each with and without retrieval, and measures how much of any gain comes from genuine evidence use versus copying. Built on IU X-Ray (NLM Open-i). Not a medical device and not for clinical use.


