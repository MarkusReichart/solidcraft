# Evidence for Agent chat usability feature requests

Captured from installed SolidCraft 0.29.0 on Windows 11 using an isolated profile, Sample Plate and a deterministic loopback-only reply. No external model, private design, account details or credentials are shown.

- reply-no-message-copy.png: right-clicking the assistant reply offered no message Copy menu; the existing Copy button belongs only to the code block. The chat header also has no conversation-export action.
- composer-context-menu.png: control from the same historical test session; the editable composer has its own standard context menu. The extra sample body comes from the separately reported clipboard-routing reproduction, not from right-clicking.
- select-all-only-first-paragraph.jpg: after Ctrl+A in the popped-out reply, only the first paragraph is selected; the heading, second paragraph, list and code remain unselected. Ctrl+C copied exactly that paragraph. Native capture bytes are kept unchanged with the matching JPEG extension.

Runtime evidence is from 0.29.0, not a claim that the latest main was built and run. Relevant current source and related work are linked in the issues. These feature requests do not fix or re-report the separate CAD clipboard-routing bug (PR #10576).

Images are kept in a separate orphan evidence branch in the reporter fork, never in a code PR. Keep this branch while the issues reference it.
