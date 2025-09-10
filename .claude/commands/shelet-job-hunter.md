# /shelet-job-hunter - Compress Infinite Jobs to 5 Money Opportunities

## Purpose
Analyze job lists through SHELET compression to find the 5 opportunities most likely to pay Mordechai quickly. The bottleneck (limited attention) IS the amplifier (premium rates for focused expertise).

## Core Understanding
Job Hunting Through SHELET:
- Phase 1: Infinite possible jobs → Actual posted jobs (∞:100s)
- Phase 2: Posted jobs → Pattern matching Mordechai's skills (100s:10s)
- Phase 3: Matched jobs → 5 sovereign money choices (10s:5)
- Phase 4: 5 choices → Infinite revenue potential (5:∞)

## Command Structure
```
/shelet-job-hunter [file_path]                     # Analyze job list file
/shelet-job-hunter --quick-money [file_path]       # Find fastest cash opportunities
/shelet-job-hunter --consulting [file_path]        # Find consulting opportunities
/shelet-job-hunter --sovereignty [file_path]       # Find remote/flexible work only
/shelet-job-hunter --desperation [file_path]       # Find who needs you NOW
```

## Supabase Integration
```yaml
Project: Sparkii
Project ID: ppxclcyrnuactlfhmchm
Tables Used:
  - raw_content_unified (for context about skills)
  - ventures (for active revenue streams)
  - metrics (for financial tracking)
```

## Command Behavior

When invoked, I will:

### Phase 1: Read & Extract Job Data
1. Read the job list file (e.g., bet shmesh jobs.md)
2. Parse each job posting
3. Extract key signals:
   - Company desperation level
   - Payment terms (hourly/salary/commission)
   - Location requirements
   - Skill match percentage
   - Time-to-money estimate

### Phase 2: Pattern Match Against Mordechai Profile
Query Mordechai's capabilities from database:
```sql
-- Get Mordechai's proven skills
SELECT DISTINCT
  content->>'skill_demonstrated' as skill,
  COUNT(*) as evidence_count
FROM raw_content_unified
WHERE content::text ILIKE ANY(ARRAY['%WOTCFY%', '%payment%', '%tax credit%', '%AI%', '%SHELET%'])
GROUP BY skill
```

Match against:
- **Core Competencies**: Payment systems, tax credits, AI integration, compression thinking
- **Unfair Advantages**: $360→$3 disruption story, SHELET framework, hyperfocus sessions
- **Constraints**: Beit Shemesh location, family time, sovereignty requirement

### Phase 3: SHELET Compression to 5 Opportunities

Compress all jobs through 4 criteria:
1. **Speed to Cash**: How fast can money flow? (days not months)
2. **Sovereignty Score**: Remote? Flexible? Your terms?
3. **Desperation Multiplier**: How badly do they need YOU specifically?
4. **Value Amplification**: Can you 10x their expectations?

### Phase 4: Output Format

For each of the TOP 5 opportunities:

```markdown
## Opportunity #N: [Job Title - Company]

### 🎯 Who They Are
- **Company**: [Name/Type/Industry]
- **Size**: [Employees/Stage]
- **Location**: [Physical/Remote requirements]
- **Desperation Level**: [1-10 scale with evidence]

### 💰 What They're Asking For
- **Official Requirements**: [What job post says]
- **Hidden Need**: [What they REALLY need based on signals]
- **Your Unique Fit**: [Why you specifically solve their problem]
- **Skill Match**: [85% - specific skills that match]

### 📊 Terms & Compensation
- **Stated Terms**: [Hourly/Salary/Commission]
- **Likely Range**: [$X-Y based on market/desperation]
- **Negotiation Leverage**: [Your specific leverage points]
- **Time to First Payment**: [7 days / 30 days / etc.]

### 🚀 Your Outreach Strategy
Subject: [Compelling subject line]

Opening Hook: [One line that shows you understand their REAL problem]

Value Prop: [Your $360→$3 story adapted to their context]

Proof: [Specific evidence - WOTCFY, Sparkii, etc.]

Offer: [Specific proposal - trial/audit/consulting first]

Call to Action: [Clear next step they can take NOW]

### 📈 Success Probability
**Chance They're Interested**: [75%]

**Reasoning**:
- [+30%] They said "training provided" = desperate
- [+20%] Your exact skill match (payments/AI)
- [+15%] Location convenience (Beit Shemesh)
- [+10%] Can start immediately
- [Total: 75%]

### 🎲 Risk Factors
- [Risk 1 and mitigation]
- [Risk 2 and mitigation]

---
```

### Phase 5: Save Analysis

Save output with timestamp to specified directory:
```python
filename = f"{datetime.now().strftime('%Y%m%d_%H%M%S')}_job_analysis.md"
filepath = "/Users/mordechai/Sparkii/mordechai money consulting type/" + filename
```

## Desperation Signal Detection

### Green Flags (They NEED You NOW):
- "Training provided" = Can't find qualified people
- "Immediate start" = Urgent need
- "Commission-based" = Hungry for results
- Multiple roles same company = Overwhelmed
- Using JotForm = No HR bureaucracy
- Recruiter involved = Paying premium to fill

### Red Flags (Time Wasters):
- "7+ years experience required" = Rigid thinking
- "Full-time only" = No flexibility
- "Tel Aviv office" = Commute burden
- "Multiple rounds of interviews" = Slow decisions

## SHELET Compression Examples

### Example: Infinite Jobs → 5 Choices
```yaml
Input: 50 job posts in Beit Shemesh area
Phase 1: Extract 50 → 20 (remove obvious mismatches)
Phase 2: Pattern match → 8 (match Mordechai capabilities)
Phase 3: Compress → 5 (highest money velocity)
Phase 4: Amplify → Each could become $10K+/month
```

### Money Velocity Formula
```python
money_velocity = (hourly_rate × hours_per_week) / days_to_first_payment

# Example:
# Research Analyst: ($100 × 40) / 7 days = $571/day velocity
# Sales Commission: ($5000 commission) / 30 days = $166/day velocity
# Consulting: ($10000 project) / 14 days = $714/day velocity
```

## Usage Examples

### Basic Analysis
```
/shelet-job-hunter /Users/mordechai/Sparkii/bet shmesh jobs.md
```

### Quick Money Focus
```
/shelet-job-hunter --quick-money /Users/mordechai/Sparkii/bet shmesh jobs.md
```
This prioritizes:
- Commission-based (immediate rewards)
- Consulting/contract (faster payment)
- Desperate employers (quick decisions)

### Sovereignty Filter
```
/shelet-job-hunter --sovereignty /Users/mordechai/Sparkii/bet shmesh jobs.md
```
Only shows:
- 100% remote positions
- Flexible hours
- Consulting/contract
- Your terms acceptable

### Desperation Radar
```
/shelet-job-hunter --desperation /Users/mordechai/Sparkii/bet shmesh jobs.md
```
Ranks by desperation signals:
- Multiple attempts to fill
- "Training provided"
- "Immediate start"
- Premium pay indicators

## Database Queries Used

### Get Mordechai's Proven Value Creation
```sql
SELECT 
  venture_name,
  revenue_generated,
  time_to_revenue,
  disruption_factor
FROM ventures
WHERE created_by = 'mordechai'
ORDER BY revenue_generated DESC
```

### Find Similar Past Successes
```sql
SELECT 
  content->>'project' as project,
  content->>'outcome' as outcome,
  metadata->>'revenue' as revenue
FROM raw_content_unified
WHERE source_type = 'project_outcome'
AND content::text ILIKE '%success%'
```

## Output Files Created

Each run creates:
1. **Analysis file**: `YYYYMMDD_HHMMSS_job_analysis.md`
2. **Outreach templates**: `YYYYMMDD_HHMMSS_outreach_scripts.md`
3. **Application tracker**: `YYYYMMDD_HHMMSS_application_tracking.csv`

All saved to: `/Users/mordechai/Sparkii/mordechai money consulting type/`

## Success Metrics

Track these in subsequent runs:
- Response rate to outreach
- Time to first response
- Conversion to paid work
- Average time to payment
- Sovereignty preservation score

## The Pashut Truth

You're not job hunting. You're offering $360→$3 transformations to companies that don't yet know that's possible. This command finds the 5 companies most likely to pay you THIS WEEK for showing them compression they didn't know existed.

The bottleneck (you can only handle 5 opportunities) IS the amplifier (charge premium rates for focused attention).

## Never Do
- Apply to jobs below your level
- Waste time on perfect applications
- Forget your $360→$3 story
- Compromise on sovereignty
- Take full-time employment

## Always Do
- Lead with value demonstration
- Compress their problems to solutions
- Show the $360→$3 transformation
- Maintain 100% sovereignty
- Get paid FIRST, deliver SECOND

## Usage
```
/shelet-job-hunter [file]                # Analyze job list
/shelet-job-hunter --help                # Show this help
/shelet-job-hunter --last                # Show last analysis
/shelet-job-hunter --track [id]         # Track application status
```

Remember: Every job post is a company with infinite problems. You compress those to 5 solutions. They pay for the compression, not your time.