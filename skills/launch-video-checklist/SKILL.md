---
name: launch-video-checklist
description: A director's checklist for product launch videos made by a coding agent (HyperFrames, Remotion, or any HTML or React video). Use it before writing a launch film, promo, ad or explainer, and again before the final render, so the film looks directed instead of AI-made. It covers the concept, one visual style, reading time, stillness, cuts, music on the beat, sound on motion, loudness and on-screen claims, with ffmpeg commands that measure each one. Use it when someone asks for a launch video, a product video, a promo, or a check of a video already made.
---

# Launch video checklist

Coding agents can write a whole launch video as code. What usually goes wrong is not the code but the
direction: no idea behind the film, a different look in every scene, text that's gone before anyone
can read it, music that lands on nothing, and numbers on screen nobody checked.

These rules come from measuring 74 AI-made launch films that did well on X, and from the checks
TeaserKit runs on every film it makes. They work with any renderer.

Work through **Before you build** first, then **Before the final render**. Fix anything that fails
before rendering the final file.

## Before you build

### 1. One idea, chosen on purpose

Write three concepts in one line each: a hook (the first 2 seconds), the moment the film hits, and a
payoff. Score them on clarity, surprise and how well they show the real product. Build the best one.
"Show the features in order" is not a concept.

### 2. Use only what's true

Every claim, number, quote and logo on screen must come from the product's site, its repo, or the
person who asked for the video. Write the list down now (see check 9). No invented stats, users,
reviews or customer logos.

### 3. One visual style for the whole film

Pick one look and write it down before the first scene: background, one accent colour, one display
font, one way things enter. Every scene follows it. Use the brand's own colours and fonts when it
has them.

### 4. Plan to transform, not to cut

The best-performing films barely cut: about 2 hard cuts per 30 seconds, the first around 3.8 seconds
in. Change things in place instead: a card morphs into the next state, a number rolls, a headline
rewrites itself. Use cuts as punctuation, on the beat.

### 5. Length and shape

30 seconds is the most common length of the films that did well, and 15 seconds the second. Make
16:9 first; keep the headline and key detail in the middle third so a 9:16 cut keeps them. The
first frame should work as a thumbnail on its own.

### 6. Music first, then the cuts

Choose the song before the storyboard. Make scene lengths whole beats of the song, and put the
film's big moment on the song's drop. A real, human-made track beats a generated loop.

## Before the final render

Render a draft first, then run these on it.

### 7. Every line is readable

A line needs about **(number of letters ÷ 15) + 1 seconds** on screen, alone. Two lines that
appear one after another need at least 0.4 seconds between them. Count from the moment the line is
fully visible, not when it starts animating in.

### 8. Nothing freezes

Less than 10% of the film should be still, and nothing should hold still for more than about a
second (the end card is the exception). Find long holds:

```bash
ffmpeg -v info -i draft.mp4 -vf "freezedetect=n=0.001:d=1" -map 0:v -f null - 2>&1 | grep freeze_
```

Each `freeze_start` is a hold of over a second, and `freeze_duration` says how long (a hold that runs
to the end of the film has no duration line). Give it a slow push-in, a typing cursor, a small drift:
anything alive.

Look at the rhythm too, one frame per second:

```bash
ffmpeg -v error -i draft.mp4 -vf "fps=1,scale=384:-1,tile=6x5:padding=4" -frames:v 1 rhythm.jpg
```

Neighbouring cells that look the same mean a dead stretch.

### 9. Every number is real

List everything on screen that makes a claim (numbers, prices, "used by", quotes, logos) next to
where it came from: the site, the repo, or the brief. Anything with no source comes out. Example
data (made-up file names in a demo) is fine if the film doesn't present it as real.

### 10. Count the cuts

```bash
ffmpeg -v info -i draft.mp4 -vf "select='gt(scene,0.4)',showinfo" -f null - 2>&1 | grep -o "pts_time:[0-9.]*"
```

Each line is a hard cut. More than about 6 in 30 seconds reads as a slideshow. Check each one lands
on a beat of the song.

### 11. Sound lands on motion

Every press, pop, slam or number landing should have a sound, and the sound should start within 2
frames of the movement. Step through each one frame by frame. A sound that arrives late reads as a
mistake, even if no one can say why.

### 12. Loudness

Social feeds play at about -14 LUFS. Measure the integrated loudness:

```bash
ffmpeg -v info -i draft.mp4 -af ebur128=peak=true -f null - 2>&1 | grep -A2 "Integrated loudness"
```

If it's far from -14, adjust the music bed and effects, or normalise with
`ffmpeg -i in.mp4 -af loudnorm=I=-14:TP=-1:LRA=11 -c:v copy out.mp4`.

### 13. The ends

- The first frame is strong as a still, with no fade in from black.
- The end card shows the logo, one line and the URL, and holds long enough to read them.
- There is no black frame at any cut.

## When it passes

Render the final file at full quality (60 fps if the renderer allows), measure loudness once more
on that file, and ship it.

---

This checklist is free. [TeaserKit](https://teaserkit.com) is a paid director that runs all of it
for your agent automatically: it picks the concept and a house style, cuts the film to a human-made
song with timed sound, and measures every check above before the final render.
