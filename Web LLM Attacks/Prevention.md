# Preventing Web LLM attacks

## 1. Treat APIs given to LLMs as publicly accessible.(Never trust the LLM API to police for you)

    1. You should enforce access controls such as authentication to make a call to the API.

    2. Any access controls should be set on the API being called, not on the LLM. So set your restrictions on the acutal API rather than asking the LLM to do it.

> This can be helpful to reduce the potential for indirect prompt injection attacks, which are closely tied to permission issues and can be mitigated to some extent by proper privilege control.

## 2. DO NOT feed LLMs sensitive data
    Ways to avoid:

    1.Apply robust sanitization techniques to the model's training data set.

    2. Only feed data to the model that your lowest-privileged user may access. This is important because any data consumed by the model could potentially be revealed to a user, especially in the case of fine-tuning data

    3.Limit the model's access to external data sources, and ensure that robust access controls are applied across the whole data supply chain.

    4.Test the model to establish its knowledge of sensitive information regularly.

> It is theoretically possible to make a LLM enforce rules on it's API usage or block information disclosure BUT......

> it can usually be sometimes bypassed using a "ignore requests containing a payload" prompt. (often called jailbreaker prompts).

# Defending against AI-powered scanner vulnerabilities

1. Restrict scanner credentials and access controls.
    - Give the scanner least privilege, only what it needs to do its job.
2. Separate scanning identity from admin identity.
    - Use dedicated testing accounts that dont have admin permissions.

3, Apply server-side controls to the scanner.
    - Always enforce access controls at application or API level, dont rely on the LLM to enforce them

4. Treat all user-modifiable content as untrusted input.
    -Assume any text from the database is unsafe and a place of injection.
