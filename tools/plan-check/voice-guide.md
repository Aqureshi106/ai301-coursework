# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first contributions to other people's
projects, and I say so once, plainly, rather than performing either
expertise or apology. What I bring to a thread is a run: an
environment, the commands, and what came back. Anyone reading my
comment should be able to re-run it and check me, and should never
have to guess which part I observed and which part I am guessing at.
When I post a plan, I am also asking the people who maintain the code
to correct me before I build, so a plan comment is written to be
redirected, not to be admired.

## Rules I write by

### Rule: promise the artifact, not the outcome

I only promise things that are entirely mine to deliver: a report, a
run, a finding. Never a fix, a merge, or a date, because the schedule
is not mine and neither is the review.

- Wrong: "I'll have a fix up by tomorrow, this should be quick!"
- Right: "Next I'm reading the range-step code to see where the
  overflow happens; I'll post what I find before I open anything."

### Rule: name the version and the behavior

Every comment I post names the version I ran and the specific behavior
I saw, so it could not be pasted onto another issue unchanged. If I
cannot name them, I have not done the work yet.

- Wrong: "Confirmed, I get this bug too. Looks like a parser problem."
- Right: "Reproduced on 4.53.3 (Homebrew, macOS 15.5): the same input
  panics with `not a string`, same trace as in the report."

### Rule: separate what I saw from what I think

Observations and guesses go in different sentences, and the guess is
labelled as one. I would rather be visibly unsure than confidently
wrong in front of a maintainer.

- Wrong: "The cause is the debounce race in the search handler."
- Right: "The log shows the editor unmounting right after the empty
  result. I haven't traced the cause yet; the debounce path the
  reporter mentions looks like where I'd start."

### Rule: say it the way I would out loud

No stacked exclamation marks, no flattery about the project, no
generated-sounding padding around a thin point. If I would not say a
sentence to a person's face in a code review, it does not go in the
comment.

- Wrong: "Hello maintainers! Amazing project!! I would love the
  opportunity to contribute to this wonderful tool!!"
- Right: "Hi, first contribution here. I reproduced this on 1.11.7 and
  the report is below."

### Rule: follow the repo's AI rule before I post, not after

I check `CONTRIBUTING.md` and any AI policy before the comment goes up.
Where the project asks for disclosure, I disclose in the comment
itself, naming the tool and what it did; where it asks for comments in
my own words, I write them myself. I never disclose by implication.

- Wrong: (posting a polished report to a repo whose policy requires
  disclosing all AI use, and saying nothing about it)
- Right: "Per the AI usage policy: I used Claude Code to help organise
  this report and check my rubric. I ran every command here myself and
  I understand what I am reporting."

### Rule: state the approach as a proposal, with its soft spot

A plan comment commits me to an approach in front of the people who
own the code. I say what I intend to change, and I name the one thing
I am least sure of and how I will check it, so a maintainer can
redirect me before I build. A plan is not a finished fix and I do not
word it like one.

- Wrong: "The fix is to rewrite the tokenizer's separator matching.
  The Python-version difference is a red herring."
- Right: "My plan is to change how the tokenizer matches separators in
  `requestitems.py`. I'm not sure the Python-version difference is
  unrelated, since one report shows different results per version, so
  I'll test on 3.11 and 3.13 before settling on it."

### Rule: say where the change stops

Every plan comment names the file or area I will touch and what I will
leave alone, in a sentence. This lets a maintainer say "that's too
much" or "also fix X" before I have written anything, and it keeps a
first contribution small.

- Wrong: "I'll clean up the parser while I'm in there."
- Right: "I'll change only the push callback in `sync_controller.go`.
  I won't touch how push status is computed or the other views."

### Rule: answer a maintainer's direction, don't step around it

If a maintainer or another commenter already suggested a direction, my
comment says whether I am following it. If I plan something different,
I say so and give the evidence from my run. I never post a plan as if
the thread were empty when it is not, and I never copy a classmate's
plan ("same approach as above"); my plan is my own, built from my own
reproduction.

- Wrong: "Here's my plan: change the argument parser." (when a
  maintainer has already said it is intended behavior)
- Right: "A maintainer said the parser behavior is intended, so I'm
  not changing it. My run shows the error message is the problem, so
  I plan to improve that message instead."

### Rule: say how I will know it worked

My plan comment ends with the observable result I will check: the
repro steps I will re-run and what should change in the output. If I
cannot say what would show it worked, the plan is not ready to post.

- Wrong: "I'll test it thoroughly."
- Right: "I'll re-run my repro steps and expect the commit to change
  color right after the push, without leaving the view."

## Things I never post

- Timelines, ETAs, or "this should be easy".
- "+1", "same here", "any update?" with nothing attached.
- A cause stated as fact when I have not shown it.
- "Can I be assigned?" as the whole comment, with no evidence of work.
- A reproduction that leans on a classmate's: my proof comes from my
  machine, in my words, even on a shared issue.
- Enthusiasm standing in for evidence, which is what I reach for when I
  am tired and want to look like I contributed.
- A plan stated as settled fact: no unknown named, no way to check it.
- A plan that does not say where the change stops, or one that grows
  into a refactor on a first contribution.
- A PR date, or a promise that it will be merged. I can promise to
  open it, not when it lands.
- "Same approach as above" or any plan borrowed from a classmate's comment.
- A plan that ignores a direction a maintainer already gave in the thread.
