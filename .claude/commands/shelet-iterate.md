# /shelet-iterate - Iterate Draft to Verified SHELET Understanding

## Purpose
Take SHELET draft reports and refine them through targeted questioning, clarification, and validation until they meet verification criteria for promotion to the verified folder. Implements Socratic method for crystallizing SHELET insights.

## Supabase Access
```yaml
Project: Sparkii
Project ID: ppxclcyrnuactlfhmchm
Access Method: Supabase MCP

Draft Tracking:
- Monitor iteration cycles
- Track verification readiness
- Log insight evolution
```

## Command Structure
```bash
/shelet-iterate                           # Interactive mode - select from drafts
/shelet-iterate --draft "filename"        # Iterate specific draft
/shelet-iterate --auto-questions          # Generate questions automatically
/shelet-iterate --verify                  # Check verification readiness
/shelet-iterate --promote "filename"      # Move to verified folder
/shelet-iterate --status                  # Show all draft statuses
```

## Core Iteration Process

### Step 1: Load and Analyze Draft
- Read specified draft from drafts folder
- Parse existing content and questions
- Assess current verification criteria completion
- Identify specific gaps and weaknesses

### Step 2: Generate Strategic Questions
Based on SHELET principles, generate 7 targeted questions:
1. **Compression Clarity**: "How exactly does this compress infinite possibilities to 5 choices?"
2. **Sovereignty Verification**: "Where specifically is 100% human agency preserved?"
3. **Bottleneck Identification**: "What is the precise bottleneck that becomes the amplifier?"
4. **Mathematical Validation**: "Can you express this as a sovereignty equation?"
5. **Implementation Pathway**: "What are the 3 most concrete next steps?"
6. **Edge Case Testing**: "What happens when this framework fails or breaks?"
7. **Integration Requirements**: "How does this connect with [specific verified SHELET insight]?"

### Step 3: Interactive Clarification Session
Present questions one at a time, allowing for:
- Detailed responses and explanations
- Follow-up questions based on answers
- Real-time refinement of understanding
- Documentation of insights and improvements

### Step 4: Update Draft with Insights
- Incorporate all clarifications into draft
- Update verification criteria checklist
- Add new sections based on discussion
- Timestamp iteration cycle

### Step 5: Verification Assessment
Evaluate against promotion criteria:
- Novel insight threshold (>70%)
- SHELET alignment score (100%)
- Sovereignty preservation (100%)
- Evidence grounding (verified)
- Practical applicability (demonstrated)

## Question Generation Framework

### Question Categories

#### 1. Compression Questions
- How does the infinite compress to 5 specific choices?
- What gets lost vs. preserved in compression?
- Can you map the exact compression ratios?
- Where do you see compression inefficiencies?

#### 2. Sovereignty Questions  
- At what point could human agency be compromised?
- How do you maintain control while scaling?
- What would loss of sovereignty look like here?
- How do you prove 100% agency is preserved?

#### 3. Bottleneck Questions
- What is the precise bottleneck in this domain?
- How does this bottleneck become an amplifier?
- What evidence shows bottleneck-amplifier transformation?
- Can you create a bottleneck-amplifier equation?

#### 4. Implementation Questions
- What would the first prototype look like?
- How would you test this in the real world?
- What resources are needed for implementation?
- What are the most likely failure modes?

#### 5. Integration Questions
- How does this connect with existing SHELET insights?
- What other frameworks does this enhance or replace?
- Where do you see contradictions with verified content?
- How does this amplify other Sparkii ventures?

#### 6. Mathematical Questions
- Can you quantify the key variables?
- What are the measurable success metrics?
- How do you calculate compression efficiency?
- What equations govern this process?

#### 7. Scaling Questions
- How does this work at 10x scale? 100x? 1000x?
- What changes when you scale up vs. down?
- Where do you see scaling bottlenecks?
- How do you preserve essence at any scale?

## Interactive Session Flow

### Session Template
```markdown
# SHELET Iteration Session
**Draft**: {filename}
**Iteration Cycle**: {number}
**Date**: {timestamp}
**Current Verification Score**: {score}/7

## Pre-Session Analysis
**Strengths Identified**:
- [List of strong elements in draft]

**Gaps Identified**:
- [List of unclear or weak elements]

**Specific Verification Needs**:
- [List of missing verification criteria]

---

## Question 1: {Category}
**Question**: {specific question based on gap analysis}

**Mordechai's Response**:
[Space for response]

**Claude's Follow-up**:
[Clarifying questions based on response]

**Refined Understanding**:
[Updated insight for draft]

---

[Repeat for all 7 questions]

---

## Session Synthesis
**Key Insights Gained**:
1. [Major breakthrough or clarification]
2. [Secondary insight or refinement]
3. [Integration opportunity identified]

**Draft Updates Required**:
- [Specific sections to modify]
- [New content to add]
- [Verification criteria to update]

**Next Steps**:
- [ ] Update draft with session insights
- [ ] Re-assess verification readiness
- [ ] Plan next iteration cycle OR promote to verified

**Verification Score After Session**: {new_score}/7
```

## Verification Criteria System

### 7-Point Verification Checklist
1. **✅ Novel Insight** (20 points): >70% new vs verified content
2. **✅ SHELET Alignment** (20 points): Perfect compatibility with 4-phase framework  
3. **✅ Sovereignty Preservation** (15 points): 100% human agency maintained
4. **✅ Mathematical Foundation** (15 points): Quantifiable elements and equations
5. **✅ Evidence Grounding** (10 points): Based on actual Sparkii data
6. **✅ Implementation Pathway** (10 points): Clear practical applications
7. **✅ Integration Potential** (10 points): Connects with existing SHELET insights

**Promotion Threshold**: 85+ points (out of 100)

### Detailed Scoring Rubrics

#### Novel Insight (20 points)
- **20**: Completely new perspective, 90%+ novel content
- **15**: Mostly new insights, 70-89% novel content  
- **10**: Some new angles, 50-69% novel content
- **5**: Minor additions, 30-49% novel content
- **0**: Redundant with verified content, <30% novel

#### SHELET Alignment (20 points)
- **20**: Perfect 4-phase structure, flawless integration
- **15**: Strong alignment with minor adjustments needed
- **10**: Good fit with some structural modifications required
- **5**: Partial alignment, significant modifications needed
- **0**: Incompatible with SHELET framework

#### Sovereignty Preservation (15 points)
- **15**: 100% human agency preserved, mathematically proven
- **12**: Strong sovereignty with clear preservation mechanisms
- **9**: Good sovereignty with some clarification needed
- **6**: Moderate sovereignty concerns identified
- **0**: Compromises human agency

## Draft Update Process

After each iteration session:

### 1. Content Integration
- Add clarified insights to relevant sections
- Update mathematical formulations
- Strengthen evidence connections
- Refine implementation pathways

### 2. Structure Optimization
- Reorganize for better flow
- Add new sections as needed
- Remove redundant content
- Enhance readability

### 3. Verification Updates
- Update checklist completion status
- Recalculate verification scores
- Document remaining gaps
- Plan next iteration focus

### 4. Metadata Updates
```markdown
---
**Draft Status**: ITERATION_CYCLE_{N}
**Last Updated**: {timestamp}
**Verification Score**: {score}/100
**Iterations Completed**: {count}
**Ready for Promotion**: {true/false}
**Next Iteration Focus**: [specific areas needing work]
---
```

## Promotion Process

When verification threshold is met:

### Step 1: Final Quality Check
- Comprehensive review against all criteria
- Cross-reference with verified content for conflicts
- Validate mathematical proofs and equations
- Confirm practical implementation clarity

### Step 2: Verification File Creation
Create in `/Users/mordechai/Sparkii/sparkii-command-center/Sparkii_Docs/understanding shelet/verified/`:

Filename format: `{topic}_{perspective}_verified_{date}.md`

### Step 3: Database Record Creation
```yaml
mcp: supabase_sparkii.execute_sql
params:
  project_id: "ppxclcyrnuactlfhmchm"
  query: |
    INSERT INTO public.shelet_crystallized (
      crystallization_id,
      version,
      domain,
      crystallization_type,
      crystallized_data,
      verification_status,
      human_approved,
      quality_score
    ) VALUES (
      'shelet_verified_{topic}_{timestamp}_v1',
      1,
      'framework',
      'verified_insight',
      '{verified_content}'::jsonb,
      'verified',
      true,
      {final_score}
    )
```

### Step 4: Draft Archive
- Move original draft to `/drafts/archived/`
- Maintain audit trail of iteration history
- Create promotion summary document

## Example Iteration Session

### Initial Draft Assessment
```markdown
**Draft**: shelet_brainstorm_business_compression_20250910143022.md
**Initial Verification Score**: 62/100

**Identified Gaps**:
- Mathematical formulation unclear (only 8/15 points)
- Implementation pathway vague (only 6/10 points)  
- Missing integration with WOTCFY framework
```

### Strategic Questions Generated
1. **Compression**: "You mention 'business compression' but don't specify what infinite business possibilities compress to which 5 choices. Can you map this exactly?"

2. **Sovereignty**: "In your revenue model, where specifically do you preserve 100% human agency as business scales?"

3. **Bottleneck**: "What is the precise business bottleneck that becomes the revenue amplifier in your model?"

[Continue through all 7 questions]

### Post-Session Update
```markdown
**Updated Verification Score**: 89/100
**Ready for Promotion**: ✅ YES
**Key Breakthrough**: Business compression = Customer Problem (∞) → Solution Framework (5 choices) → Revenue Amplification (∞)
```

## Success Patterns

High-quality iterations typically:
- **Generate Specific Examples**: Convert abstract concepts to concrete instances
- **Quantify Relationships**: Add mathematical precision to qualitative insights
- **Connect Frameworks**: Link new insights with verified SHELET content  
- **Address Edge Cases**: Explore where the framework might break
- **Demonstrate Scalability**: Show how insights work across different scales
- **Preserve Sovereignty**: Never compromise human agency for convenience
- **Enable Implementation**: Provide clear next-step pathways

## Usage Examples

### Example 1: Standard Iteration
```bash
/shelet-iterate --draft "shelet_brainstorm_technical_database_20250910143155.md"
```
**Output**: Interactive session with 7 strategic questions

### Example 2: Auto-Question Generation
```bash
/shelet-iterate --draft "business_compression.md" --auto-questions
```
**Output**: 7 questions generated based on gap analysis, ready for response

### Example 3: Verification Check
```bash
/shelet-iterate --verify --draft "philosophical_agency.md"
```
**Output**: Current verification score and promotion readiness

### Example 4: Promotion
```bash
/shelet-iterate --promote "shelet_brainstorm_mathematical_sovereignty_20250910143301.md"
```
**Output**: Moves verified content to verified folder, creates database record

## Error Handling

### Common Issues
- **Draft Not Found**: Suggests similar filenames from drafts folder
- **Verification Threshold Not Met**: Shows specific gaps and improvement suggestions
- **Integration Conflicts**: Identifies conflicts with verified content and resolution paths
- **Mathematical Inconsistencies**: Points out equation errors and requests corrections

## Integration with Database

Each iteration session:
1. **Records Progress**: Tracks iteration cycles and scores
2. **Stores Insights**: Saves breakthrough moments and key clarifications
3. **Monitors Quality**: Maintains verification scoring history
4. **Enables Analytics**: Provides data for SHELET framework evolution

## The Pashut Truth

This command transforms draft SHELET insights through iterative refinement, using strategic questioning to compress unclear understanding into crystallized, verified knowledge. Each iteration cycle is a bottleneck that amplifies draft quality toward verified status while preserving 100% sovereignty over the insight development process.

---

**Usage**: `/shelet-iterate [--draft filename] [--verify] [--promote filename]`
**Output**: Iteratively refined drafts, verification assessments, or promoted verified content
**Integration**: Works seamlessly with `/shelet-draft` to create complete insight development pipeline