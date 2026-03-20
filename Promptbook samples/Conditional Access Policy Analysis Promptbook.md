![Security CoPilot Logo](https://github.com/Azure/Copilot-For-Security/blob/main/Images/ic_fluent_copilot_64_64%402x.png)
# Conditional Access Policy Analysis Promptbook

**Required plugins**: Microsoft Entra, Natural Language to KQL to Microsoft Sentinel

**Required Input**: Conditional Access Policy Name or ID: <PolicyNameOrId>

**Description**: Performs a comprehensive deep-dive analysis of an individual Conditional Access policy within the context of all other policies in the tenant. Produces a structured table covering intent, ownership, scope, exclusions, controls, overlaps, lifecycle, and critical thinking insights that go beyond binary configuration checks.

1. Retrieve the full configuration of the target Conditional Access policy and list every other Conditional Access policy in the tenant so the analysis has full context.
 ```
Using the Microsoft Entra plugin, retrieve the complete configuration of the Conditional Access policy identified by <PolicyNameOrId>. Also list all other Conditional Access policies in the tenant with their names, states, and targeted applications so we can assess overlap and gaps later.
 ```
2. Determine the single-sentence intent of this policy using the "If X, then Y, to reduce Z" format and identify who owns and approves changes to it.
 ```
Based on the full configuration of the Conditional Access policy <PolicyNameOrId>, provide the following in a table with columns Area, Question, Answer / Notes, and Evidence / Link:
Row 1 – Intent: What is the single-sentence intent of this policy expressed as "If [condition/users], then [grant/session control], to reduce [risk]"? Derive this from the conditions, grant controls, and targeted users — do not simply restate the display name.
Row 2 – Ownership: Based on audit logs and policy metadata, who most recently created or modified this policy? Recommend who should own it (e.g., Identity team, Security Operations) and approve changes, and explain why.
 ```
3. Assess the current status, user/group scope, and scope maintenance posture.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add these rows:
Row 3 – Status: Is the policy currently Enabled, Report-only, or Disabled? If Report-only or Disabled, note how long it has been in that state and whether that is appropriate.
Row 4 – Scope: Which users, groups, or directory roles are included? Explain the likely rationale for this scope — for example, is it targeting all users, privileged roles only, or a specific department? Flag if the scope seems too broad or too narrow compared to the policy's stated intent.
Row 5 – Scope Maintenance: Which groups or role assignments feed the inclusion list? Assess whether those groups appear to be dynamically managed or manually maintained, and recommend a review cadence (e.g., quarterly access review). Note any risk if membership goes stale.
 ```
4. Analyze exclusions and identify any risky gaps in coverage.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add this row:
Row 6 – Exclusions: List every exclusion — users, groups, applications, locations, and device platforms. For each exclusion, explain the likely business justification and flag any that look risky. Specifically call out: (a) break-glass / emergency access accounts — are they correctly excluded and are they protected by a separate policy? (b) service accounts or app registrations — could they be an unmonitored bypass? (c) any exclusion that effectively nullifies the policy for a high-risk population.
 ```
5. Evaluate the targeted resources and whether critical applications are missing.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add this row:
Row 7 – Resources: Which cloud applications or user actions does this policy target? If it targets "All cloud apps", note what is implicitly included. If it targets specific apps, list them and assess whether any security-critical apps are missing (e.g., Azure portal, Microsoft Graph, Exchange Online, SharePoint Online, Azure Management). Cross-reference with the other tenant policies to determine if missing apps are covered elsewhere.
 ```
6. Review the conditions configured and assess signal trustworthiness.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add this row:
Row 8 – Conditions: Which conditions are configured — sign-in risk, user risk, device platforms, locations (named/trusted), client app types, device filters? For each configured condition, assess whether the underlying signal is trustworthy in this tenant. For example: Are named locations up to date? Is Identity Protection configured with sufficient data for risk signals? Are device compliance signals flowing from Intune? If no conditions are set beyond user/app targeting, note that the policy applies unconditionally and explain the implications.
 ```
7. Assess grant controls and session controls for appropriateness.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add these rows:
Row 9 – Grant Controls: What does this policy enforce — Block access, Require MFA, Require authentication strength, Require compliant device, Require Hybrid Azure AD joined device, Require approved client app, Require app protection policy, Require Terms of Use, or a combination? Assess whether the control strength is proportionate to the risk the policy addresses. If MFA is required, note whether a specific authentication strength is set or if any MFA method satisfies it. If multiple controls are required, note whether the operator is AND or OR and whether that is appropriate.
Row 10 – Session Controls: Are any session controls configured — Conditional Access App Control, Sign-in frequency, Persistent browser session, Continuous Access Evaluation, or Disable resilience defaults? For each, explain why it may have been configured and whether the setting is appropriate. If no session controls are set, note whether any would be beneficial for this policy's use case.
 ```
8. Assess user impact, known failure scenarios, and operational burden.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add these rows:
Row 11 – User Impact: Based on the policy's scope, conditions, and controls, describe the top 3 most likely user friction points or help-desk ticket drivers. For example: users prompted for MFA on every sign-in from untrusted locations, legacy app breakage from blocking legacy authentication, or BYOD users unable to access resources requiring compliant devices. Provide practical mitigation suggestions for each.
Row 12 – Known Failures: Identify any "it breaks when…" scenarios — situations where this policy is known or likely to cause issues. Examples: VPN split-tunnel scenarios, guest users accessing shared resources, service accounts using basic auth, or kiosk/shared devices. Note any standing exceptions that are routinely requested and whether those exceptions introduce risk.
 ```
9. Analyze overlap, conflicts, and duplication with other tenant policies, then assess lifecycle and retirement readiness.
 ```
Continuing the table for Conditional Access policy <PolicyNameOrId>, add these rows:
Row 13 – Overlap / Conflict: Using the full list of Conditional Access policies retrieved earlier, identify any policies that overlap with this one in terms of targeted users, apps, and conditions. For each overlap, state whether it is: (a) complementary — they work together as intended, (b) redundant — one could be merged into the other, or (c) conflicting — they impose contradictory controls (e.g., one grants access while another blocks it for the same population). Provide specific policy names and explain the interaction.
Row 14 – Lifecycle: Is this policy still needed today, or has the threat landscape, tenant configuration, or organizational structure changed since it was created? Recommend whether to keep, merge with another policy, or retire it. If it were to be disabled or removed, what is the rollback plan and what monitoring should be in place to detect impact?
 ```
10. Produce the final consolidated analysis table with an executive summary.
 ```
Compile all 14 rows from the analysis above into a single consolidated table with columns: Area, Question, Answer / Notes, and Evidence / Link. Then provide a brief executive summary (5-7 sentences) highlighting: the overall health of this policy, the top 3 risks or gaps identified, any recommended changes, and priority actions for the policy owner. Format the executive summary as a numbered list of recommendations sorted by priority.
 ```
