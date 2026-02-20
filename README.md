# Telugu Language Model (Curriculum Learning)

This project implements curriculum learning for training small GPT-2 models on Telugu text data. The project includes data preparation, tokenizer creation, model training, and comprehensive evaluation.

## Project Overview

The project trains GPT-2  models (very small architecture) on Telugu text using curriculum learning. It includes:
- **Data preprocessing and curriculum creation** from Telugu news articles
- **Custom BPE tokenizer** trained on Telugu corpus
- **Multiple model training runs** with different random seeds for reproducibility
- **Comprehensive evaluation** including text generation and grammaticality judgments

## Project Structure

```
telugu_model/
├── Tokenizer_Telugu.ipynb              # BPE tokenizer creation
├── Telugu_Sentence_Scoring_2.ipynb     # Data preparation and curriculum creation
├── training.ipynb.ipynb                # Model training and evaluation
├── curriculum_runs_summary.csv         # Training results summary
├── curriculum_perplexities.csv         # Perplexity metrics
├── all_seeds_generation_results.csv   # Text generation results
└── all_seeds_minimal_pairs.csv        # Minimal pair evaluation results
```




## Workflow

### 1. Tokenizer Creation (`Tokenizer_Telugu.ipynb`)

Creates a BPE (Byte Pair Encoding) tokenizer for Telugu text:
- Vocabulary size: 32,000
- Special tokens: `<pad>`, `<unk>`, `<bos>`, `<eos>`
- Trained on combined curriculum and 17M token datasets
- Output: `telugu_tokenizer/` directory

### 2. Data Preparation (`Telugu_Sentence_Scoring_2.ipynb`)

Processes Telugu news articles and creates curriculum-ordered training data:

**Steps:**
1. Downloads Telugu news articles from Hugging Face (`SuryaKrishna02/aya-telugu-news-articles`)
2. Text normalization using Indic NLP library
3. Cleaning: removes URLs, emails, HTML tags, non-Telugu symbols
4. Filters sentences by Telugu character ratio (>40%) and length (3-200 tokens)
5. Scores sentences based on:
   - Frame frequency (first 3 words)
   - Utterance length (shorter = easier)
   - Mean word length (shorter = easier)
   - Mean word frequency (higher = easier)
6. Orders sentences by difficulty (easy to hard)
7. Generates:
   - `telugu_curriculum.txt` - All ordered sentences (~214K sentences, ~17M tokens)
   - `telugu_17M_tokens.txt` - Subset with exactly 17M tokens

### 3. Model Training (`training.ipynb.ipynb`)

Trains GPT-2 like  models with curriculum learning:

**Model Architecture:**
- Embedding dimension: 128
- Number of layers: 2
- Number of attention heads: 2
- Context length: 128 tokens
- Vocabulary size: 32,000 (from tokenizer)

**Training Configuration:**
- Batch size: 4 per device
- Gradient accumulation: 8 steps
- Effective batch size: 32
- Learning rate: 5e-4
- Epochs: 12
- Warmup steps: 1,000
- Evaluation steps: 5,000
- Optimizer: AdamW with cosine learning rate schedule

**Training Modes:**
- **Curriculum Learning**: Sentences ordered from easy to hard
**Multiple Runs:**
- 10 different random seeds: `[42, 123, 456, 789, 1011, 2024, 3035, 4046, 5057, 6068]`
- Each run saves:
  - Trained model in `gpt-telugu/{mode}_seed_{seed}/final_model/`
  - Training statistics in `training_stats.csv`
  - Run metadata in `run_info.json`





## Output Files

### Training Results
- `curriculum_runs_summary.csv` - Summary of all training runs
- `curriculum_perplexities.csv` - Perplexity values for each seed

### Evaluation Results
- `all_seeds_generation_results.csv` - Text generation outputs for all seeds
- `all_seeds_minimal_pairs.csv` - Minimal pair perplexity comparisons



## Key Features

1. **Curriculum Learning**: Sentences ordered from easy to hard for better learning
2. **Reproducibility**: Multiple seeds for statistical robustness
3. **Comprehensive Evaluation**: Both generation and grammaticality tests
4. **Telugu-Specific**: Custom tokenizer and normalization for Telugu text

## Notes

- The model architecture is intentionally very small ("GPT-2 Wee") for experimental purposes
- Training uses curriculum learning (easy-to-hard ordering)
- All models are trained on the same data, only ordering differs
- Evaluation includes both quantitative (perplexity) and qualitative (generation, minimal pairs) metrics



