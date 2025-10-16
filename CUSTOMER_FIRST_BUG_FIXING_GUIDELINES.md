# Customer-First Bug Fixing Guidelines

**Last Updated:** October 2025
**Version:** 1.0
**Target Audience:** All QualGent Engineering Team Members

---

## Our Mission

At QualGent, we **delight customers through reliability, speed, and trust**. Every bug we fix is a step toward earning that trust. This is not just maintenance—this is impact.

When a customer reports an issue, it's a moment of truth. How fast and how well we respond reflects our standards. Stay accountable: a ticket is only closed once it's **deployed to production and verified working**.

---

## Priority & SLOs

| Priority | Target Resolution Time | Description |
|----------|----------------------|-------------|
| **Urgent** | Within 1 business day | Customer-facing blocker or production issue |
| **High** | Within 3 business days | Major impact, but not a full blocker |
| **Medium** | Within 6 business days | Noticeable but not critical |
| **Low** | Within 10 business days | Minor or cosmetic issue |

**Resolution Time** means: Root cause identified → Fix implemented and tested → Code reviewed and merged → Deployed to production → **Verified working in production**.

---

## The 3-Ticket Rule

**Keep a maximum of 3 tickets "In Progress" at a time.**

Why:
- Finish, verify, and close before moving on
- Prevents context-switching
- Makes blockers immediately visible
- Keeps flow clean

---

## Linear Organization

### 1. Organize by Priority
- Review assigned tickets weekly
- Set priority based on customer impact
- Assign target SLO dates

### 2. Document Everything
**Rule:** If it's not in Linear, it didn't happen.
- All work must have a Linear ticket
- Makes progress visible
- Creates historical record

### 3. Link Every PR
**Rule:** Every PR must reference a Linear ticket.

```bash
# In PR title:
[QUAL-123] Fix login timeout on mobile app
```

### 4. Keep Tickets Small
Each ticket should be completable within its SLO. If larger, split into multiple tickets with clear acceptance criteria.

### 5. Handle Blockers
If blocked, document it and move to backlog:

```
Blocked by: Waiting for GCE quota increase (QUAL-789)
Expected unblock: October 22, 2025
```

Then change status to **Backlog** until unblocked.

---

## Bug Fixing Workflow

### 1. Ticket Creation
- Set priority and SLO date
- Add customer context
- Tag appropriately

### 2. Acknowledge & Investigate (within 2 hours)
- Acknowledge in Linear
- Start investigation
- Update with findings

### 3. Fix & Test
- Create branch: `fix/QUAL-123-description`
- Implement fix
- Write/update tests
- Test locally and in staging

### 4. Code Review
- Create PR with Linear reference
- Get approval
- Address feedback

### 5. Deploy to Production
- Merge PR
- Verify deployment success
- Monitor errors in Sentry

### 6. Verify in Production
**Critical - Don't Skip!**
- Test the fix in production
- Confirm issue resolved
- Monitor for 1-4 hours
- Check for regressions

### 7. Close Ticket
Only after verification:

```
✅ Deployed at 2:30 PM PST
✅ Verified fix working
✅ Monitored for 2 hours - no errors
✅ Customer notified
```

---

## Communication

### Internal (Linear)
- Daily updates on "In Progress" tickets
- Flag blockers immediately
- Document deployment and verification

**Good:** "Root cause: DB connection pool exhaustion. Solution: connection throttling. ETA: Tomorrow 2 PM."

**Bad:** "Working on it"

### Customer Communication
Notify customers when:
1. Urgent/High issues acknowledged (within 2 hours)
2. Fix deployed and verified
3. Approaching SLO deadline with update

---

## Getting Help

**Blocked for > 2 hours?**
1. Post in engineering Google Chat space
2. Mention relevant team members
3. Update Linear with blocker details

**Approaching SLO deadline?**
1. Escalate to engineering lead
2. Better to ask early than miss silently

### Resources
- **Technical Documentation:** QualGent_Tech_Stack_Onboarding.md
- **Architecture Questions:** engineering Google Chat space
- **Linear Best Practices:** Linear documentation

---

## Quick Reference

### Priority Decision Tree

```
Production completely broken? → Urgent (1 day)
Significantly impacts workflow? → High (3 days)
Noticeably affects UX? → Medium (6 days)
Otherwise → Low (10 days)
```

### Daily Checklist
- [ ] Review "In Progress" tickets (≤3?)
- [ ] Update each ticket with progress
- [ ] Check new urgent/high priority assignments
- [ ] Respond to teammate blockers

### Weekly Checklist
- [ ] Review all tickets for priority/SLO accuracy
- [ ] Close completed tickets with verification notes
- [ ] Move blocked tickets to backlog
- [ ] Plan upcoming week

---

## Why This Matters

We're not just fixing bugs—we're building trust, one fix at a time. When customers see bugs vanish quickly, they see a team that cares deeply. **That's who we are.**

Every fix counts. Every action compounds. Let's think like the customer, act with urgency, and take pride in every fix we ship.

**Let's ship it!** 🚀
