# Local Code Companion: Powering Your Development with Ollama & LLMs

Welcome to your ultimate guide for setting up and using a powerful, local AI coding assistant. This repository provides everything you need to install Ollama, build customized coding models, and choose the best Large Language Model (LLM) for your needs. Run state-of-the-art models on your own machine, ensuring privacy and offline accessibility.

## 1. Installation: Get Started with Ollama

Ollama is a lightweight and extensible tool for running LLMs on your local machine. Follow the instructions for your operating system below.

### System Requirements
* **macOS:** 11.0 Big Sur or later
* **Windows:** Windows 10 or later (with WSL2)
* **Linux:** A modern distribution
* **RAM:** 8 GB is the minimum for 3B and 7B models. For larger models (13B+), 16 GB or more is recommended.
* **Disk Space:** At least 5 GB of free space for models.

### macOS

1.  **Download Ollama:** Visit the [Ollama download page](https://ollama.com/download) and download the macOS version.
2.  **Install:** Open the downloaded `.dmg` file and drag the Ollama application into your Applications folder.
3.  **Run Ollama:** Launch the Ollama application from your Applications folder. A new icon will appear in your menu bar.
4.  **Verify Installation:** Open your terminal and run the following command to ensure Ollama is installed correctly:
    ```bash
    ollama --version
    ```

### Linux

1.  **Installation Script:** The easiest way to install Ollama on Linux is by using the official installation script. Open your terminal and run:
    ```bash
    curl -fsSL [https://ollama.com/install.sh](https://ollama.com/install.sh) | sh
    ```
2.  **Verify Installation:** Once the script completes, verify the installation by running:
    ```bash
    ollama --version
    ```

### Windows (with WSL2)

1.  **Install WSL2:** Ollama on Windows requires the Windows Subsystem for Linux 2 (WSL2). If you don't have it installed, open PowerShell as an administrator and run:
    ```bash
    wsl --install
    ```
    Reboot your system after the installation is complete.
2.  **Download Ollama:** Go to the [Ollama download page](https://ollama.com/download) and download the Windows version.
3.  **Install:** Run the downloaded `.exe` installer and follow the on-screen instructions.
4.  **Verify Installation:** Open a new PowerShell or Command Prompt window and type:
    ```bash
    ollama --version
    ```

## 2. Building Models with `Modelfile`

The `Modelfile` is a powerful feature in Ollama that allows you to create custom models with specific behaviors and parameters. This is perfect for tailoring a general model into a specialized coding assistant.

### `Modelfile` Structure and Parameters

A `Modelfile` is a simple text file with a series of instructions:

* **`FROM`** (Required): Specifies the base model to build upon (e.g., `codegemma:7b`).
* **`SYSTEM`**: Sets a system-level prompt to define the model's persona and task (e.g., "You are an expert Python programmer...").
* **`PARAMETER`**: Adjusts the model's settings.
* **`TEMPLATE`**: Defines the prompt structure for the model.

**Key Parameters for Coding Assistants:**

| Parameter        | Description                                                                        | Recommended Value (for Coding) |
| ---------------- | ---------------------------------------------------------------------------------- | ------------------------------ |
| `temperature`    | Controls the randomness of the output. Lower values produce more deterministic code. | `0.0` - `0.2`                    |
| `num_ctx`        | The context window size (in tokens). A larger window helps with code context.      | `4096` or `8192`                 |
| `stop`           | A sequence of characters that will stop the model's generation.                    | `"```"`, `"\n\n"`                |
| `top_k`          | Restricts the model to the `k` most likely tokens.                                 | `40`                           |
| `top_p`          | Uses nucleus sampling; considers tokens with a cumulative probability of `p`.      | `0.9`                          |
| `repeat_penalty` | Penalizes the model for repeating tokens.                                          | `1.1`                          |

### Example `Modelfile` for a Python Coding Assistant

This `Modelfile` creates a specialized Python assistant based on `codellama`.

```modelfile
# --- Gemma Coding Assistant Modelfile ---

# Base Model: We use codegemma's instruction-tuned model for its strong
# performance in understanding and generating code.
FROM codegemma:7b-instruct

# Model Parameters: These are tuned for a coding assistant.
# Lower temperature for more deterministic and accurate code.
PARAMETER temperature 0.1
# Nucleus sampling for a balance of creativity and coherence.
PARAMETER top_p 0.9
PARAMETER top_k 40
# Discourage repetitive code generation.
PARAMETER repeat_penalty 1.15
# Set a reasonable context window for understanding larger code snippets.
PARAMETER num_ctx 4096

# System Prompt: This defines the persona and expertise of our coding assistant.
SYSTEM """
You are an expert AI programming assistant. Your primary goal is to help users write clean, efficient, and well-documented code.

When asked to write code, follow these guidelines:
- **Clarity is Key:** Write code that is easy to understand and maintain.
- **Provide Explanations:** Before presenting the code, briefly explain the logic and approach you will take.
- **Use Code Blocks:** Always enclose code in markdown code blocks with the appropriate language identifier (e.g., ```python).
- **Offer Alternatives:** When relevant, suggest alternative approaches or libraries and explain the trade-offs.
- **Error Handling:** For more complex requests, include basic error handling.
- **Stay Concise:** Keep your explanations and code snippets to the point.
"""

# Template: This is the specific instruction template for the codegemma instruct model.
# It ensures the model correctly interprets the user's prompt.
TEMPLATE """<|end_of_turn|>
user
{{ .System }}
{{ .Prompt }}<|end_of_turn|>
model
"""

# Stop Tokens: These are crucial for generating clean and complete code snippets.
# They prevent the model from generating extraneous text after the code is complete.
PARAMETER stop "<|end_of_turn|>"
PARAMETER stop "<|file_separator|>"
PARAMETER stop "```"
PARAMETER stop "\n\n"
```
### How to Build and Run Your Custom Model
1. Save the Modelfile: Save the content above to a file named Modelfile (no extension).

2. Build the Model: In your terminal, navigate to the directory containing the Modelfile and run:

```Bash

ollama create gpt-4.1 -f ./Modelfile
```
3. Run Your Custom Model: You can now interact with your newly created assistant:

```Bash

ollama run gpt-4.1
```
## 3. Comparison of LLM Models for Coding
Choosing the right model is crucial for the best coding assistance experience. Here's a comparison of top models available on Ollama, optimized for code-related tasks.
| Model          | Key Features & Strengths                                                                                                                                                                   | Ideal Use Case                                                                                                              |
|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| codellama      | Versatile & Robust: Excellent all-around performance in code generation, completion, and instruction following. Has specialized versions for Python (:python) and code completion (:code). | General-purpose coding, Python development, and in-editor code completion. A great starting point for most users.           |
| codegemma      | Modern & Efficient: Based on Google's Gemini architecture. Offers strong performance, especially with the instruction-tuned model. Known for its speed and accuracy.                       | Code generation, creating and explaining complex algorithms, and as a general-purpose coding chatbot.                       |
| deepseek-coder | Highly Trained on Code: Pre-trained on a massive dataset with a high percentage of code. Excellent at understanding and generating complex code structures.                                | Generating boilerplate code, complex algorithms, and for developers who need support in both English and Chinese.           |
| starcoder2     | Broad Language Support: Trained on a vast corpus of permissively licensed code from over 80 languages. Great for developers working on polyglot projects.                                  | Code generation in a wide variety of programming languages, especially for projects with diverse tech stacks.               |
| qwen2.5-coder  | Advanced Code Intelligence: State-of-the-art performance in code generation, reasoning, and especially code fixing and debugging.                                                          | Debugging complex issues, refactoring code, and as a powerful assistant for understanding and improving existing codebases. |

