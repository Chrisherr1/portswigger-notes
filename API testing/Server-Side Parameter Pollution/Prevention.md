# Preventing server-side parameter pollution

To prevent server-side parameter pollution:

1. Use an **allowlist** to define characters that don't need encoding.
2. Make sure all other user input is **encoded** before it's included in a server-side request.
3. Make sure that all input adheres to the **expected format and structure**.
