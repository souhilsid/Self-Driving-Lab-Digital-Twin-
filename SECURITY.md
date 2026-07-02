# Security Policy

Do not commit LiveKit API secrets, participant tokens, `.livekit.env` files, Streamlit secrets, or generated token bundles.

Runtime deployments must mint short-lived participant tokens on a trusted server. Unity and browser clients must receive participant tokens only; they must never receive the LiveKit API secret.

Report suspected credential exposure privately to the corresponding author or AISCIA Informatics. Revoke exposed credentials before publishing incident details.
