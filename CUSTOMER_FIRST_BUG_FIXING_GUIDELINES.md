# Customer-First Bug Fixing Guidelines

**Last Updated:** October 2025
**Version:** 1.0
**Target Audience:** All QualGent Engineering Team Members

---

## Our Mission

At QualGent, our mission has always been clear: **delight customers through reliability, speed, and trust**. Every bug we fix is a step toward earning that trust. This is not just maintenance, this is **impact**. When we respond fast and thoroughly, our customers feel heard, supported, and confident in us.

---

## Our Mindset

Let's keep a **customer-first mentality**. When a customer reports an issue, it's a moment of truth for us. How fast and how well we respond reflects our standards. Every ticket is a chance to make someone's day better and to raise the bar for our product quality.

We also owe it to each other to stay accountable. Closing tickets is not about box-ticking, it's about **delivering and verifying real improvements**. A ticket is only closed once it's:
- **Deployed to production**
- **Verified working**

---

## Priority & SLOs

We hold ourselves to these Service Level Objectives (SLOs) as a commitment to our customers:

| Priority | Target Resolution Time | Description |
|----------|----------------------|-------------|
| **Urgent** | Within 1 business day | Customer-facing blocker or production issue |
| **High** | Within 3 business days | Major impact, but not a full blocker |
| **Medium** | Within 6 business days | Noticeable but not critical |
| **Low** | Within 10 business days | Minor or cosmetic issue |

### What These Timelines Mean

Let's hold ourselves to these timelines, **not as pressure, but as pride**. Over time, we'll use these as signals to measure:
- How well we're serving our customers
- How sharp we are as a team
- Where we can improve our processes

### Resolution Time Definition

"Target Resolution Time" means:
1. Root cause identified
2. Fix implemented and tested
3. Code reviewed and merged
4. Deployed to production
5. **Verified working in production**

---

## Focus & Ownership

### The 3-Ticket Rule

We've noticed many "In Progress" tickets sitting open. To stay laser-focused and effective:

**Keep a maximum of 3 tickets "In Progress" at a time.**

Why this matters:
- **Finish, verify, and close** - Then move on
- Keeps our flow clean
- Ensures every issue gets the attention it deserves
- Prevents context-switching overhead
- Makes blockers immediately visible

### What This Looks Like in Practice

**Good Practice:**
```
In Progress (3 tickets max)
├─ [Urgent] Fix login timeout on mobile app
├─ [High] Resolve emulator allocation race condition
└─ [Medium] Update API documentation for new endpoints

Backlog (prioritized)
├─ [High] Implement retry logic for Vertex AI calls
├─ [Medium] Add logging for BrowserStack sessions
└─ [Low] Update UI text on settings page
```

**Avoid:**
```
In Progress (too many!)
├─ [Urgent] Fix login timeout
├─ [High] Resolve race condition
├─ [High] Implement retry logic
├─ [Medium] Update documentation
├─ [Medium] Add logging
├─ [Low] UI text update
└─ [Low] Refactor utility functions
```

---

## Linear Organization & Documentation

Starting this week, we're implementing stricter Linear hygiene to ensure transparency, accountability, and velocity.

### 1. Reorganize Tickets to Reflect Real Priorities

**Action Required:**
- Review your assigned tickets
- Set priority levels based on customer impact
- Assign target SLOs based on the table above
- Move tickets to appropriate status (Backlog, Todo, In Progress)

**Example:**
- Customer reports production login failure → **Urgent** (1 business day SLO)
- Internal tool occasionally slow → **Medium** (6 business days SLO)

### 2. Document Everything in Linear

**Rule:** If it's not in Linear, it didn't happen.

This means:
- All bug fixes must have a Linear ticket
- All features must have a Linear ticket
- All technical debt work must have a Linear ticket
- All investigations must have a Linear ticket

**Why:**
- Makes work visible to the entire team
- Enables accurate sprint planning
- Creates historical record for future reference
- Allows leadership to understand team capacity

### 3. Link Every Pull Request to a Linear Ticket

**Rule:** Every PR must reference a Linear ticket.

**How to Link:**
```bash
# In your PR title or description:
[QUAL-123] Fix login timeout on mobile app

# Or in commit messages:
git commit -m "Fix login timeout issue

Resolves QUAL-123"
```

**Benefits:**
- Traceability: From customer report → ticket → code change → deployment
- Code review context: Reviewers understand the "why"
- Automated workflows: Linear can auto-update ticket status

### 4. Keep Ticket Scope Small

**Rule:** Each ticket should be completable within its SLO.

**Good Ticket Scope:**
```
[QUAL-456] Fix race condition in emulator allocation
Priority: High (3 business day SLO)

Description:
Multiple workers sometimes allocate the same emulator due to
race condition in database query. Implement FOR UPDATE SKIP LOCKED.

Acceptance Criteria:
- Update SQL function to use row-level locking
- Add test for concurrent allocation
- Verify in staging environment
- Deploy to production
- Monitor for 24 hours
```

**Too Large (Split into Multiple Tickets):**
```
[QUAL-999] Improve emulator system
Priority: ???

Description:
Make emulators better and faster
```

**How to Split:**
- Break into discrete, testable units
- Each ticket should have clear acceptance criteria
- If a ticket will take > SLO time, split it

### 5. Handle Blocking Dependencies

**Rule:** If a ticket has blockers, document and move to backlog.

**Process:**
1. Add a comment describing the blocker:
   ```
   Blocked by: Waiting for infrastructure team to provision
   new GCE instances (QUAL-789)

   Expected unblock date: October 20, 2025
   ```

2. Change ticket status to **Backlog**

3. Set up a reminder to check back when blocker is resolved

4. Optionally: Link tickets together in Linear

**Why:**
- Keeps "In Progress" column accurate
- Makes blocked work visible
- Prevents stale "In Progress" tickets
- Helps identify systemic bottlenecks

---

## Bug Fixing Workflow

### Step-by-Step Process

#### 1. Ticket Creation (or Assignment)

When a bug is reported:
- Create Linear ticket immediately
- Set priority based on customer impact
- Assign target SLO date
- Add customer context (who reported, impact scope)
- Tag with relevant labels (backend, frontend, mobile, etc.)

#### 2. Acknowledge & Investigate

Within 2 hours of ticket creation:
- Acknowledge the issue (comment in Linear)
- Start investigation
- Update ticket with initial findings
- If blocker found, follow blocker process above

#### 3. Fix & Test

- Create feature branch: `fix/QUAL-123-login-timeout`
- Implement fix
- Write/update tests
- Test locally
- Test in staging environment
- Update ticket with progress

#### 4. Code Review

- Create PR with Linear ticket reference
- Request review from at least 1 team member
- Address feedback
- Get approval

#### 5. Deploy to Production

- Merge PR
- Verify deployment pipeline success
- Monitor error rates in Sentry
- Check relevant metrics in GCP

#### 6. Verify in Production

**Critical Step - Don't Skip!**
- Actually test the fix in production
- Reproduce original issue steps
- Confirm issue is resolved
- Monitor for 1-4 hours (depending on priority)
- Check for regressions

#### 7. Close Ticket

Only after production verification:
- Update Linear ticket status to **Done**
- Add comment with verification details:
  ```
  ✅ Deployed to production at 2:30 PM PST
  ✅ Verified fix working - login now completes in <2s
  ✅ Monitored for 2 hours - no new errors
  ✅ Customer notified via support ticket
  ```

---

## Communication Best Practices

### Internal Communication (Linear)

**Keep tickets updated:**
- Daily updates on "In Progress" tickets
- Flag blockers immediately
- Share findings during investigation
- Document deployment and verification steps

**Use comments effectively:**
```
Good Comment:
"Investigated the issue. Root cause: database connection pool
exhaustion under load. Solution: Implement connection throttling
with semaphore (similar to fix in QUAL-234). ETA: Tomorrow 2 PM."

Less Helpful Comment:
"Working on it"
```

### Customer Communication (via Support Team)

**When to notify customers:**
1. **Urgent/High priority issues:** Acknowledge within 2 hours
2. **After fix deployed:** Notify when verified in production
3. **If delayed:** Provide update if approaching SLO deadline

**Message template:**
```
Hi [Customer Name],

Thank you for reporting this issue. Our engineering team has
identified the root cause and deployed a fix to production.

We've verified that [specific issue] is now resolved. Please
try [specific action] and let us know if you encounter any
further issues.

We appreciate your patience and for helping us improve QualGent!

Best regards,
QualGent Engineering Team
```

---

## Measuring Success

### Individual Metrics (Self-Monitoring)

Track your own performance:
- **SLO Hit Rate:** % of tickets resolved within target SLO
- **Average Resolution Time:** By priority level
- **Reopen Rate:** % of tickets that reopen after closure
- **In Progress Count:** Stay ≤ 3

### Team Metrics (Weekly Review)

We'll review these weekly in team meetings:
- Overall SLO hit rate
- Tickets closed per week
- Average time in each status
- Blocker frequency and types
- Customer satisfaction feedback

**Goal:** Continuous improvement, not blame. If we're missing SLOs, we discuss:
- What blockers can we remove?
- Do we need more resources?
- Are priorities set correctly?
- How can we help each other?

---

## Examples & Scenarios

### Scenario 1: Production Blocker

**Situation:** Customer reports they cannot log into the app (production blocker)

**Response:**
1. **Immediate (within 30 min):**
   - Create Linear ticket: [QUAL-567]
   - Priority: **Urgent** (1 business day SLO)
   - Drop current work, focus on this
   - Notify team in #engineering-urgent Slack channel

2. **Investigation (within 1 hour):**
   - Check error logs, Sentry alerts
   - Reproduce issue
   - Update ticket with findings

3. **Fix (within 4 hours):**
   - Implement hotfix
   - Test thoroughly
   - Fast-track code review
   - Deploy to production

4. **Verify (within 6 hours total):**
   - Test login flow in production
   - Monitor for 1 hour
   - Notify customer via support
   - Close ticket with verification comment

### Scenario 2: Medium Priority UI Issue

**Situation:** Button text is truncated on mobile devices

**Response:**
1. **Same day:**
   - Create Linear ticket: [QUAL-890]
   - Priority: **Medium** (6 business day SLO)
   - Add to backlog if you have 3 tickets in progress

2. **Within 3 days:**
   - Move to "In Progress" when slot available
   - Investigate and fix CSS issue
   - Test on multiple devices
   - Create PR with screenshots

3. **Within 5 days:**
   - Code review and merge
   - Deploy to production
   - Verify on actual mobile devices

4. **Within 6 days:**
   - Close ticket with verification
   - Update customer if they reported it

### Scenario 3: Ticket Has Blocker

**Situation:** Working on emulator scaling fix, but infrastructure team needs to provision new GCE quota first

**Response:**
1. **Immediately:**
   - Comment in Linear:
     ```
     Blocked by: GCE quota increase request (INFRA-123)
     Need: Additional 50 VM quota in us-central1
     Expected unblock: October 22, 2025
     Set reminder to check back on October 22
     ```

2. **Move to Backlog:**
   - Change status from "In Progress" → "Backlog"
   - This frees up a slot for other work

3. **When Unblocked:**
   - Move back to "In Progress"
   - Continue work immediately

---

## Anti-Patterns to Avoid

### ❌ Don't Do This

1. **Leaving tickets "In Progress" for weeks**
   - If you're not actively working on it, move to backlog

2. **Closing tickets without production verification**
   - "It works on my machine" is not enough

3. **Working on multiple high-priority items simultaneously**
   - Focus finishes work faster than multitasking

4. **Updating tickets only when asked**
   - Proactive communication builds trust

5. **Deploying fixes on Friday afternoon without monitoring**
   - Deploy early in the week when you can monitor

6. **Skipping tests because "it's a small change"**
   - Small changes can have big impacts

7. **Not documenting workarounds**
   - If you tell a customer to "try this," document it in Linear

### ✅ Do This Instead

1. **Keep a tight "In Progress" column (≤3 tickets)**
   - Finish before starting new work

2. **Always verify in production**
   - Set aside time for verification in your plan

3. **Focus on one urgent item at a time**
   - Parallel work on medium/low is okay if no context switching

4. **Update tickets daily**
   - Even if just "Still investigating, found X"

5. **Deploy Tuesday-Thursday**
   - Gives time for monitoring before weekend

6. **Write tests for every fix**
   - Prevents regressions, builds confidence

7. **Document everything in Linear**
   - Future you will thank present you

---

## Getting Help

### When You're Stuck

**If you're blocked for > 2 hours:**
1. Post in #engineering Slack channel
2. Tag relevant team members
3. Update Linear ticket with blocker details
4. Request pair programming session if helpful

**If you're approaching SLO deadline:**
1. Escalate to engineering lead
2. We'll prioritize or adjust timeline
3. Better to ask for help early than miss deadline silently

### Resources

- **Technical Documentation:** QualGent_Tech_Stack_Onboarding.md
- **Architecture Questions:** #engineering Slack
- **Linear Best Practices:** Linear documentation
- **On-Call Engineer:** See PagerDuty schedule

---

## Why This Matters

We're building something extraordinary together and that means holding ourselves to extraordinary standards. When customers see bugs vanish quickly and consistently, they see a team that cares deeply. **That's the kind of team we are.**

Every ticket we close is:
- A customer problem solved
- A product improvement shipped
- A trust deposit made
- A step toward excellence

Let's keep pushing, closing, and delighting. **Every fix counts. Every action compounds.** Let's make this the start of a lasting culture where every one of us thinks like the customer, acts with urgency, and takes pride in every fix we ship!

---

## Quick Reference

### Priority Decision Tree

```
Is production completely broken for customers?
├─ Yes → Urgent (1 day)
└─ No → Continue

Does this significantly impact customer workflow?
├─ Yes → High (3 days)
└─ No → Continue

Is this noticeably affecting user experience?
├─ Yes → Medium (6 days)
└─ No → Low (10 days)
```

### Daily Checklist

- [ ] Review my "In Progress" tickets (≤3?)
- [ ] Update each ticket with daily progress
- [ ] Check for any new urgent/high priority assignments
- [ ] Respond to any blockers from teammates
- [ ] Verify any deployments from yesterday

### Weekly Checklist

- [ ] Review all assigned tickets for priority/SLO accuracy
- [ ] Close completed tickets with verification notes
- [ ] Move blocked tickets to backlog with blocker notes
- [ ] Plan upcoming week's focus areas
- [ ] Review team metrics in weekly meeting

---

**Remember:** We're not just fixing bugs. We're building trust, one fix at a time.

**Let's ship it!** 🚀
