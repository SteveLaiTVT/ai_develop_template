# Discovery Questions Template

This template provides the full question framework for A Session's discovery interview phase.

---

## Purpose

Before designing any system, A Session should conduct a discovery interview to understand:
- Who will use the app
- What technical resources exist
- What the product vision is
- What constraints apply

This mimics how a real architect/product manager works.

---

## Discovery Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    DISCOVERY INTERVIEW FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Greet & Explain    "I'd like to understand your project..."  │
│         ↓                                                        │
│  2. Target Audience    Who will use this? How tech-savvy?        │
│         ↓                                                        │
│  3. Technical Context  Your experience? Existing resources?      │
│         ↓                                                        │
│  4. Product Vision     Core problem? MVP scope? Design style?    │
│         ↓                                                        │
│  5. Constraints        Budget? Compliance? Integrations?         │
│         ↓                                                        │
│  6. Summarize          Confirm understanding before proceeding   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Question Categories

### Category 1: Target Users & Audience

**Goal**: Understand who will use the app and how.

```markdown
## 📱 WHO WILL USE THIS APP?

**Q1: Who is your target audience?**

Please tell me about your users:
- Age range: [ ] Children (under 13) [ ] Teens (13-17) [ ] Young adults (18-25)
              [ ] Adults (25-45) [ ] Middle-aged (45-65) [ ] Elderly (65+) [ ] All ages
- Technical skill: [ ] Low (not tech-savvy) [ ] Moderate [ ] High (developers/tech workers)
- Primary language/locale: _______________

**Q2: How will they access your app?**
- [ ] Mobile-first (phone is the primary device)
- [ ] Desktop-first (computer is the primary device)
- [ ] Both equally important
- [ ] Specific device: _______________

**Q3: Any accessibility requirements?**
- [ ] Color-blind friendly design
- [ ] Screen reader support
- [ ] Large text / high contrast options
- [ ] Motor accessibility (large touch targets)
- [ ] Other: _______________
- [ ] None specific / standard accessibility
```

**Adaptive Questions**:
- If elderly users → Ask about font size preferences
- If children → Ask about parental controls
- If B2B → Ask about multi-tenant requirements

---

### Category 2: Technical Context

**Goal**: Understand what technical resources and preferences exist.

```markdown
## 🔧 YOUR TECHNICAL BACKGROUND

**Q4: What's your tech experience?**

Frontend:
- [ ] React (I know it well)
- [ ] Vue (I know it well)
- [ ] Angular (I know it well)
- [ ] None (no frontend experience)
- [ ] Other: _______________

Backend:
- [ ] Node.js / TypeScript
- [ ] Python (Django/FastAPI/Flask)
- [ ] Go
- [ ] Java / Kotlin
- [ ] None (no backend experience)
- [ ] Other: _______________

Are you comfortable with TypeScript? [ ] Yes [ ] No [ ] What's that?

**Q5: What resources do you have?**
- [ ] I have a designer / design team
- [ ] I have existing brand guidelines (colors, fonts, logo)
- [ ] I have an existing codebase to build on
- [ ] I'm starting from scratch
- APIs or services to integrate: _______________

**Q6: Where do you want to host this?**
- [ ] Vercel (easy deployment, serverless)
- [ ] AWS (flexible, more complex)
- [ ] Google Cloud Platform
- [ ] Self-hosted / On-premise
- [ ] Not sure yet
- [ ] Other: _______________
```

**Adaptive Questions**:
- If no experience → Suggest simpler stacks
- If existing codebase → Ask about its tech stack
- If self-hosted → Ask about infrastructure team

---

### Category 3: Product Vision

**Goal**: Understand the core problem and what success looks like.

```markdown
## 🎯 YOUR PRODUCT VISION

**Q7: What problem does this app solve?**

Complete this sentence:
"Users struggle with _________________, and this app helps by _________________."

Example: "Users struggle with tracking expenses manually, and this app helps by
automatically categorizing transactions and showing spending insights."

**Q8: What's the MINIMUM that makes this useful? (MVP)**

If your app could only do ONE thing well, what would it be?
_______________________________________________________________

What features are nice-to-have but NOT essential for launch?
_______________________________________________________________

**Q9: What's your design style preference?**

- [ ] Minimal & clean (like Notion, Linear, Apple)
- [ ] Playful & colorful (like Duolingo, Headspace, Spotify)
- [ ] Professional & data-rich (like Salesforce, Bloomberg, Jira)
- [ ] Utilitarian / function over form
- [ ] Other: _______________

Reference apps you like the look/feel of:
1. _______________
2. _______________
3. _______________

What do you like about them?
_______________________________________________________________
```

**Adaptive Questions**:
- If B2B → Ask about dashboard requirements
- If consumer → Ask about onboarding flow preferences
- If data-heavy → Ask about visualization needs

---

### Category 4: Constraints

**Goal**: Understand limitations and requirements.

```markdown
## ⚠️ CONSTRAINTS & CONSIDERATIONS

**Q10: Any budget constraints?**

- [ ] Low budget (prefer free/cheap services only)
  - Max monthly hosting cost: $___
- [ ] Medium budget (some paid services OK)
  - Max monthly budget: $___
- [ ] Flexible (best tool for the job)
- [ ] Enterprise (cost is not a primary concern)

**Q11: Any compliance or regulatory requirements?**

- [ ] GDPR (EU data privacy - user consent, data deletion)
- [ ] HIPAA (US health data protection)
- [ ] SOC 2 (security compliance)
- [ ] PCI-DSS (payment card data)
- [ ] COPPA (children's privacy)
- [ ] Industry-specific: _______________
- [ ] None that I know of

**Q12: Existing systems to integrate with?**

Authentication:
- [ ] Build our own
- [ ] Auth0
- [ ] Firebase Auth
- [ ] Clerk
- [ ] Existing SSO: _______________

Payments:
- [ ] Not needed
- [ ] Stripe
- [ ] PayPal
- [ ] Other: _______________

Other APIs/Services:
_______________________________________________________________
```

---

## Summary Template

After asking all questions, summarize the understanding:

```markdown
## 📋 DISCOVERY SUMMARY

Let me confirm my understanding of your project:

### Target Audience
- **Users**: {age_range}, {tech_level} technical skill
- **Primary access**: {device_preference}
- **Accessibility**: {accessibility_needs}

### Technical Context
- **Your experience**: {frontend_exp} frontend, {backend_exp} backend
- **Stack preference**: {preferred_stack}
- **Existing resources**: {resources}
- **Hosting**: {hosting_preference}

### Product Vision
- **Core problem**: {problem_statement}
- **MVP scope**: {mvp_definition}
- **Design style**: {design_style}
- **Reference apps**: {reference_apps}

### Constraints
- **Budget**: {budget_level}
- **Compliance**: {compliance_requirements}
- **Integrations**: {integrations}

---

**Is this accurate?** Please correct anything I misunderstood.

Once confirmed, I'll:
1. Update DESIGN_STATE.yaml with this information
2. Analyze the project structure
3. Begin designing the architecture
```

---

## Recording Answers

After discovery, update DESIGN_STATE.yaml:

```yaml
discovery:
  completed: true
  completed_at: "2026-01-15"

  target_audience:
    age_range: "25-45"
    tech_level: "moderate"
    primary_device: "mobile-first"
    accessibility_needs: ["color-blind-friendly"]
    locale: "en-US"

  user_personas:
    - name: "Busy Professional"
      description: "Uses app during commute, needs quick actions"
      priority: "primary"

  technical_context:
    user_stack_preference:
      frontend: "react"
      backend: "nestjs"
      familiar_with: ["typescript", "postgresql"]
    existing_resources:
      has_designer: false
      has_brand_guidelines: false
      existing_codebase: false
      apis_to_integrate: ["stripe"]
    infrastructure_preference: "vercel"
    team_size: "solo"

  product_vision:
    core_problem: "Users struggle with tracking expenses"
    mvp_scope: "Log expenses quickly, see monthly summary"
    design_style: "minimal"
    reference_apps: ["Mint", "YNAB", "Copilot"]

  constraints:
    budget: "low"
    compliance: ["GDPR"]
    integrations: ["stripe"]

  raw_answers:
    - question: "Who is your target audience?"
      answer: "Young professionals aged 25-40 who want to track spending"
    # ... store all Q&A for reference
```

---

## Adaptive Discovery

### For Existing Projects

If there's already code in the repository:

```markdown
I notice this project already has some code. Let me adjust my questions:

1. **What's the current state?**
   - Working prototype? Early development? Production?

2. **What needs to be added/changed?**
   - New feature? Bug fix? Refactor?

3. **Any existing documentation I should read?**
   - README? Design docs? API specs?
```

### For Simple Requests

If the user just wants a quick feature:

```markdown
I see you want to add {feature}. Let me ask a few quick questions:

1. Who will use this feature?
2. Any specific UI preferences?
3. Any constraints I should know about?
```

### For Technical Users

If the user is clearly technical:

```markdown
Since you're familiar with development, let me focus on the specifics:

1. Preferred tech stack?
2. Any architectural patterns you want to follow?
3. Testing requirements?
4. CI/CD preferences?
```

---

## Tips for A Session

1. **Don't overwhelm** - Ask 2-3 questions at a time, not all at once
2. **Adapt** - Skip irrelevant questions based on context
3. **Summarize often** - Confirm understanding before moving on
4. **Be conversational** - This is a dialogue, not a survey
5. **Record everything** - Store answers in DESIGN_STATE.yaml for B/C sessions
