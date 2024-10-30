# PromptGuard: Exploring Secure Execution of Encrypted Prompts in LLMs

### Introduction

In a world where data privacy is paramount, securing prompt content in large language models (LLMs) like ChatGPT has become essential. PromptGuard is an experimental concept aimed at securely processing sensitive prompts without exposing their content, thus ensuring user data privacy while maintaining functional output. This project began with a simple idea: **can we fully encrypt prompts so that LLMs can execute them without needing to decode sensitive information?**

The project seeks to address two core needs:
1. **Ensuring privacy**: Keeping sensitive or proprietary information within prompts fully encrypted during processing.
2. **Maintaining functionality**: Allowing LLMs to execute and respond accurately to prompts without needing to access the original, unencrypted content.

### Project Background

Consider the example of a mathematical assistant prompt:
> "You are a math assistant. Please output the first *n* digits of pi as specified by the user. If the user provides an invalid input (like a negative number or non-numeric input), provide an error message."

With sensitive content embedded in this prompt, the objective was to explore how we might securely transmit and execute this request without exposing the original prompt text to potential leaks.

### Technical Challenges

Despite the potential benefits, several significant technical challenges emerged, making the project challenging to achieve with current LLM technology:

1. **Lack of Native Decryption Support**: 
   Current LLMs cannot perform traditional cryptographic decryption, meaning they cannot natively decode encrypted data provided within prompts. Without an embedded decryption capability, LLMs treat encrypted text as incomprehensible characters.

2. **Security Restrictions**: 
   For security reasons, LLMs like ChatGPT are designed to avoid executing or interpreting encrypted or heavily obfuscated content. This prevents accidental or malicious misuse but also limits legitimate use cases where sensitive information needs protection.

3. **Absence of Isolated Execution Environments**: 
   Even if decryption were possible, current LLM platforms lack a “sandbox” environment for safely interpreting decrypted content without risk of residual data exposure. Any decrypted information would, theoretically, remain accessible to the model’s output logs or session memory.

### Explored Solutions and Limitations

To address these limitations, we explored several partial solutions to see if LLMs could interpret and act on encrypted prompts while ensuring a degree of content protection:

#### 1. Encapsulation and Segmentation

One approach was to encapsulate or segment the prompt content, using structured tags or pseudocode to convey the intent. For example:
```plaintext
<task>
  <role>Math Assistant</role>
  <main_task>Output user-specified length of pi digits.</main_task>
  <error_handling>If the input is invalid (negative or non-numeric), respond with an error message.</error_handling>
</task>
```
This method can protect the intent and structure of the prompt but does not prevent the model from accessing the actual text. It’s more of a “soft” privacy measure than a true encryption solution.

#### 2. Pseudocode Commands

Using pseudocode, we can partially encode instructions in a structured way:
```plaintext
MathAssistant {
    role = "Math Assistant";
    function output_PI(length) {
        if length > 0 {
            return PI[length];
        } else {
            return error_message("Invalid input");
        }
    }
}
```
This pseudocode allows the model to understand the request in parts but does not fully hide prompt details.

#### 3. Partial Encryption with Hints

Another attempt was to partially encrypt sensitive segments, coupled with hints for LLMs to interpret specific keywords. However, without native decryption, this approach resulted in incomplete execution and limited privacy.

### Conclusions and Future Directions

Through PromptGuard, we discovered that while partial privacy measures can add a layer of obscurity, true prompt encryption remains technically unfeasible in current LLM frameworks. Without integrated decryption capabilities, secure, end-to-end prompt execution is not achievable. However, this exploration underlines a critical need for LLMs to support privacy-preserving computation, such as isolated execution environments and secure sandboxing.

PromptGuard stands as an initial exploration of encrypted prompt processing, highlighting both the promise and the limitations of privacy protection within LLMs. We hope this project can inspire further innovations in secure prompt execution and privacy-preserving AI.
