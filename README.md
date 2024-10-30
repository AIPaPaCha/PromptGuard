# PromptGuard: Exploring Secure Execution of Encrypted Prompts in LLMs

### Introduction

In a world where data privacy is paramount, securing prompt content in large language models (LLMs) like ChatGPT has become essential. PromptGuard is an experimental concept aimed at securely processing sensitive prompts without exposing their content, thus ensuring user data privacy while maintaining functional output. This project began with a simple idea: **can we fully encrypt prompts so that LLMs can execute them without needing to decode sensitive information?**

The project seeks to address two core needs:
1. **Ensuring privacy**: Keeping sensitive or proprietary information within prompts fully encrypted during processing.
2. **Maintaining functionality**: Allowing LLMs to execute and respond accurately to prompts without needing to access the original, unencrypted content.

### Project Background

Consider the example of a mathematical assistant prompt:
> "You are a math assistant. Please output the first *n* digits of pi as specified by the user. Provide an error message if the user provides an invalid input (like a negative number or non-numeric input)."

With sensitive content embedded in this prompt, the objective was to explore how we might securely transmit and execute this request without exposing the original prompt text to potential leaks.

What I tried:
I used a reversible encryption algorithm, such as AES (Advanced Encryption Standard), to encrypt this Prompt content. An encryption key and an initialization vector (IV) will be used to ensure successful decryption. In AES encryption, the IV typically acts as a random seed.

### Encryption Steps:
- **Algorithm**: AES-256-CBC (using a 256-bit key and CBC mode)
- **Key**: A key is generated to ensure encryption strength (a 32-byte random value).
- **IV**: Initialization Vector (IV), typically a 16-byte random value, used to guarantee the uniqueness of the encrypted result.


---

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad, unpad
import base64

# Prompt content to encrypt
prompt_text = (
    "You are a math assistant, and you need to output a user-specified length of pi digits. "
    "Once ready, you can ask the user: 'How many digits of pi do you need?' "
    "If the user provides an invalid input, such as a negative number or non-numeric input, "
    "you should give a rejection response. "
    "Execute this Prompt to ensure you understand its content."
)

# AES-256-CBC encryption setup
key = get_random_bytes(32)  # 256-bit key
iv = get_random_bytes(16)  # 16-byte IV

# Create AES cipher in CBC mode
cipher = AES.new(key, AES.MODE_CBC, iv)
ciphertext = cipher.encrypt(pad(prompt_text.encode(), AES.block_size))

# Encode ciphertext and IV in base64 for easy sharing
encoded_ciphertext = base64.b64encode(ciphertext).decode()
encoded_iv = base64.b64encode(iv).decode()
encoded_key = base64.b64encode(key).decode()

encoded_ciphertext, encoded_key, encoded_iv
```

**Encryption complete.** Below is the encrypted information along with the key and initialization vector (IV) needed for decryption:

- **Encryption Algorithm**: AES-256-CBC
- **Ciphertext** (base64-encoded): `hsXE0sOj5hJnGy9+3dwJ2I1+mWCypiurB+eqOrnJ+MUnkOzGNYAgn6HFeiLD1bpj/huOF1pxuDV/TZqgUCOMhrIdwJRgn5OG4GmAMClOOi6a/XRoQyVnY5Gs9OOpvsx6yaU6m0/iqdyTMeeUXFl/1N5SCGc6vXMyzMefTqf+OTBv6G+Vo49RAv+Z8YOY9WMI1stoj6h+J7MxGKGgKDPvhgs3AvpiSXH4OCa6C7G2mG3CF5T1SipaHz8HCDfzCkByvXpOD2pKeRUd6pXB0z/LUN8nCqfDtFwUxzSTYfm69eB3X4GnhMIpHVzQ5L8HUWj+2F1gL3j97MT5UpZE3cu9bWjX3IOGgQbk2voOZa3vOqo+Vx5W0z4OdEsnSBbPTyJt6sQIoYUiRF1NfWZXl1bjyrK+ayDQTAP/ODf9vYscd19rs+LKdyF10TxzE12NCQxk2cbIm+GKaf15+U1zNRGzZ1/eaJbYSxLd2rZFU7QxZPZ44XoQfuxgY+ZNQLfAmHONz09xZ6wVe4AsnBI1qfg/Pg==`
- **Key** (base64-encoded): `PNX+3L/EYGAwSaBt18DhFkx3DBHhOidoTrs1/18qbf4=`
- **IV** (base64-encoded): `5mpYb5qEY12jX7MT2CkoBw==`

And, then when I tried to ask ChatGPT to help me to decode and run:

>I have an encrypted prompt, I need to you to decrypt it and run it, could you try? <br>
>Encryption Algorithm: AES-256-CBC <br>
>Ciphertext (base64-encoded): hsXE0sOj5hJnGy9+3dwJ2I1+mWCypiurB+eqOrnJ+MUnkOzGNYAgn6HFeiLD1bpj/huOF1pxuDV/TZqgUCOMhrIdwJRgn5OG4GmAMClOOi6a/XRoQyVnY5Gs9OOpvsx6yaU6m0/iqdyTMeeUXFl/1N5SCGc6vXMyzMefTqf+OTBv6G+Vo49RAv+Z8YOY9WMI1stoj6h+J7MxGKGgKDPvhgs3AvpiSXH4OCa6C7G2mG3CF5T1SipaHz8HCDfzCkByvXpOD2pKeRUd6pXB0z/LUN8nCqfDtFwUxzSTYfm69eB3X4GnhMIpHVzQ5L8HUWj+2F1gL3j97MT5UpZE3cu9bWjX3IOGgQbk2voOZa3vOqo+Vx5W0z4OdEsnSBbPTyJt6sQIoYUiRF1NfWZXl1bjyrK+ayDQTAP/ODf9vYscd19rs+LKdyF10TxzE12NCQxk2cbIm+GKaf15+U1zNRGzZ1/eaJbYSxLd2rZFU7QxZPZ44XoQfuxgY+ZNQLfAmHONz09xZ6wVe4AsnBI1qfg/Pg== <br>
>Key (base64-encoded): PNX+3L/EYGAwSaBt18DhFkx3DBHhOidoTrs1/18qbf4= <br>
>IV (base64-encoded): 5mpYb5qEY12jX7MT2CkoBw==


ChatGPT 4o:<br>
<img width="736" alt="image" src="https://github.com/user-attachments/assets/be9de1a8-b7bb-4338-bdc7-d2814443b7d7">

Claude 3.5:<br>
<img width="701" alt="image" src="https://github.com/user-attachments/assets/b08152e5-79dc-44d5-8f34-6584a3b0229f">

Gemini:<br>
<img width="672" alt="image" src="https://github.com/user-attachments/assets/9f4e17b4-06fd-4096-9a8b-16b64c46a234">



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


Happy to discuss more about it:
https://www.linkedin.com/in/charleslmq/
