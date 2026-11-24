---
stage: confirm
triggers:
    - provider: slack
      event: message_posted
      filter:
        channels:
            - incidents
---
A message was posted in `#incidents`, the channel where people report that something is broken right now. Nobody has to mention the factory here. A new top-level report enters the respond lane at confirm, with the message and its links as the signal.

Check first what kind of message it is:
- a new top-level report is a new signal. Acknowledge it in its thread in one sentence, then confirm it;
- a reply in a thread whose task is still open continues that task at the stage it was in. Relay it, in the person's words, to the same responder conversation, and post the answer in the same thread. It does not restart confirm;
- a reply in a thread whose task is finished, a social message, a message from the factory itself or another bot, or a message plainly meant for another person ends silently.

This lane confirms the problem, finds the change that caused it, and opens a revert or a fix as a pull request. It does not deploy, roll back, or change anything in production; say so in the thread when a person asks for that. Post findings and the result in the same thread.
