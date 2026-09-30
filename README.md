# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This project builds a retrieval-augmented question-answering system over the `campus_life` corpus. It indexes short campus-related documents, retrieves the most relevant chunks for a question, applies a relevance cutoff, and then generates an answer using only the retrieved documents. The system is designed to answer specific questions about campus services, courses, study spaces, library hours, financial aid, and similar topics while refusing questions that are outside the corpus.

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy
I used paragraph-aware chunking with a target size of about 450 characters and no overlap. The `campus_life` corpus contains short posts averaging about 317 characters, so most documents stay intact, while longer posts split at natural paragraph boundaries instead of arbitrary character positions. The starter produced 88 chunks from 88 documents; my custom chunker produced 91 chunks, showing that only a few longer posts needed to be split.
**Chunk size:** About 450 characters
**Overlap:** 0 characters

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160_exams.txt#0` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology — assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_math_220_exams.txt#0` — produced by: `chunker.py::split_documents`

```
MATH 220 Linear Algebra — assessment

Two midterms and a cumulative final. Curved to a b- median.

The problem sets are the course; the lectures make sense afterwards rather than during.
```

**Chunk 4** — source: `dining_the_ridgeway_cafe.txt#0` — produced by: `chunker.py::split_documents`

```
The Ridgeway Café

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.
```

**Chunk 5** — source: `housing_morrow_house.txt#0` — produced by: ``chunker.py::split_documents

```
Morrow House — what it's actually like

Just finished a year in this building. Built 1954, partially renovated 2008. Rooms are singles and doubles, hall bathrooms.

The good: cheapest housing tier by about $900 a year, and the singles are real singles.

The bad: known damp problem on the ground floor; two rooms were taken offline in 2024.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** "How many hours can I book a study room for every week?"

**Answer:** You can book a maximum of two blocks of two hours per person per week, which totals four hours per person per week.

Source: study_group_rooms.txt

Sources retrieved: course_cs_340.txt, course_engl_205_workload.txt, course_stat_150_workload.txt, money_jobs.txt, study_group_rooms.txt

```
```

**My relevance cutoff:** 0.6

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

The five in-corpus questions had best distances between `0.192` and `0.404`, while the five out-of-scope questions had best distances between `0.825` and `0.934`. The gap was therefore between `0.404` and `0.825`. I kept the cutoff at `0.6` because it sits safely between those two groups. With this cutoff, all five in-corpus questions were answered and all five out-of-scope questions were refused.

| Question | In corpus? | Best distance |
|---|---|---|
| What are the walk-in hours for the health centre? | Yes | 0.192 |
| What's one piece of advice for PHYS 130 Mechanics? | Yes | 0.248 |
| How many hours can I book a study room for every week? | Yes | 0.276 |
| What are the library hours during reading week? | Yes | 0.404 |
| Does work-study income count against financial aid? | Yes | 0.272 |
| What is the capital of Mongolia? | No | 0.825 |
| How do I change the oil in a diesel engine? | No | 0.934 |
| Who won the 1994 World Cup? | No | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844 |
| How do I write a for loop in Rust? | No | 0.896 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I used ChatGPT while designing the custom chunking strategy. I explained that the `campus_life` corpus contained short documents averaging about 317 characters and that the starter produced one chunk per document. ChatGPT suggested paragraph-aware chunking with a target of about 450 characters and no overlap. I used that approach because it preserved natural paragraph boundaries while only splitting the few longer posts.

**2.** I also used ChatGPT to review my acceptance criteria and retrieval results. I first wrote the criteria and reasons myself, then asked ChatGPT to check whether they were measurable and testable. Later, I provided the best retrieval distances for five in-corpus and five out-of-scope questions, and ChatGPT helped me confirm that the existing 0.6 cutoff sat safely between the two groups. I kept the cutoff at 0.6 rather than changing it.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---
# Unit 2

## Run Log — Before

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain complete thoughts and do not cut sentences in half | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answers contain the expected fact or phrase from the source | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

### Real output from the before run

Produced by `run_eval.py::main`, using retrieval from `store.py::search` and chunks from `chunker.py::split_documents`.

**Criterion 1 — Retrieved chunk contains the answer**

Question: What are the walk-in hours for the health centre?

Retrieved sources included `health_center.txt`.

```text
The walk-in hours for the health centre are 8am to 11am (health_center.txt).
```

**Criterion 2 — Every answer names a source**

Question: Does work-study income count against financial aid?

```text
No, work-study earnings do not count against your financial aid the way ordinary income does.

Source: admin_campus_jobs_and_financial_aid.txt
```

**Criterion 3 — Gate stops out-of-corpus questions**

Produced by `run_eval.py::check_out_of_scope`.

```text
What is the capital of Mongolia? — refused — best distance 0.825
How do I change the oil in a diesel engine? — refused — best distance 0.934
Who won the 1994 World Cup? — refused — best distance 0.886
What is the recommended dosage of ibuprofen for a headache? — refused — best distance 0.844
How do I write a for loop in Rust? — refused — best distance 0.896

Gate refused 5 of 5.
```

**Criterion 4 — Sampled chunks contain complete thoughts**

Produced by `chunker.py::split_documents`.

Example:

```text
BIOL 160 Cell Biology — assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

This chunk is a complete thought and does not cut a sentence in half.

**Criterion 5 — Answers contain the expected fact or phrase**

Question: How many hours can I book a study room for every week?

```text
You can book a maximum of two blocks of two hours per person per week (totaling up to four hours per person).

Source: study_group_rooms.txt
```

This contains the expected fact of four hours per person per week.

## Verdicts

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MET | All five questions retrieved at least one chunk containing the information needed to answer the question in all three runs, exceeding the target of 4 of 5. |
| 2 | Every answer names a source | MET | Every generated answer named at least one source document in all three runs, meeting the 5 of 5 target. |
| 3 | Gate stops out-of-corpus questions | MET | The relevance gate refused all five out-of-scope questions, exceeding the target of 4 of 5. |
| 4 | Sampled chunks contain complete thoughts and do not cut sentences in half | MET | All five sampled chunks were complete thoughts with no sentence cut off at either end, exceeding the target of 4 of 5. |
| 5 | Answers contain the expected fact or phrase from the source | MET | All five questions produced answers containing the expected fact from the source documents in all three runs, exceeding the target of 4 of 5. |

## Diagnoses

No criteria were missed in the before run, so there were no failures that required a pipeline-stage diagnosis.

However, the results suggest that some of my original targets were relatively safe. In particular, Criterion 1 required only 4 of 5 questions to retrieve a chunk containing the answer, but the system achieved 5 of 5 in all three runs. If I were setting a stricter version of this criterion, I would consider requiring 5 of 5.

I also noticed that retrieval sometimes returned several loosely related documents along with the correct source. For example, the health-centre question retrieved `health_center.txt`, but also retrieved dining and transit documents that were not needed to answer the question. This did not cause a failure, but it suggests that retrieval precision could be improved.

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

I reduced the retrieval `top-k` from 5 to 3.

**Why I picked it:**

In the before run, the correct source was consistently retrieved, but several unrelated documents were also being returned. For example, the health-centre question retrieved five documents even though only `health_center.txt` was needed, so I reduced top-k to make retrieval more focused.

### Run Log — After

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain complete thoughts and do not cut sentences in half | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answers contain the expected fact or phrase from the source | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**

Yes. Reducing top-k from 5 to 3 kept all five criteria at MET while returning fewer unnecessary documents for each question. The correct source was still retrieved for all five test questions, and all five out-of-scope questions were still refused.

The change also reduced model usage from 9,176 tokens in the before run to 6,399 tokens in the after run, a reduction of about 30%. This suggests that the system used less context while maintaining the same measured answer quality.

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
