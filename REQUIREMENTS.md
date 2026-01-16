# Supermarket Sweep - Question Rounds Requirements

## Overview
A SvelteKit web application for Supermarket Sweep question rounds, with separate contestant and host interfaces syncing in real-time via WebSocket.

---

## 1. Question Types

### 1.1 Hangman-Style
- Single clue provided upfront
- Blank spaces for each letter in the answer
- Letters appear one at a time with configurable delay (ms)
- Example: "Breakfast drink" → _ _ _ _ _ E (with O, R, A, N, G appearing sequentially)

### 1.2 Progressive Clues
- Answer spaces are blank initially
- Up to 3 clues revealed progressively (e.g., after 20s, 40s, 60s)
- Host controls when to reveal next clue
- Example: Clue 1: "Found in a garden" → Clue 2: "Orange vegetable" → Clue 3: "Bugs Bunny's favorite"

### 1.3 Fill in the Blank
- Incomplete sentence/phrase with blank to fill
- Single correct answer or multiple acceptable answers
- Example: "The price is _____" or "I'm a _____ banana"

### 1.4 Guess the Price
- Show 3 Products
- Contestants guess which is the cheapest

### 1.5 Future Types
- Extensible for additional question types

---

## 2. Game Flow

- **Questions per round:** 3 (default, configurable)
- **Time per question:** 2 minutes (default, configurable)
- **Contestant interaction:** Host enters responses on behalf of contestants (no direct web input from contestants)
- **Scoring:** Competitive format where each correct answer awards additional time for the shopping round
- **Multiple contestants:** Support for multiple competing players

---

## 3. Host Interface

### Responsibilities:
- View the answer(s) to current question (always visible)
- Advance to next question
- Display next clue (for Progressive Clues type)
- Mark which contestant(s) answered correctly
- Setup/manage questions in advance (CRUD operations)
- Monitor contestant responses and scores
- Control question visibility and timing
- Control Points of Contestants
- Trigger a sound to indicate if the contestant was correct or incorrect

### Features:
- Pre-loaded questions from PostgreSQL database
- Real-time contestant answer tracking
- Time/score updates sent to contestant display in real-time

---

## 4. Contestant Interface

### Display:
- Current question and clues (based on question type and host's reveal timing)
- Blank spaces/answer format appropriate to question type
- Timer showing remaining time for current question
- Current score/bonus time earned
- Real-time updates from host

### No Direct Input:
- Contestants do not enter answers via the web app
- Host controls all answer submission and correctness marking

---

## 5. Real-time Synchronization

- **Technology:** WebSocket (for live bi-directional sync)
- **Data synced:**
  - Question changes
  - Clue reveals
  - Correct answer marking
  - Timer updates
  - Score/time bonus updates
- **Reliability:** Handle reconnection gracefully

---

## 6. Data Persistence

### Database: PostgreSQL

### Core Entities:
1. **Questions**
   - ID, type (hangman, progressive, fill-blank, price-guess), content, answer(s), clues (if applicable), delay values, difficulty, category

2. **Rounds**
   - ID, created_at, questions[] (3 questions), host_id

3. **Contestants**
   - ID, name, assigned_round, correct_answers_count, bonus_time_earned

4. **Results** (optional)
   - ID, round_id, contestant_answers[], scores, timestamps

---

## 7. User Interface - High Level

### Contestant Page (`/questions/contestant`)
- Large, readable question display
- Clear answer format based on question type
- Prominent timer
- Score/bonus time tracker

### Host Page (`/questions/host`)
- Question answer(s) visible
- Control buttons (Next Question, Reveal Clue, etc.)
- Contestant list with answer tracking
- Question setup/management section
- Clue reveal timeline (for Progressive Clues)

---

## 8. Scope & Assumptions

- Single browser window per role (one host, multiple contestants on separate devices)
- Questions are pre-created by host before game starts
- No authentication/user accounts in MVP
- Game runs sequentially (one round at a time)

---

## 9. Future Enhancements

- Multiple simultaneous rounds
- User accounts and game history
- Image uploads for questions
- Sound effects/music
- Mobile-optimized contestant display

