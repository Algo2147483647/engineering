# Diffusion Relationships

Referral-based diffusion relationships refer to a relationship attribution and progress computation mechanism designed for growth-oriented scenarios. In such a mechanism, a user acts as a `leader` and initiates a `diffusion instance`, within a specific business context (`scene`). The system then generates a shareable access point (`share code`), such as a link, invitation code, or referral code. When other users, referred to as `followers`, enter through these access points, the system attributes their participation to the corresponding leader and diffusion instance, and persists this participation as a relationship record (`relation`). After validity checks and contribution measurement, these relationship records are further used to update instance-level progress and support downstream business policy decisions.

The mechanism serves three core purposes:

1. recording a diffusion activity;
2. recording relationships between users; and
3. making these relationships available to business applications.

> **Initiation of diffusion → Instance creation → Access point generation → User participation → Attribution → Relationship persistence → Validity and contribution assessment → Progress update → Policy output**

In growth strategies such as new-user invitation, friend-assisted campaigns, group buying, referral codes, sharing-based rebates, and member-get-member programs, businesses need to record and manage relationship data generated through user-driven diffusion. They also need to use these relationships to support business policy decisions.

This mechanism abstracts fragmented and often inconsistent diffusion paths into a stable relationship model and progress computation framework. From the user perspective, it makes the participation process transparent: who initiated the activity, through which access point a user joined, whether the participation is valid, what the current progress is, and whether the conditions for rewards or group formation have been satisfied. From the business perspective, it provides a unified foundation for relationship records and progress computation, enabling different growth mechanisms to reuse the same underlying capabilities.