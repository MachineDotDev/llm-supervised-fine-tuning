[![Machine](./docs/github-repo-banner.png)](https://machine.dev/)

Machine supercharges your GitHub Workflows with seamless GPU acceleration. Say goodbye to the tedious overhead of managing GPU runners and hello to streamlined efficiency. With Machine, developers and organizations can effortlessly scale their AI and machine learning projects, shifting focus from infrastructure headaches to innovation and speed.


# Supervised Fine-Tuning (SFT)

This repository provides a complete, automated workflow for GPU-accelerated supervised fine-tuning (SFT) of Llama 3.2 models using Unsloth. Leveraging GitHub Actions powered by Machine.dev, it simplifies fine-tuning conversational models using popular datasets such as FineTome-100k and OpenAssistant's oasst1, optimizing models through LoRA (Low-Rank Adaptation).

The workflow supports automatic checkpointing and retry mechanisms to handle training interruptions seamlessly.

We have followed the guides provided by unsloth from their [Notebook](https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Llama3.2_(1B_and_3B)-Conversational.ipynb)

> **ℹ️ Info:** The code in this repo was taken from the unsloth repository and is used
for the training process. The code is a great resource for understanding the training
process and the techniques used to fine-tune the model.

---

### ✨ **Key Features**

- **⚡ GPU Acceleration:** Efficiently fine-tune conversational models using GPUs via [Machine](https://machine.dev)
- **🗣️ Conversational Models:** Quickly fine-tune Llama 3.2 for conversational tasks
- **📚 Popular Datasets:** Easily train on widely-used datasets such as FineTome-100k and oasst1
- **🚀 LoRA Optimizations:** Utilize Low-Rank Adaptation (LoRA) for memory-efficient training
- **🔄 Auto-Retry Functionality:** Automatically resume training from checkpoints on spot instance interruptions
- **📤 Hugging Face Hub:** Automatically push trained models and checkpoints directly to Hugging Face repositories
- **🛠️ Customizable Training:** Flexibly configure training parameters like LoRA rank, learning rate, and maximum sequence length
- **📈 Enhanced Inference:** Seamlessly switch from fine-tuning to optimized inference mode

---

### 📁 **Repository Structure**

```
├── .github/workflows/
│   ├── supervised-fine-tuning.yaml                   # Basic supervised fine-tuning workflow
│   └── supervised-fine-tuning-with-retry.yaml        # Fine-tuning workflow with checkpointing and retry
├── .github/actions/check-runner-interruption/
│   └── action.yaml                                   # Action to detect spot instance interruptions
├── supervised_fine_tuning.py                         # Script for basic fine-tuning and inference
├── supervised_fine_tuning_checkpointed.py            # Extended script with checkpointing and retry
└── requirements.txt                                  # Python dependencies
```

---

### ▶️ **Getting Started**

#### 1. **Use This Repository as a Template**
Click the **Use this template** button at the top of this page to create your own copy.

#### 2. **Set Up GPU Runners**
Ensure your repository uses Machine GPU-powered runners. No additional configuration is required if you're already using Machine.dev.

#### 3. **Configure Hugging Face Access**

1. Create a Hugging Face access token with write permissions.
2. Add this token as a repository secret named `HF_TOKEN` in your GitHub repository settings.

#### 4. **Run the Workflow**

Trigger the workflow manually in GitHub Actions (`workflow_dispatch`).

You can choose between two workflows:

- `supervised-fine-tuning.yaml`: Basic supervised fine-tuning without checkpointing
- `supervised-fine-tuning-with-retry.yaml`: Training with automatic checkpointing and retry on spot instance interruptions

##### Basic Supervised Fine-Tuning Parameters

```yaml
inputs:
  source_model: 'unsloth/Llama-3.2-3B-Instruct'
  data_set: 'mlabonne/FineTome-100k'
  max_seq_length: '2048'
  lora_rank: '16'
  max_steps: '100'
  learning_rate: '2e-4'
  hf_target_repo: 'your-hf-repo-name'
```

##### Fine-Tuning with Retry Workflow Parameters

The `supervised-fine-tuning-with-retry.yaml` workflow includes additional parameters:

```yaml
inputs:
  attempt:
    type: string
    description: 'The attempt number'
    default: '1'
  max_attempts:
    type: number
    description: 'The maximum number of attempts'
    default: 5
  # (All parameters from the basic supervised fine-tuning workflow are also included)
```

##### How the Retry Mechanism Works

The retry mechanism ensures training progress isn't lost due to spot instance interruptions:

1. Workflow starts with a specified attempt number.
2. If training completes successfully, the workflow ends.
3. If a spot instance interruption occurs:
   - The `check-runner-interruption` action detects the interruption.
   - The workflow calculates the next attempt number.
   - If within the maximum attempts limit, it triggers a new workflow run with an incremented attempt number.
   - All original parameters are preserved for the new attempt.
4. The script (`supervised_fine_tuning_checkpointed.py`) automatically saves checkpoints to Hugging Face Hub.
5. New attempts resume training from the latest checkpoint on Hugging Face.

#### 5. **Monitor and Review Results**

- Training progress, metrics, and GPU usage statistics are logged during each workflow execution.
- The fine-tuned model and checkpoints are automatically pushed to your specified Hugging Face repository.

---

### 🔑 **Prerequisites**

- GitHub account
- Access to [Machine](https://machine.dev) GPU-powered runners
- [Hugging Face](https://huggingface.co) account for model hosting

_No local installation necessary—all processes run directly within GitHub Actions._

---

### 📄 **License**

This repository is available under the [MIT License](LICENSE).

---

### 📌 **Notes**

- This supervised fine-tuning template specifically targets Llama 3.2 models for conversational tasks but can easily be adapted for other models, datasets, and tasks with minimal modifications.

- This repository is currently open for use as a template. While public forks are encouraged, we are not accepting Pull Requests at this time.

_For questions or concerns, please open an issue._


## Machine runtime limits

These workflows run on Machine GPU runners. Install the GitHub App on this
repository before starting a job. Start with $10 of free GPU time. No charge,
no commitment.

Unpaid teams can run one job at a time, for up to 30 minutes. Buy compute or a
plan before running a longer training or evaluation job. A larger
`timeout-minutes` value in GitHub doesn't remove the platform limit.

See [billing and limits](https://machine.dev/docs/guides/billing/) for current limits.
