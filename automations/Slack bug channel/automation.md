---
stage: triage
triggers:
    - provider: slack
      event: message_posted
      filter:
        channels:
            - bugs
---
A message was posted in `#bugs`, this factory's bug intake channel. Nobody has to mention the factory here: every new top-level report in this channel is work, and it enters at triage.

Check first what kind of message it is:
- a new top-level report is new work. Acknowledge it in its thread, then triage it;
- a reply in a thread whose task is still open continues that task at the stage it was in. It is not a new report, and it does not restart triage;
- a reply in a thread whose task is finished, a social message, a message from the factory itself or another bot, or a message plainly meant for another person ends silently.

Triage checks for existing work, records the issue with the thread linked, and decides what comes next. Post questions, status, and the result in the same thread.
