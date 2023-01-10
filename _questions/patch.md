---
html-id: patch
question: Why was the original patch insufficent to prevent further attacks?
---

On its own, the added sanity checks prevented the specific attacks of [Backendal, Haller and Paterson](https://mega-awry.io) and [Ryan and Heninger](https://eprint.iacr.org/2022/914). However, the patch did not address their root cause: the lack of key separation and integrity protection for stored keys. It is thus unsurprising that different attacks could be found.