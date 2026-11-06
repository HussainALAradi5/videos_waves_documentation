# Private accounts, notifications, and status

| ID | Actor | Flow | Result |
| --- | --- | --- | --- |
| UC-SOC-10 | User | Settings → Privacy → Private account on | Others see the profile header with a lock; videos, followers, and following need approval |
| UC-SOC-11 | User | Follow a private account | Button shows **Requested**; the owner gets a `follow_request` notification; tapping again cancels |
| UC-SOC-12 | Private account owner | Notifications → Follow requests → Accept or Decline (also on the requester's profile) | Accept creates the follow and notifies the requester; Decline is silent |
| UC-SOC-13 | Private account owner | Turn Private account off | Every pending request is accepted |
| UC-SOC-14 | Anyone | Home → Discover | Videos from public accounts only, never your own |
| UC-NTF-01 | User | Someone follows, likes, comments, replies, or adds them to a group | A live toast with Open, and an entry in the bell with an unread badge |
| UC-NTF-02 | User | Notifications → All or Unread; tap one; Mark all read | Tapping opens the link and marks it read; Mark all clears the badge |
| UC-STS-01 | User | Pick Online, Away, Busy, or Appear offline (inbox header) and a custom line (Settings → Status) | Chat partners see the coloured dot and "Busy · In a meeting"; Appear offline looks offline |
