uKY<3}yz)1+8wpHL

sbp_3c7aa7e67cdb5de37a60081dadc2cd4a95bf209c

### Command for entering Deal

```SQL
INSERT INTO public.deals (
  restaurant_id,
  deal_title,
  deal_image_url,
  original_price,
  current_price
)
SELECT
  id,
  'Your Deal Title Here',
  'https://your‑image.url/here.jpg',
  49.99,
  29.99
FROM public.restaurants
WHERE name = 'Exact Restaurant Name';

```

# Prompt: Security Audit for Fast AI-Built MVPs

I need you to act as a security expert and do a full audit of the codebase. Your goal is to flag high-impact vulnerabilities and help fix them with minimal changes.

Follow this 3-phase approach:

---

**Phase 1: Codebase Scan**

Go through the entire repo. Focus especially on:

• Auth flows

• API endpoints

• DB queries

• Env variables and secrets

• User input handling

Flag anything risky with:

• File name and line numbers

• Clear explanations of what’s wrong

• Priority level (Critical, High, Medium, Low)

---

**Phase 2: Risk Analysis + Fix Plan**

For every issue:

• Explain what the vulnerability is

• Describe how it can be exploited

• Recommend the smallest fix needed

• Explain how the fix improves security

Avoid overengineering. Focus on practical fixes that make the code safer without breaking anything.

---

**Phase 3: Secure Fixes**

• Make minimal changes

• Show a before/after diff

• Verify the fix works and doesn’t introduce anything new

• Flag anything that needs manual testing

---

**Focus Areas to Prioritize:**

• Leaked API keys or credentials

• Missing rate limits

• Broken or bypassable auth

• Insecure direct object references (IDOR)

• Missing server-side validation

• Poor error handling that leaks info

• Sensitive data being exposed unnecessarily

---

Return the final report as a markdown list I can share with my team.

Be precise. Be realistic. Prioritize impact.